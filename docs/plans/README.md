# 実装計画（Issue #1〜#7）

[docs/04_jairo_cloud_log_parser_improvement_issues.md](../04_jairo_cloud_log_parser_improvement_issues.md) を元に起票した GitHub Issue #1〜#7 の実装計画です。

## 実施順序

docs/04 の「推奨する実施順序」に従います。まず設計意図の明文化（ドキュメント系）、その後に機能追加（Excel 系）です。

| 順序 | Issue | 計画 | 主な変更対象 | 優先度 |
|---|---|---|---|---|
| 1 | [#2](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/2) | [issue-02](issue-02-metric-selection-guide.md) | README（＋Excel） | High |
| 2 | [#6](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/6) | [issue-06](issue-06-purpose-guide-sheet.md) | Excel | High |
| 3 | [#3](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/3) | [issue-03](issue-03-oa-sheet-clarification.md) | Excel（＋README） | High |
| 4 | [#4](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/4) | [issue-04](issue-04-no-simple-addition-warning.md) | README＋Excel | High |
| 5 | [#1](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/1) | [issue-01](issue-01-readme-statistics-definitions.md) | README | High |
| 6 | [#5](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/5) | [issue-05](issue-05-oa-difference-query.md) | Excel（新規クエリ） | Medium |
| 7 | [#7](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/7) | [issue-07](issue-07-sample-data.md) | リポジトリ（サンプル TSV）＋docs | Low |

## 実装状況（2026-07-15 時点）

テキスト系（このリポジトリで直接編集できるもの）は実装・コミット済みです。Excel ワークブック（`.xlsx`）内のシート・Power Query の変更は Power Query エディターでの手作業が必要で、プログラムからは安全に自動編集できないため**未実施**です。各計画に貼り付け可能な文言・M コードを用意してあります。

| Issue | テキスト系 | Excel 手作業 | 状態 |
|---|---|---|---|
| #1 | README「統計値の意味」節を追加 | — | **完了** |
| #2 | README「集計値の選択」節を追加 | Usage and Config への説明（#6 に合流） | テキスト完了 / Excel 残 |
| #3 | README のOAシート説明に注記追加 | `Filedownload (OA)` シートへの注記 | テキスト完了 / Excel 残 |
| #4 | README「集計時の注意」節を追加 | Usage and Config への注意書き（#6 に合流） | テキスト完了 / Excel 残 |
| #5 | — | 新規クエリ `OA Difference` とシート追加＋README のシート説明 | Excel 残（M コードは計画に用意） |
| #6 | — | Usage and Config への目的別ガイド表 | Excel 残（表は計画に用意） |
| #7 | `samples/` の TSV 4 件＋`samples/README.md`＋README 追記 | サンプルで「すべて更新」して期待値を照合 | テキスト完了 / 照合は Excel 残 |

### Excel 手作業として残っている項目（貼り付け用素材は各計画に記載）
1. **Usage and Config シート**（#6、#2・#4 の Excel 側を包含）：目的別ガイド表＋指標選択・単純加算注意の補足。→ [issue-06](issue-06-purpose-guide-sheet.md)
2. **Filedownload (OA) シートの注記**（#3）：`accessrole = open_access` のみ・`open_date` 非包含・部分集合。→ [issue-03](issue-03-oa-sheet-clarification.md)
3. **OA Difference クエリ／シート新設**（#5）：M コード貼り付け → 新規シート出力 → README のシート説明追記。→ [issue-05](issue-05-oa-difference-query.md)
4. **サンプルでの期待値照合**（#7）：`samples/` を `Path` に設定して「すべて更新」し、[samples/README.md](../../samples/README.md) の手計算期待値と一致するか確認。差異があれば解消。
5. いずれの Excel 変更も、完了後に日付サフィックス付きの新ファイル名（例：`JAIRO_Cloud_log_parser_YYYYMMDD.xlsx`）で保存・コミットし、README のファイル名記載を更新する。

## 共通の前提と作業ルール

- **2 種類の変更対象がある**：リポジトリ内のテキストファイル（README、docs/、サンプル TSV）はこのリポジトリ上で直接編集・コミットできる。Excel ワークブック内の変更（シート・Power Query）は Excel の Power Query エディターでの手作業となる。各計画では投入する文言・M コードを完成形で示し、Excel 上の作業を「貼り付けるだけ」に近づける。
- **ワークブックのリリース手順**：Excel を変更した場合は、日付サフィックスの新ファイル名（例：`JAIRO_Cloud_log_parser_20260715.xlsx`）で保存してコミットし、README のファイル名記載も更新する。複数 Issue の Excel 変更は 1 回のリリースにまとめてよい。
- **検証**：Excel 変更後は、実データ（または Issue #7 のサンプル TSV）を置いたフォルダを `Path` テーブルに設定し「すべて更新」を実行、全シートがエラーなく更新されることと Acceptance criteria を確認する。
- **文言の正確性**：統計値の意味に関する記述は [docs/01](../01_weko3_access_statistics_logic.md)〜[docs/03](../03_notes_on_using_weko3_statistical_reports.md) と矛盾しないこと。特に「OA 側は全体側の部分集合」「`open_date` は OA 側に含まれない」「内訳列は相互排他ではない」の 3 点。
