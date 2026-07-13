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
![［統