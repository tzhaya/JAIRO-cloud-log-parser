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

## リンク
-  定型レポートの出力方法の詳細やフォーマットや内容の解説は「JAIRO Cloud（WEKO3）基本マニュアル 統計機能」のうち以下をご覧ください。
   -  [1．定型レポートの出力](https://meatwiki.nii.ac.jp/confluence/pages/viewpage.action?pageId=63868532#id-%E7%B5%B1%E8%A8%88%E6%A9%9F%E8%83%BD-1%EF%BC%8E%E5%AE%9A%E5%9E%8B%E3%83%AC%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AE%E5%87%BA%E5%8A%9B)
   -  [定型レポートのタイプ一覧](https://meatwiki.nii.ac.jp/confluence/pages/viewpage.action?pageId=63868532#id-%E7%B5%B1%E8%A8%88%E6%A9%9F%E8%83%BD-%E5%AE%9A%E5%9E%8B%E3%83%AC%E3%83%9D%E3%83%BC%E3%83%88%E3%81%AE%E3%82%BF%E3%82%A4%E3%83%97%E4%B8%80%E8%A6%A7)

## 更新
 - 2025/01/04
   - 最初のリリース

## To Do
- 

## 作者
林 賢紀　（Takanori Hayashi）
