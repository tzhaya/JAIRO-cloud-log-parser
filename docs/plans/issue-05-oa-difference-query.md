# Issue #5 実装計画：全ダウンロードと OA ダウンロードの差分確認シートを追加する

対象：[Issue #5](https://github.com/tzhaya/JAIRO-cloud-log-parser/issues/5) ／ 優先度 Medium ／ 実施順序 6 番目

## ゴール

`FileDownload`（全体）と `Filedownload (OA)` を年月＋ファイル名＋登録インデックス名で突合し、差分と状態（OA一致／OA側なし／部分一致）を一覧できるシートを追加する。問い合わせ対応時の手作業突合をなくす。

## 変更対象

- Excel：新規 Power Query `OA Difference` と新規シート「OA Difference」
- `README.md`「シートの説明」への追記

## 設計

- 既存クエリ `FileDownload` と `#"Filedownload (OA)"` を参照する（フォルダの再読み込みはしない）。
- 同一キーが複数行になるケースに備え、突合前に両側を `Table.Group` で合計してから結合する。
- OA は全体の部分集合なので左外部結合（全体側基準）とし、OA 側にない行は 0 埋めする。

## 投入する M コード案

Power Query エディターで「空のクエリ」を作成し、名前を `OA Difference` にして詳細エディターに以下を貼り付ける：

```m
let
    全体集計 = Table.Group(FileDownload, {"date", "File Name", "Registered Index Name"},
        {{"All Downloads", each List.Sum([No. Of Times Downloaded]), Int64.Type}}),
    OA集計 = Table.Group(#"Filedownload (OA)", {"date", "File Name", "Registered Index Name"},
        {{"OA Downloads", each List.Sum([No. Of Times Downloaded]), Int64.Type}}),
    結合 = Table.NestedJoin(全体集計, {"date", "File Name", "Registered Index Name"},
        OA集計, {"date", "File Name", "Registered Index Name"}, "OA", JoinKind.LeftOuter),
    展開 = Table.ExpandTableColumn(結合, "OA", {"OA Downloads"}),
    OA補完 = Table.ReplaceValue(展開, null, 0, Replacer.ReplaceValue, {"OA Downloads"}),
    差分列 = Table.AddColumn(OA補完, "Difference",
        each [All Downloads] - [OA Downloads], Int64.Type),
    状態列 = Table.AddColumn(差分列, "Status",
        each
        // 正常系は All = OA / OA = 0 / All > OA > 0 の 3 種のみ。
        // OA > All や負数は本来ありえない不整合なので先に「要確認」に分類する。
        if [OA Downloads] > [All Downloads] or [All Downloads] < 0 or [OA Downloads] < 0 then "要確認"
        else if [All Downloads] = [OA Downloads] then "OA一致"
        else if [OA Downloads] = 0 then "OA側なし"
        else "部分一致", type text),
    列名変更 = Table.RenameColumns(状態列, {{"date", "Year-Month"}}),
    並べ替え = Table.Sort(列名変更, {{"Year-Month", Order.Ascending}, {"File Name", Order.Ascending}})
in
    並べ替え
```

## 作業手順

1. 上記クエリを追加し、「閉じて読み込む」で新規シート「OA Difference」のテーブルとして出力する。
2. シート上部に注記を入れる：「Difference として考えられる要因には、accessrole が open_date／open_login／open_no／未設定のファイル、旧データ移行時の設定差、元レポートの不整合などがあります。原因の特定には item の JSON の accessrole 等を確認してください（詳細は docs/02）。このクエリ自体は accessrole を取得しないため、原因を断定するものではありません。」
3. README「シートの説明」に「OA Difference」の項を追加する。
4. ワークブックを日付サフィックス付きの新ファイル名で保存する。

## 受け入れ基準との対応

| Acceptance criteria | 対応 |
|---|---|
| 差分確認用クエリ／シートの追加 | 手順 1 |
| 年月・ファイル名・登録インデックス名での突合 | M コードの結合キー |
| OA 側に存在しない行の確認 | Status =「OA側なし」 |
| 差分値の確認 | Difference 列 |

## 検証

- 実データ（複数月分）で「すべて更新」し、`OA Difference` の `All Downloads` 合計が `FileDownload by Month` の月合計と一致することを確認。
- Status の値が仕様どおり（All=OA→OA一致、OA=0→OA側なし、All>OA>0→部分一致、それ以外→要確認）に出ることを目視確認。
- 異常ケース（`OA > All` や負数）を意図的に含むデータで、「要確認」に分類され「部分一致」に紛れ込まないことを確認。
- Issue #7 のサンプル TSV（`samples/`）で、`open_date` 相当の file-b が「OA側なし」になることを確認。
