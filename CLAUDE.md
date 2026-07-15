# CLAUDE.md

このファイルは、このリポジトリで作業する Claude Code (claude.ai/code) に対するガイダンスを提供します。

## このリポジトリについて

単一成果物のツールです。Excel ワークブック（`JAIRO_Cloud_log_parser_YYYYMMDD.xlsx`）が Power Query を使って、JAIRO Cloud（WEKO3）リポジトリシステムから出力した月次の定型レポートを結合します。通常のソースコード・ビルドシステム・テストは存在せず、すべてのロジックは .xlsx 内に埋め込まれた Power Query の M クエリとして保持されています。README.md（日本語）はエンドユーザー向けドキュメントです。ライセンスは CC0 1.0 です。

リリースは、日付サフィックス付きのファイル名（例：`_20250104`）で新しいワークブックをコミットする形で行います。

## docs/ フォルダ

`docs/` には WEKO3 の統計仕様の調査結果と本ツールの改善計画がまとまっています。統計値の意味に関わる変更や README の更新を行う前に、関連するものを必ず参照してください：

- `01_weko3_access_statistics_logic.md` — WEKO3 ソースにおけるダウンロード数・閲覧数の記録・集計ロジック。重要な仕様：統計イベントの `accessrole` はアクセス時に再計算されず、ファイルの JSON に保存された値がそのまま使われる。
- `02_accessrole_and_publication_state.md` — `accessrole` の値（`open_access`、`open_date`、`open_login` など）とアイテム公開状態の関係。「OA ダウンロード」は `accessrole = open_access` のファイルのみが対象で、公開日を過ぎた `open_date` は含まれない。つまり OA 側は全体側の部分集合であり、両者を加算すると二重計上になる。
- `03_notes_on_using_weko3_statistical_reports.md` — レポート利用上の注意。`Non-Logged In User`／`Logged In User`／`Site License`／`Admin`／`Registrar` の各列は互いに排他的な分類ではないため、単純加算してはいけない。
- `04_jairo_cloud_log_parser_improvement_issues.md` — GitHub Issue として起票できる粒度の改善案 7 件（統計値の意味の README への明記、`No. Of Times Downloaded` と `Non-Logged In User` の使い分け、OA シートの名称・説明の正確化、単純加算防止の注意書き、全体と OA の差分確認クエリ、目的別ガイドの Usage and Config シートへの追加、サンプルデータと期待結果の整備）。本ツールの改善作業はまずここを起点にすること。
- `05_additional_topics_to_document.md` — 今後ドキュメント化を推奨するトピック（WEKO3 のバージョン・コミット ID の記録、用語集など）。
- `plans/` — GitHub Issue #1〜#7（docs/04 の Issue 番号と一致）の実装計画。着手時は `plans/README.md` の実施順序と共通ルールから読むこと。

## ワークブックの取り扱い

.xlsx はバイナリファイルであり、git の差分は読めません。機能変更は Excel の Power Query エディターで行う必要があり、このリポジトリ上のファイル編集では行えません。

Excel なしで M コードを**閲覧**する方法：.xlsx は zip アーカイブであり、Power Query の定義は `customXml/item9.xml`（対応する `itemProps*.xml` に `DataMashup` と記載のあるアイテム）に base64 エンコードされた DataMashup バイナリとして格納されています。base64 をデコードし、先頭 8 バイトのヘッダー（バイト 4〜7 は内側 zip のリトルエンディアン長）をスキップして unzip し、`Formulas/Section1.m` を読みます。

## データフロー（Power Query のアーキテクチャ）

入力：ユーザーがテーブル `Path`（列 `定型レポートの場所`、シート「Usage and Config」のセル B9）にフォルダのパスを貼り付けます。すべてのクエリはこのパスに対する `Folder.Files` から始まり、ファイル名のプレフィックスでフィルタします：

- `logReport_FileDownload_{YYYY}-{MM}.tsv` — **2 つのセクションが縦に連結**されています。前半が「No. Of File Downloads」、その後にマーカー行 `Open-Access No. Of File Downloads` が入り、後半が OA セクションです。`f_FileDownload` はマーカーより前の行を、`f_FileDownloadOpenaccess` はマーカーより後の行を取り出します。
- `logReport_DetailView_{YYYY}-{MM}.tsv` — 単一セクション、4 列。

TSV の形式：UTF-8（コードページ 65001）、タブ区切り、先頭 4 行の前置き行をスキップしてからヘッダーを昇格します。

ファイルごとの変換関数（`f_FileDownload`、`f_FileDownloadOpenaccess`、`f_DetailView`）が一致するすべてのファイルに適用され、`date` 列（例：`2024-12`）がファイル名から固定オフセットの `Text.Middle` で導出されます（FileDownload 系ファイル名はオフセット 23、DetailView 系はオフセット 21 — ファイル名の形式が変わると壊れます）。

出力クエリ → シートの対応：

- `FileDownload`、`Filedownload (OA)`、`DetailVIew`（大文字小文字の誤り込みで、これが実際のクエリ名です）— 全月の行レベル連結。
- `FileDownload by Month`、`DetailVIew by Month` — 同じデータを月ごとに `List.Sum` で集計したもの。

各 `f_*` 関数には、列名の導出に使うサンプルバイナリを束ねる `サンプル`／`パラメーター` クエリのペア（Power Query の「サンプル ファイル」パターン）があります。サンプルクエリはフォルダ内の最初に一致したファイル `{0}` を選ぶため、フォルダが空だったり内容が不一致だとすべてのクエリが壊れます。

クエリのステップ名は日本語です（日本語版 Excel の UI による自動生成）。Power Query エディターで編集する際もこの慣例を維持してください。
