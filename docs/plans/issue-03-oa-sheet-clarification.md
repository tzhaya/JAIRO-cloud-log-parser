# Issue #3 実装計画：FileDownload (OA) が accessrole=open_access のみを対象とすることを明示する

対象：[Issue #3](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/3) ／ 優先度 High ／ 実施順序 3 番目

## ゴール

`Filedownload (OA)` シートが「現在誰でも利用可能な全ファイル」ではなく「`accessrole = open_access` のファイル」だけを集計していることを、シートを開いた利用者が誤解なく理解できる状態にする。

## 方針の決定：Option B（注記方式）を推奨

docs/04 は「シート名変更（Option A）」と「注記追加（Option B）」の二択を示している。**Option B を推奨**する。理由：

- シート名 `Filedownload (OA)` は Power Query のクエリ名・接続名・README の記載と紐づいており、改名するとクエリ名（`#"Filedownload (OA)"`）と接続の表示名も追随修正が必要になり、変更範囲が広がる。
- 注記であれば読み手への情報量はむしろ多く、`open_date` 非包含まで説明できる。

最終判断は起票者（リポジトリオーナー）に委ねる。Option A を選ぶ場合はシート名・クエリ名・接続名・README・Usage and Config の 5 箇所を同時に改名すること。

## 変更対象

- Excel「Filedownload (OA)」シート（注記の追加）
- `README.md`「シートの説明」（Issue #1 と合同）

## 作業手順（Option B）

1. Excel で「Filedownload (OA)」シートのテーブル上部（1〜3 行目）に行を挿入し、次の注記を記載する：

   ```text
   このシートは、WEKO3でファイルのaccessroleがopen_accessに設定されている
   ダウンロードのみを集計します。公開日経過後のopen_dateは含まれません。
   全ダウンロード（FileDownloadシート）の部分集合です。
   ```

   ※行挿入でクエリテーブルの出力先セルがずれるため、挿入後に「すべて更新」してテーブル位置が崩れないことを確認する。テーブルの上に空き行がない場合は、テキストボックスでの注記でも受け入れ基準を満たせる。
2. README「シートの説明」の該当項に同旨の一文を追加する（Issue #1 の手順 3 と同一作業）。
3. ワークブックを日付サフィックス付きの新ファイル名で保存する（他 Issue の Excel 変更と同一リリース可）。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| シート名または注記で対象条件が明確 | 手順 1 |
| `open_date` 非包含が確認できる | 手順 1 の注記 2 文目 |
| 全ダウンロードの部分集合であることが分かる | 手順 1 の注記 3 文目 |

## 検証

- 「すべて更新」後も注記とテーブルが重ならず表示されることを確認。
- README とシート注記の文言が一致していることを確認。
