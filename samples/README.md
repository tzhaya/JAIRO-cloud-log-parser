# サンプルデータと期待結果

このフォルダは、`JAIRO_Cloud_log_parser_YYYYMMDD.xlsx`（配布されているワークブック。現在は `JAIRO_Cloud_log_parser_20250104.xlsx`）の動作確認用の**合成（ダミー）データ**です。実データは含みません。統計値の違い（総数・非ログイン利用・OA の差）が不具合ではなく WEKO3 の統計仕様どおりであることを確認し、ワークブック改修時の回帰テストにも使えるようにするためのものです（Issue #7）。

## 使い方
1. Excel で配布されているワークブック（`JAIRO_Cloud_log_parser_YYYYMMDD.xlsx`）を開きます。
2. 「Usage and Config」シートの `定型レポートの場所`（セル B9）に、この `samples/` フォルダの絶対パスを貼り付けます。
3. 「データ」タブの「すべて更新」を押します。
4. 各シートの出力を、後述の「期待結果」表と照合します。

## ファイル構成
2 か月分（2025-01・2025-02）を用意しています。月別集計シートの検証のため 2 か月にしています。

- `logReport_FileDownload_2025-01.tsv` / `logReport_FileDownload_2025-02.tsv`
- `logReport_DetailView_2025-01.tsv` / `logReport_DetailView_2025-02.tsv`

### TSV の構造（Power Query が依存する形式）
- 文字コード UTF-8、タブ区切り。先頭 4 行は前置き行として読み飛ばされ、5 行目がヘッダーです（前置き行の**内容**は集計に影響しません。行数のみが重要です）。
- FileDownload は 8 列。前半「No. Of File Downloads」データの後に、**空行 → マーカー行 `Open-Access No. Of File Downloads` → ヘッダー行 → OA データ**が続きます。前半は `f_FileDownload`、後半（OA）は `f_FileDownloadOpenaccess` が処理します。
- DetailView は 4 列。末尾 2 列が `View Count` と `Non-logged-in User` です。単一セクションです。
- ファイル名の `logReport_FileDownload_`（23 文字）/ `logReport_DetailView_`（21 文字）は、`date` 列を固定オフセットで切り出すために正確な長さが必要です。

## 重要な注意（サンプルで検証できること／できないこと）
- **`accessrole` は TSV には存在しません。** 定型レポート TSV に `accessrole` 列はなく、OA セクションに含まれるか否かだけが出力されます。したがって「`file-b` が OA 側に出ない」ことは検証できますが、その原因が公開日経過後の `open_date` であることは、このサンプル**単体では証明できません**。本サンプルでは file-b を「`open_date` 相当（公開中だが OA 集計対象外）」として設計していますが、原因の確認には item の JSON（`accessrole` 等）を別途参照する必要があります（[docs/02](../docs/02_accessrole_and_publication_state.md) 参照）。
- **DetailView の先頭 2 列（`Item Id` / `Item Title`）はプレースホルダです。** 集計では `View Count` と `Non-logged-in User` のみを参照するため列名は結果に影響しませんが、実レポートの正確なヘッダー文言に置き換えることを推奨します。
- 期待値は下表のとおり TSV の仕様から**手計算で先に確定**しています。Excel の「すべて更新」後の実出力と照合し、差異があれば原因を解消してから正とします（実出力を無検証で正とはしません）。

## サンプルデータの設計意図
| ファイル | 総数 | Non-Logged In | Logged In | Site License | OA 側 | 意図 |
|---|---|---|---|---|---|---|
| file-a.pdf | 10 / 12 | 10 / 12 | 0 | 0 | あり | `open_access`：全体と OA が一致 |
| file-b.pdf | 8 / 5 | 8 / 5 | 0 | 0 | なし | `open_date` 相当：公開中でも OA 側に出ない |
| file-c.pdf | 4 / 3 | 0 | 4 / 3 | 0 | なし | ログイン利用のみ：Non-Logged In = 0 |
| file-d.pdf | 6 / 7 | 4 / 5 | 2 / 2 | 2 / 3 | なし | 内訳が重複：内訳合計 > 総数 |

（各セルは「2025-01 / 2025-02」の値）

**単純加算不可の実証:** file-d は 1 ダウンロードが複数区分に該当する状況を再現しています。2025-01 の file-d は総数 6 に対し、内訳合計は Non-Logged 4 + Logged 2 + Site License 2 = 8 で、**総数を超えます**。内訳列を合計しても総数にはなりません。

