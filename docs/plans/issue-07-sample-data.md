# Issue #7 実装計画：集計値の違いを確認できるテスト用サンプルを追加する

対象：[Issue #7](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/7) ／ 優先度 Low ／ 実施順序 7 番目（最後）

## ゴール

最小構成のサンプル TSV と期待結果をリポジトリに含め、(1) 利用者が「全体・非ログイン・OA の差は不具合ではない」と確認でき、(2) ワークブック改修時の回帰テストデータとして使える状態にする。

## 変更対象

- 新規 `samples/` フォルダ：サンプル TSV（2 か月分を推奨。「by Month」系の月別集計も検証できるため）
  - `samples/logReport_FileDownload_2025-01.tsv`
  - `samples/logReport_DetailView_2025-01.tsv`
  - `samples/logReport_FileDownload_2025-02.tsv`
  - `samples/logReport_DetailView_2025-02.tsv`
- 期待結果ドキュメント：`samples/README.md`
- `README.md`（サンプルの使い方への言及）

## サンプル TSV の構造要件（Power Query が依存する形式）

実際の定型レポートから preamble（先頭 4 行）とヘッダー行の正確な文言を転記して作成する。クエリが依存する構造は次のとおり：

- **共通**：UTF-8、タブ区切り、先頭 4 行はスキップされる前置き行、5 行目がヘッダー。
- **FileDownload**：8 列（`File Name` / `Registered Index Name` / `No. Of Times Downloaded` / `Non-Logged In User` / `Logged In User` / `Site License` / `Admin` / `Registrar`）。前半データの後に**空行 → マーカー行**（1 列目が `Open-Access No. Of File Downloads`）**→ 再度ヘッダー行 → OA データ**が続く。`f_FileDownload` は「マーカーの 1 行前（空行）まで」を取り、`f_FileDownloadOpenaccess` は「マーカーの次の行をヘッダーとしてそれ以降」を取るため、空行とマーカーの位置関係を正確に再現すること。
- **DetailView**：4 列（末尾 2 列が `View Count` / `Non-logged-in User`）。単一セクション。
- **ファイル名**：`logReport_FileDownload_` は 23 文字、`logReport_DetailView_` は 21 文字ちょうどであること（date 列は固定オフセットの `Text.Middle` で切り出されるため、命名を変えると年月が壊れる）。

## サンプルデータ（docs/04 の例を採用、2025-01 分）

| File Name | 全体 DL | Non-Logged In | Logged In | OA 側 | 意図 |
|---|---|---|---|---|---|
| file-a.pdf | 10 | 10 | 0 | 10 | `open_access`：全体と OA が一致 |
| file-b.pdf | 8 | 8 | 0 | なし | 公開日経過後の `open_date`：公開中でも OA 側に出ない |
| file-c.pdf | 4 | 0 | 4 | なし | ログイン利用のみ：Non-Logged In = 0 |
| file-d.pdf | 6 | 4 | 2 | なし | 混在：内訳の単純加算と総数の違い |

2025-02 分は数値を変えた同構成とし、月別集計の検証に使う。DetailView 側も 2〜3 アイテムの最小構成を用意する。

## 期待結果（samples/README.md に記載する内容）

- 各シートの期待値表：`FileDownload`（8 行）、`Filedownload (OA)`（file-a のみ×2 か月）、`FileDownload by Month`（月ごとの合計。例：2025-01 の No. Of Times Downloaded の合計 = 28）、`DetailVIew`／`DetailVIew by Month`。
- 確認ポイントの明文化：
  - file-b が OA 側に出ないこと（`open_date` の挙動）
  - 全体合計 28 ≠ 内訳合計（単純加算しない根拠の実例）
  - 元データ（行レベル）が保持されること
- 使い方：`samples/` フォルダの絶対パスを `Path` テーブル（B9）に貼り付けて「すべて更新」。

## 作業手順

1. 手元の実レポートから preamble・ヘッダーの正確な文言を確認し、サンプル TSV 4 ファイルを作成する（このリポジトリ上でテキストとして作成可能）。
2. Excel の `Path` を `samples/` に向けて「すべて更新」し、全シートの実際の出力を採取する。
3. 出力を基に `samples/README.md` の期待値表を確定する（期待値は手計算ではなく実出力で検証してから記載）。
4. README にサンプルの存在と使い方を 1 段落追記する。
5. Issue #5 の `OA Difference` シートが実装済みであれば、file-b／file-c／file-d が「OA側なし」になることも期待結果に含める。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| サンプル TSV がリポジトリに含まれる | 手順 1 |
| 更新後の期待結果が記載されている | 手順 3 |
| 両指標の違いを確認できる | file-c／file-d |
| `open_date` が OA 側に出ない例 | file-b |
| 元データが保持されることの確認 | 期待値表（行レベルシート） |

## 検証

- クリーンな環境（ワークブックを開き直し、`Path` を `samples/` に設定）で「すべて更新」し、全シートがエラーなく更新され、期待値表と一致することを確認。
- サンプルは実データを含まないダミーであることを確認（公開リポジトリのため）。
