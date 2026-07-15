# 用語集

JAIRO Cloud（WEKO3）の統計と本ツールを理解するための用語をまとめます。定義の根拠は [01_weko3_access_statistics_logic.md](01_weko3_access_statistics_logic.md)、[02_accessrole_and_publication_state.md](02_accessrole_and_publication_state.md)、[03_notes_on_using_weko3_statistical_reports.md](03_notes_on_using_weko3_statistical_reports.md) を参照してください。

> **最初に押さえるべき区別**
> - **アイテムのアクセス権**（書誌メタデータ）と**ファイルの `accessrole`**（各ファイルの公開条件）は別物です。両者は一致するとは限りません。
> - **現在公開中（実際にダウンロードできる）**であることと、**`accessrole` が `open_access`** であることは別です。OA ダウンロード統計は後者だけを対象とします。

## アイテムとファイルの構成

- **アイテム（item）**
  - WEKO3 に登録される 1 件のレコード。書誌メタデータと 0 個以上の添付ファイルを持ちます。
- **ファイル（file）**
  - アイテムに添付される個々のファイル。公開条件は各ファイルの `accessrole` として保存され、アイテム単位ではなくファイル単位で管理されます。
- **インデックス／登録インデックス（Registered Index）**
  - WEKO3 がアイテムを分類するツリー状のカテゴリ。アイテムが登録されているインデックスの名称が「登録インデックス名（Registered Index Name）」で、ファイルダウンロードレポートの集計単位の 1 つです。
- **PID**
  - アイテムに割り当てられる永続識別子（persistent identifier）。アイテム詳細閲覧イベントに記録されます。

## 公開状態とアクセス制御

- **アイテム公開状態**
  - アイテム自体が公開されているかどうかの状態。ファイルが実際に取得できるかは、これとは別にファイルの `accessrole` で決まります。
- **アイテムのアクセス権（subitem_access_right）**
  - アイテムの書誌メタデータに記録されるアクセス権情報（例：`open access`、URI 付き）。人間向けの表示・記述であり、**ファイルダウンロード制御や OA 統計には使われません**。
- **`accessrole`**
  - 各ファイルに保存される公開条件を表す文字列。ファイルダウンロードの制御と統計は、この値を用います。統計イベントには、アクセス時に再判定した値ではなく、ファイルの JSON に保存された `accessrole` が**そのまま**記録されます。
- **`open_access`**
  - 原則として誰でも利用可能とする `accessrole`。OA ダウンロード統計の対象は、この値のファイルだけです。
- **`open_date`**
  - 指定日以降に公開する `accessrole`。**公開日を過ぎても値は `open_date` のまま**で、`open_access` へ自動変更されません。そのため、現在は誰でもダウンロードできても OA 統計には含まれません。
- **`open_login`**
  - ログイン利用者等に限定して公開する `accessrole`。
- **`open_no`**
  - 非公開の `accessrole`。
- **`open_restricted`**
  - 制限公開機能による条件付き公開の `accessrole`。
- **`accessrole` 未設定・空欄**
  - 明示的な公開区分が保存されていない状態。移行データ等で生じることがあります。
- **現在公開中**
  - アクセス制御を判定した結果、現在の利用者がファイルを取得できる状態。`open_access` に加え、公開日を過ぎた `open_date`、権限を持つ利用者による `open_login`、管理者・登録者によるアクセスなどを含みます。**`accessrole = open_access` とは一致しません。**

## 統計・レポートの用語

- **定型レポート／運用レポート**
  - 管理画面の［統計］＞［運用レポート］から出力する月ごとの統計。本ツールはこのうち「ファイルダウンロード（logReport_FileDownload）」と「アイテムビュー（logReport_DetailView）」を扱います。
- **No. Of File Downloads**
  - ファイルダウンロードレポートの**前半セクション**の見出し。指定期間に記録された全ファイルダウンロードを対象とします。
- **No. Of Times Downloaded（総ダウンロード数）**
  - 各行の総ダウンロード数の列。利用者区分の内訳ではなく、**総数はこの列**を使います。
- **Open-Access No. Of File Downloads（OAダウンロード）**
  - ファイルダウンロードレポートの**後半セクション**の見出し。全ダウンロードのうち `accessrole = open_access` のものだけを集計した値で、**全ダウンロードの部分集合**です。全体側と加算すると二重計上になります。
- **Detail View（アイテム詳細閲覧）**
  - アイテム詳細画面が表示された回数の統計。ファイルダウンロードとは別の統計で、イベントに `accessrole` は記録されません。
- **非ログイン利用（Non-Logged In User）**
  - ログインしていない状態で行われた利用（ダウンロード）。匿名利用の傾向を見る指標ですが、**必ずしも学外・外部利用者のみを意味しません**（学内の非ログイン利用、担当者のログアウト状態での確認、サイトライセンス網からの匿名利用なども含み得ます）。
- **ログイン利用（Logged In User）**
  - ログインした状態で行われた利用。
- **サイトライセンス（Site License）**
  - サイトライセンス対象ネットワーク（機関の IP 等）からの利用。ログイン利用と同時に該当することもあります。
- **管理者（Admin）／登録者（Registrar）**
  - 利用者区分の一種。管理者・登録者による利用。
- **利用者区分の内訳列**
  - `Non-Logged In User` / `Logged In User` / `Site License` / `Admin` / `Registrar`。**相互排他的ではない**ため、単純加算して総数を求めてはいけません（同一利用が複数区分に該当し得ます）。
- **二重クリック除外（短時間重複除外）**
  - 同一利用者による短時間の重複アクセスをまとめる処理。このため Web サーバの生ログ上のリクエスト件数と統計上の件数は一致しないことがあります。

## 本ツール固有の用語

- **定型レポートの場所（Path テーブル／セル B9）**
  - 「Usage and Config」シートで、集計対象の TSV があるフォルダの絶対パスを指定するセル。すべてのクエリの入力元です。
- **by Month シート**
  - `FileDownload by Month`／`DetailVIew by Month`。行レベルのデータを月ごとに `List.Sum` で集計したシートです。
- **OA Difference シート／Status**
  - 全ダウンロード（`File Download`）と OA ダウンロード（`Filedownload (OA)`）を年月・File Name・Registered Index Name で突合し、差分を確認するシート。各行の `Status` は次のとおりです。
    - **OA一致**：全体と OA が一致
    - **OA側なし**：OA 側に該当なし（例：公開日経過後の `open_date`）
    - **部分一致**：OA 側が一部のみ
    - **要確認**：OA が全体を超えるなどの不整合（本来はありえない値）

## 関連

- 公開状態と `accessrole` の詳細 → [02_accessrole_and_publication_state.md](02_accessrole_and_publication_state.md)
- 統計の集計ロジック → [01_weko3_access_statistics_logic.md](01_weko3_access_statistics_logic.md)
- レポート利用時の注意 → [03_notes_on_using_weko3_statistical_reports.md](03_notes_on_using_weko3_statistical_reports.md)