## 期待結果（手計算）

### シート File Download（前半・行レベル）
| date | File Name | No. Of Times Downloaded | Non-Logged In User | Logged In User | Site License | Admin | Registrar |
|---|---|---|---|---|---|---|---|
| 2025-01 | file-a.pdf | 10 | 10 | 0 | 0 | 0 | 0 |
| 2025-01 | file-b.pdf | 8 | 8 | 0 | 0 | 0 | 0 |
| 2025-01 | file-c.pdf | 4 | 0 | 4 | 0 | 0 | 0 |
| 2025-01 | file-d.pdf | 6 | 4 | 2 | 2 | 0 | 0 |
| 2025-02 | file-a.pdf | 12 | 12 | 0 | 0 | 0 | 0 |
| 2025-02 | file-b.pdf | 5 | 5 | 0 | 0 | 0 | 0 |
| 2025-02 | file-c.pdf | 3 | 0 | 3 | 0 | 0 | 0 |
| 2025-02 | file-d.pdf | 7 | 5 | 2 | 3 | 0 | 0 |

### シート Filedownload (OA)（後半・行レベル）
| date | File Name | No. Of Times Downloaded | Non-Logged In User | Logged In User | Site License | Admin | Registrar |
|---|---|---|---|---|---|---|---|
| 2025-01 | file-a.pdf | 10 | 10 | 0 | 0 | 0 | 0 |
| 2025-02 | file-a.pdf | 12 | 12 | 0 | 0 | 0 | 0 |

file-b・file-c・file-d は OA セクションに存在しないため、ここには現れません。

### シート FileDownload by Month（前半・月別集計）
| date | No. Of Times Downloaded の合計 | Non-Logged In User の合計 | Logged In User の合計 | Site License の合計 | Admin の合計 | Registrar の合計 |
|---|---|---|---|---|---|---|
| 2025-01 | 28 | 22 | 6 | 2 | 0 | 0 |
| 2025-02 | 27 | 22 | 5 | 3 | 0 | 0 |

2025-01 は総数 28 に対し、内訳列の合計は 22 + 6 + 2 = 30 で一致しません（単純加算不可の例）。

### シート Detail View（行レベル）
| date | Item Id | Item Title | View Count | Non-logged-in User |
|---|---|---|---|---|
| 2025-01 | item-0001 | Sample item 1 | 20 | 15 |
| 2025-01 | item-0002 | Sample item 2 | 9 | 9 |
| 2025-02 | item-0001 | Sample item 1 | 18 | 12 |
| 2025-02 | item-0002 | Sample item 2 | 7 | 7 |

### シート DetailVIew by Month（月別集計）
| date | View Count の合計 | Non-logged-in User の合計 |
|---|---|---|
| 2025-01 | 29 | 24 |
| 2025-02 | 25 | 19 |

### 参考：OA Difference シート（Issue #5 実装時）
`FileDownload` と `Filedownload (OA)` を年月＋ファイル名＋登録インデックス名で突合した場合の期待 Status。

| Year-Month | File Name | All Downloads | OA Downloads | Difference | Status |
|---|---|---|---|---|---|
| 2025-01 | file-a.pdf | 10 | 10 | 0 | OA一致 |
| 2025-01 | file-b.pdf | 8 | 0 | 8 | OA側なし |
| 2025-01 | file-c.pdf | 4 | 0 | 4 | OA側なし |
| 2025-01 | file-d.pdf | 6 | 0 | 6 | OA側なし |
| 2025-02 | file-a.pdf | 12 | 12 | 0 | OA一致 |
| 2025-02 | file-b.pdf | 5 | 0 | 5 | OA側なし |
| 2025-02 | file-c.pdf | 3 | 0 | 3 | OA側なし |
| 2025-02 | file-d.pdf | 7 | 0 | 7 | OA側なし |

## 確認ポイントのまとめ
- `file-b` が OA 側に出ないこと（`Filedownload (OA)` に file-b の行がない）。
- 月別集計の総数（28 / 27）が内訳列の合計と一致しないこと（単純加算をしない根拠）。
- `file-c` のように総数 > 0 でも Non-Logged In User が 0 の行があること（総数と非ログイン利用は別指標）。
- 行レベルシート（`File Download` / `Detail View`）に元データが保持され、機関独自の再集計が可能なこと。
