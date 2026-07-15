# Issue #4 実装計画：内訳列を単純加算しない旨を明記する

対象：[Issue #4](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/4) ／ 優先度 High ／ 実施順序 4 番目

## ゴール

`Non-Logged In User`／`Logged In User`／`Site License`／`Admin`／`Registrar` の内訳列が相互排他でないため合計してはならず、総数には `No. Of Times Downloaded` を使う——という注意を README と Excel の両方に置く。

## 変更対象

- `README.md`
- Excel「Usage and Config」シート（Issue #6 の表と同時に作業）

## 作業手順

1. README の「統計値の意味」セクション（Issue #1 で新設）内、または直後に「集計時の注意」として docs/04 の提案文を記載する：

   ```text
   利用者区分の各列は、すべてが相互排他的とは限りません。
   総ダウンロード数は各内訳列の合計ではなく、
   No. Of Times Downloaded列を使用してください。

   Non-Logged In Userは総数とは別の観点から利用状況を確認するための指標であり、
   No. Of Times Downloadedに加算する値ではありません。
   ```

   同一ダウンロードが複数区分（例：ログイン利用かつサイトライセンス網）に該当し得る、という理由も一文添える（docs/03 §2 準拠）。
2. Excel「Usage and Config」シートに同じ注意書きを追加する。Issue #6 の目的別ガイド表の直下に配置し、**#6 と同一の Excel 作業で実施**する。

## 依存関係

- README の配置先は Issue #1 のセクション構成に依存する。#1 より先に着手する場合は独立セクションとして追加し、#1 実施時に統合する。
- Excel 側は #6 と同一リリース。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| README に単純加算不可の説明 | 手順 1 |
| Excel 上でも同様の注意 | 手順 2 |
| 総数に使用する列の明示 | 手順 1（`No. Of Times Downloaded`） |
| Non-Logged In User が加算対象でない明記 | 手順 1 の後段 |

## 検証

- README・Excel 双方に注意書きがあり、文言が一致していることを確認。
- docs/03「レポート内の列を単純加算しない」と矛盾しないことを確認。
