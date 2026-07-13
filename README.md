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
- シート Detailview
  - 定型レポートのうち各月の「アイテムビュー」を結合したデータです。
  - 出力されたファイルのうち、logReport_DetailView_{年}-{月}.tsvを集計します。

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
