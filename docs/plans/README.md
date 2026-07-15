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

## 共通の前提と作業ルール

- **2 種類の変更対象がある**：リポジトリ内のテキストファイル（README、docs/、サンプル TSV）はこのリポジトリ上で直接編集・コミットできる。Excel ワークブック内の変更（シート・Power Query）は Excel の Power Query エディターでの手作業となる。各計画では投入する文言・M コードを完成形で示し、Excel 上の作業を「貼り付けるだけ」に近づける。
- **ワークブックのリリース手順**：Excel を変更した場合は、日付サフィックスの新ファイル名（例：`JAIRO_Cloud_log_parser_20260715.xlsx`）で保存してコミットし、README のファイル名記載も更新する。複数 Issue の Excel 変更は 1 回のリリースにまとめてよい。
- **検証**：Excel 変更後は、実データ（または Issue #7 のサンプル TSV）を置いたフォルダを `Path` テーブルに設定し「すべて更新」を実行、全シートがエラーなく更新されることと Acceptance criteria を確認する。
- **文言の正確性**：統計値の意味に関する記述は [docs/01](../01_weko3_access_statistics_logic.md)〜[docs/03](../03_notes_on_using_weko3_statistical_reports.md) と矛盾しないこと。特に「OA 側は全体側の部分集合」「`open_date` は OA 側に含まれない」「内訳列は相互排他ではない」の 3 点。
