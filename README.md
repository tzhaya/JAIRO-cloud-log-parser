# JAIRO Cloud log parser

JAIRO Cloud（WEKO3）の管理画面の［統計］＞［運用レポート］から出力した月ごとの統計をPowerQueryで結合しシートに出力します。

動作にはMicrosoft ExcelとPower Queryを使用しています。

## ファイルの説明
- **JAIRO_Cloud_log_parser.xlsx**
  - Power Queryを使用して、任意のフォルダに置かれたJAIRO Cloud（WEKO3）の統計情報を結合します。
  - 定型レポートのうち「ファイルダウンロード（logReport_FileDownload）」と「アイテムビュー（logReport_DetailView）」を集計しています。

## 動作環境
- Power queryが動作するExcel

## 使い方
1. JAIRO Cloud（WEKO3）の管理画面の［統計］＞［運用レポート］をクリックします。
2. レポートの管理画面の［定型レポート］の［月別集計］で、［タイプ］は「すべて」、出力したい［年］、および［月］を選びます。
![［統計］＞［運用レポート］](https://meatwiki.nii.ac.jp/confluence/download/attachments/63868532/01.png)
3. 「ダウンロード」をクリックしてファイルをダウンロードします。
4. ダウンロードした定型レポートをお好きなフォルダに移動して、展開（解凍）します。
![image](https://github.com/user-attachments/assets/8039826b-fe31-428a-ba91-127b4cd4ac77)
5. 定型レポートのあるフォルダのパス（場所）をエクスプローラからコピーして、テーブル「定型レポートの場所」のグレーの場所（セルB9）に貼り付けます。
![image](https://github.com/user-attachments/assets/fead1634-5174-4ba3-9505-2490084ad79c)
6. 「データ」タブにある「すべて更新」を押します。各シートが自動で更新されます。
![image](https://github.com/user-attachments/assets/2bbb0c2d-68bd-426c-9a27-bf7e21a2c5f7)

## 動作の概要
1. 「定型レポートの場所」で指定された定型レポートのあるフォルダから、集計対象（「ファイルダウンロード」ならlogReport_FileDownload_{年}-{月}.tsv）を抽出します。
   - 「ファイルダウンロード」は、前半「No. Of File Downloads」と後半「Open-Access No. Of File Downloads」を別に集計します。
2. 集計対象のファイルを結合します。
3. 先頭の列に年月（例：2024-12）を書き込みます。

## 統計値の意味
各統計値は、それぞれ対象や観点が異なります。加算・比較の前に意味を確認してください。

- **No. Of Times Downloaded**
  - 指定期間中に記録された全ファイルダウンロード数です。
- **Non-Logged In User**
  - WEKO3にログインしていない状態で行われたダウンロード数です。匿名利用の傾向を確認するための指標として利用できます。
- **Open-Access No. Of File Downloads**
  - 全ダウンロードのうち、ファイルの `accessrole` が `open_access` に設定されているもののみを対象とした数です。
  - 「現在誰でも利用可能な全ファイル」を意味するわけではありません。公開日を過ぎた `open_date` のファイルは、現在公開中であってもこの数には含まれません。
  - この値（`Filedownload (OA)` シート）は、全ダウンロード（`File Download` シート）の**部分集合**です。両者を**加算しないでください**（二重計上になります）。

詳細は [WEKO3ソースにおけるアクセス統計集計ロジック](docs/01_weko3_access_statistics_logic.md) および [`accessrole = open_access` の設定条件とファイル公開の設定・状態](docs/02_accessrole_and_publication_state.md) を参照してください。

## 集計値の選択
本ツールでは `No. Of Times Downloaded` と `Non-Logged In User` の両方を保持・集計します。どちらを利用指標として採用するかは、機関の運用、ログイン利用の状況、統計の目的に応じて判断してください。どちらか一方が正解というものではありません。

- **`No. Of Times Downloaded`**
  - WEKO3が記録した総ダウンロード数です。リポジトリ全体の利用量を把握する場合に使用します。
- **`Non-Logged In User`**
  - WEKO3にログインしていない状態で行われたダウンロード数です。匿名利用の傾向を把握する場合に使用します。
  - 必ずしも学外利用者・外部利用者のみを意味しません。学内利用者の非ログイン利用、担当者のログアウト状態での確認、サイトライセンス対象ネットワークからの匿名利用なども含まれ得ます。

また、ダウンロードは `File Download` シート、アイテム詳細閲覧は `Detail View` シートに、それぞれ行レベルの元データを保持しています。これらは別々の統計であり、機関独自の基準で用途に応じて再集計できます。

## 集計時の注意（内訳列を単純加算しない）
ファイルダウンロードレポートの `Non-Logged In User` / `Logged In User` / `Site License` / `Admin` / `Registrar` の各列は、すべてが相互排他的な分類とは限りません。同一のダウンロードが、ログイン利用かつサイトライセンス対象ネットワークからの利用など、複数の観点に同時に該当する場合があります。

- 総ダウンロード数は各内訳列の合計ではなく、`No. Of Times Downloaded` 列を使用してください。
- `Non-Logged In User` は総数とは別の観点から利用状況を確認するための指標であり、`No. Of Times Downloaded` に加算する値ではありません。

詳細は [WEKO3統計レポート利用時の注意点](docs/03_notes_on_using_weko3_statistical_reports.md) を参照してください。

## シートの説明
- シート Usage and Config
  - 使い方を記載しています。また、集計対象のファイルがあるフォルダをこのシートに記載します。
- シート FileDownload by Month
  - 定型レポートのうち各月の「ファイルダウンロード」の前半「No. Of File Downloads」を結合し、月ごとに集計したデータです。
  - 出力されたファイルのうち、logReport_FileDownload_{年}-{月}.tsvを集計します。
- シート Detailview by Month
  - 定型レポートのうち各月の「アイテムビュー」を結合し、月ごとに集計したデータです。
  - 出力されたファイルのうち、logReport_DetailView_{年}-{月}.tsvを集計します。
- シート FileDownload
  - 定型レポートのうち各月の「ファイルダウンロード」の前半「No. Of File Downloads」を結合したデータです。
  - 出力されたファイルのうち、logReport_FileDownload_{年}-{月}.tsvを集計します。
- シート FileDownload (OA)
  - 定型レポートのうち、各月の「ファイルダウンロード」の後半「Open-Access No. Of File Downloads」を結合したデータです。
  - 出力されたファイルのうち、logReport_FileDownload_{年}-{月}.tsvを集計します。
  - このシートは、ファイルの `accessrole` が `open_access` に設定されているダウンロードのみを集計します。公開日を過ぎた `open_date` は含まれません。全ダウンロード（FileDownload シート）の部分集合です。
- シート Detailview
  - 定型レポートのうち各月の「アイテムビュー」を結合したデータです。
  - 出力されたファイルのうち、logReport_DetailView_{年}-{月}.tsvを集計します。

## サンプルデータ
- [samples/](samples/) に動作確認用の合成（ダミー）データと期待結果を用意しています。
  - `samples/` フォルダの絶対パスを「Usage and Config」シートのセル B9 に貼り付けて「すべて更新」すると、統計値の違い（総数・非ログイン利用・OA の差）が仕様どおりであることを確認できます。詳細と期待値は [samples/README.md](samples/README.md) を参照してください。

## ドキュメント
- [WEKO3ソースにおけるアクセス統計集計ロジック](docs/01_weko3_access_statistics_logic.md)
  - WEKO3がファイルダウンロードとアイテム詳細閲覧を記録・集計する処理を整理しています。
- [`accessrole = open_access` の設定条件とファイル公開の設定・状態](docs/02_accessrole_and_publication_state.md)
  - ファイルの公開設定、公開可能な状態、OA統計の対象条件の違いを説明しています。
- [WEKO3統計レポート利用時の注意点](docs/03_notes_on_using_weko3_statistical_reports.md)
  - 各列の合算、非ログイン利用、OAダウンロード数などを解釈する際の注意点をまとめています。
- [JAIRO-cloud-log-parser 改善Issue案](docs/04_jairo_cloud_log_parser_improvement_issues.md)
  - README、Excel、差分確認機能、テストデータ等の改善案をIssue単位で整理しています。
- [追加でまとめておくことを推奨する情報](docs/05_additional_topics_to_document.md)
  - バージョン記録、用語集、問い合わせ対応、移行データ、既知の制約などの追加論点を整理しています。

## リンク
- 定型レポートの出力方法の詳細やフォーマットや内容の解説は「JAIRO Cloud（WEKO3）基本マニュアル 統計機能」のうち以下をご覧ください。
  - [1．定型レポートの出力](https://jpcoar.org/support/jairo-cloud/manual/statistics/#m1)
  - [定型レポートのタイプ一覧](https://jpcoar.org/support/jairo-cloud/manual/statistics/#m1.1-list)

## 更新
- 2025/01/04
  - 最初のリリース

## To Do
-

## 作者
林 賢紀（Takanori Hayashi）
