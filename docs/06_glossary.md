# 用語集

JAIRO Cloud（WEKO3）の統計と本ツールを理解するための用語をまとめます。定義の根拠は [01_weko3_access_statistics_logic.md](01_weko3_access_statistics_logic.md)、[02_accessrole_and_publication_state.md](02_accessrole_and_publication_state.md)、[03_notes_on_using_weko3_statistical_reports.md](03_notes_on_using_weko3_statistical_reports.md) を参照してください。

本文書では、JAIRO Cloud で使用されているリポジトリシステムを「JAIRO Cloud（WEKO3）」と表記します。以降、利用者向けの説明では「JAIRO Cloud」と記載します。`accessrole` などの内部フィールド名、`logReport_FileDownload` などのファイル名、参照資料上の正式名称は、そのまま記載します。

**OA（Open Access／オープンアクセス）**
一般には、誰でも利用できる公開状態を指します。ただし、JAIRO Cloud（WEKO3）の「OAダウンロード統計」は、ファイルの `accessrole` が `open_access` であるダウンロードだけを対象とする技術的な区分です。現在誰でも利用可能なファイルすべてを表すものではありません。

> **最初に押さえるべき区別**
> - **アイテムのアクセス権**（書誌メタデータ）と**ファイルの `accessrole`**（各ファイルの公開条件）は別物です。両者は一致するとは限りません。
> - **現在取得可能（実際にダウンロードできる）**であることと、**`accessrole` が `open_access`** であることは別です。OA ダウンロード統計は後者だけを対象とします。

## アイテムとファイルの構成

- **アイテム（item）**
  - JAIRO Cloud に登録される 1 件のレコード。書誌メタデータと 0 個以上の添付ファイルを持ちます。
- **ファイル（file）**
  - アイテムに添付される個々のファイル。公開条件は各ファイルの `accessrole` として保存され、アイテム単位ではなくファイル単位で管理されます。
- **インデックス／登録インデックス（Registered Index）**
  - JAIRO Cloud がアイテムを分類するツリー状のカテゴリ。アイテムが登録されているインデックスの名称が「登録インデックス名（Registered Index Name）」で、ファイルダウンロードレポートの集計単位の 1 つです。
- **PID**
  - アイテムに割り当てられる永続識別子（persistent identifier）。アイテム詳細閲覧イベントに記録されます。

## 公開状態とアクセス制御

- **アイテム公開状態**
  - アイテム自体が公開されているかどうかを表す状態です。ファイルを実際に取得できるかは、ファイルの `accessrole` を主要な条件として、アイテム公開状態・利用可能日・ログイン状態・利用者の権限などを併せて判定します。
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
- **現在取得可能**
  - アクセス中の利用者が、その時点でファイルを取得できる状態です。取得できるかどうかは、ファイルの公開条件（`accessrole`）・利用可能日・ログイン状態・利用者の権限などによって異なります。「誰でも利用可能」とは限りません。
- **現在誰でも利用可能**
  - ログインや特別な権限なしで、現在ファイルを取得できる状態です。`open_access` に加え、公開日を過ぎた `open_date` も該当する場合があります。**`accessrole = open_access` とは一致しません**（OA 統計の対象範囲と実際の公開状態に差が生じる原因です）。

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

## 技術用語

- **TSV**
  - 項目をタブ文字で区切ったテキストファイル。定型レポートはこの形式で出力されます。
- **Power Query**
  - Excel で外部データの読み込み・変換・結合を行う機能。本ツールの集計ロジックはこの機能で実装されています。
- **クエリ**
  - Power Query に登録された一連のデータ処理。各シートの出力は、それぞれのクエリの結果です。
- **JSON**
  - JAIRO Cloud（WEKO3）がアイテムやファイルの設定情報を保持するデータ形式。ファイルの `accessrole` もここに保存されます。
- **行レベルデータ**
  - 月別合計にまとめる前の、ファイルやアイテムごとの明細データ。
- **絶対パス**
  - ドライブ名から始まる、フォルダの完全な場所（例：`C:\...\samples`）。

## 本ツール固有の用語

- **定型レポートの場所（Path テーブル／セル B9）**
  - 「Usage and Config」シートで、集計対象の TSV があるフォルダの絶対パスを指定するセル。すべてのクエリの入力元です。
- **by Month シート**
  - 複数月の明細を月単位にまとめ、同じ月の数値を合計したシートです（`FileDownload by Month`／`DetailVIew by Month`）。内部では Power Query の `List.Sum` を使用しています。
- **OA Difference シート／Status**
  - 全ダウンロード（`File Download`）と OA ダウンロード（`Filedownload (OA)`）を年月・File Name・Registered Index Name で突合し、差分を確認するシート。各行の `Status` は次のとおりです。
    - **OA一致**：全体と OA が一致
    - **OA側なし**：OA 側に対応する行が存在しない状態。`open_date`／`open_login`／`open_no`／`accessrole` 未設定などが原因として考えられますが、この Status だけでは原因を特定できません（確認には item JSON の `accessrole` 等を参照）
    - **部分一致**：OA 側が一部のみ
    - **要確認**：OA が全体を超えるなどの不整合（本来はありえない値）

## 関連

- 公開状態と `accessrole` の詳細 → [02_accessrole_and_publication_state.md](02_accessrole_and_publication_state.md)
- 統計の集計ロジック → [01_weko3_access_statistics_logic.md](01_weko3_access_statistics_logic.md)
- レポート利用時の注意 → [03_notes_on_using_weko3_statistical_reports.md](03_notes_on_using_weko3_statistical_reports.md)
