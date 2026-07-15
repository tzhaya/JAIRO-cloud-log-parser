# Issue #2 実装計画：No. Of Times Downloaded と Non-Logged In User の選択基準を README に追加する

対象：[Issue #2](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/2) ／ 優先度 High ／ 実施順序 1 番目

## ゴール

「どちらの指標を使うべきか」に単一の正解を置かず、機関の運用・目的に応じて選択するという本ツールの設計意図を README と Excel の両方で明文化する。

## 変更対象

- `README.md`
- Excel「Usage and Config」シート（Issue #6 の目的別ガイド表とまとめて実施可）

## 作業手順

1. README に「集計値の選択」セクションを新設し、docs/04 の Issue 2 提案文をそのまま採用する：
   - 両指標を保持・集計していること
   - `No. Of Times Downloaded` = 総ダウンロード数（リポジトリ全体の利用量向け）
   - `Non-Logged In User` = 非ログイン状態のダウンロード数（匿名利用の傾向向け）
   - **`Non-Logged In User` ≠ 学外・外部利用者**であること（学内の非ログイン利用、担当者のログアウト状態での確認、サイトライセンス網からの匿名利用を含み得る）
   - 元データ（`FileDownload`／`Detail View` シート）を保持しているため独自基準での再集計が可能なこと
2. Excel「Usage and Config」シートに同旨の短い説明を追加する。Issue #6 の目的別ガイド表に「注意点」列として吸収できるため、**Excel 側の作業は #6 と同時に 1 回で行う**。

## 依存関係

- 後続の #1・#4・#6 が本セクションを参照する。最初に実施する。
- Excel 側は #6 と同一リリースにまとめる。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| 両指標の使い分けが記載 | 手順 1 |
| 一方を正解として固定しない明記 | 手順 1（「機関の運用・目的に応じて判断」） |
| Non-Logged In User ≠ 学外利用者の説明 | 手順 1 |
| 元データ保持の意義 | 手順 1 |
| Excel 上でも選択基準を確認できる | 手順 2（#6 と合同） |

## 検証

- README プレビューで docs/03 の注意点と整合していることを確認。
- Excel 側は #6 の検証と合同で、「Usage and Config」シートに説明が表示されることを確認。
