# Issue #1 実装計画：README に統計値の意味と集計上の注意を追記する

対象：[Issue #1](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/1) ／ 優先度 High ／ 実施順序 5 番目（#2・#4 の後）

## ゴール

README を読めば、`No. Of Times Downloaded`・`Non-Logged In User`・`Open-Access No. Of File Downloads` の意味と関係（OA は全体の部分集合、加算禁止）が分かる状態にする。

## 変更対象

- `README.md` のみ（Excel 変更なし）

## 作業手順

1. README の「動作の概要」と「シートの説明」の間に「統計値の意味」セクションを新設し、docs/04 の Issue 1 提案文をベースに以下を記載する：
   - `No. Of Times Downloaded`：指定期間中に記録された全ファイルダウンロード
   - `Non-Logged In User`：非ログイン状態のダウンロード。匿名利用の傾向確認用
   - `Open-Access No. Of File Downloads`：全ダウンロードのうち `accessrole = open_access` のファイルのみ。「現在公開中の全ファイル」ではなく、公開日経過後の `open_date` は含まれない
   - OA 側は全体側の**部分集合**であり、両者を**加算しない**こと
   - 両指標を保持する設計意図（機関ごとに採用指標が異なるため。詳細は Issue #2 のセクションに委譲）
2. 根拠の詳細として `docs/01`〜`03` へのリンクを添える。
3. 「シートの説明」の `FileDownload (OA)` の項に「`accessrole = open_access` のみが対象」の一文を追加する（Issue #3 と整合させる）。

## 依存関係

- Issue #2 の「集計値の選択」セクションと同じ README に入るため、先に #2 を反映し、重複しないよう本 Issue では「定義と関係」に絞る。
- Issue #4 の「単純加算しない」注意と隣接配置し、相互参照する。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| 各統計の定義が記載 | 手順 1 |
| OA が部分集合であることの明記 | 手順 1 |
| `open_date` が OA 側に含まれない明記 | 手順 1 |
| 加算しない旨の記載 | 手順 1 |
| 両指標を保持する設計意図の説明 | 手順 1（#2 セクションへの参照） |

## 検証

- README をプレビューし、docs/01〜03 の記述と矛盾がないことを確認。
- Issue #2・#4 で追加したセクションと内容が重複・矛盾していないことを確認。
