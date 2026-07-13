# JAIRO-cloud-log-parser 改善Issue案

対象リポジトリ：

```text
https://github.com/tzhaya/JAIRO-cloud-log-parser
```

以下は、そのままGitHub Issueとして起票できる粒度で整理した改善案です。

本ツールは、`No. Of Times Downloaded` と `Non-Logged In User` の両方を集計し、元データも保持する設計です。どちらを利用指標として採用するかは、各機関の運用環境、ログイン利用の状況、統計の目的によって異なるという考え方を前提とします。

---

## Issue 1: READMEに各統計値の意味と集計上の注意を追記する

### Title

```text
READMEにNo. Of Times DownloadedとOAダウンロードの集計条件を明記する
```

### Background

JAIRO Cloud（WEKO3）のファイルダウンロードレポートには、前半の `No. Of File Downloads` と、後半の `Open-Access No. Of File Downloads` が含まれています。

本ツールでは両者を別クエリ・別シートとして処理しており、この設計は妥当です。

一方、利用者から見ると、次の点が分かりにくい状態です。

- `Open-Access No. Of File Downloads` は全ダウンロードとは別の独立値ではない
- OA側は `accessrole = open_access` のファイルのみを対象とする
- 公開日を過ぎた `open_date` は、現在公開中でもOA側には含まれない
- OA側と全体側を加算すると二重計上になる

### Proposed change

READMEに「統計値の意味」セクションを追加し、次を明記する。

```markdown
## 統計値の意味

- No. Of Times Downloaded
  - 指定期間中に記録された全ファイルダウンロード
- Non-Logged In User
  - WEKO3にログインしていない状態で行われたダウンロード
  - 一般利用・匿名利用の傾向を確認するための指標として利用できる
- Open-Access No. Of File Downloads
  - 全ダウンロードのうち、ファイルの `accessrole` が `open_access` のもの
  - 現在公開中の全ファイルを意味するわけではない
  - 公開日経過後の `open_date` は含まれない
- No. Of Times Downloaded と Open-Access No. Of File Downloads は加算しない
  - OA側は全体側の部分集合である
```

### Acceptance criteria

- READMEに各統計の定義が記載されている
- OA側が全体側の部分集合であることが明記されている
- `open_date` がOA側に含まれないことが明記されている
- 両者を加算しない旨が記載されている
- `No. Of Times Downloaded` と `Non-Logged In User` の両方を保持する設計意図が説明されている

### Priority

High

---

## Issue 2: No. Of Times DownloadedとNon-Logged In Userの使い分けを明記する

### Title

```text
No. Of Times DownloadedとNon-Logged In Userの選択基準をREADMEに追加する
```

### Background

本ツールでは、`No. Of Times Downloaded` と `Non-Logged In User` の両方を集計し、元データも保持しています。

これは、どちらを利用指標として採用するかが、機関ごとの利用環境や集計目的によって異なるためです。

例：

- リポジトリ全体の利用量を把握したい場合
  - `No. Of Times Downloaded`
- ログインした管理者・登録者等の利用を除き、匿名利用の傾向を確認したい場合
  - `Non-Logged In User`
- 後から別の基準で再集計したい場合
  - 元データを使用

ただし、`Non-Logged In User` は「学外利用者」や「外部利用者」と完全には一致しません。

次の利用も含まれる可能性があります。

- 学内利用者がログインせずに利用した
- 図書館担当者がログアウト状態で確認した
- サイトライセンス対象ネットワークから匿名利用した

### Proposed change

READMEに次の説明を追加する。

```markdown
## 集計値の選択

本ツールでは、`No. Of Times Downloaded` と `Non-Logged In User` の両方を保持・集計します。

どちらを利用指標として採用するかは、機関の運用、ログイン利用の状況、統計の目的に応じて判断してください。

- `No. Of Times Downloaded`
  - WEKO3が記録した総ダウンロード数
  - リポジトリ全体の利用量を把握する場合に使用
- `Non-Logged In User`
  - WEKO3にログインしていない状態で行われたダウンロード数
  - 匿名利用の傾向を把握する場合に使用
  - 必ずしも学外利用者・外部利用者のみを意味しない

元データも保持しているため、機関独自の基準による再集計が可能です。
```

可能であれば、Excelの `Usage and Config` シートにも同様の説明を追加する。

### Acceptance criteria

- READMEに両指標の使い分けが記載されている
- どちらか一方を正解として固定しないことが明記されている
- `Non-Logged In User` が必ずしも学外利用者ではないことが説明されている
- 元データを保持する意義が記載されている
- Excel上でも選択基準を確認できる

### Priority

High

---

## Issue 3: FileDownload (OA) シート名または説明をより正確にする

### Title

```text
FileDownload (OA) がaccessrole=open_accessのみを対象とすることを明示する
```

### Background

`FileDownload (OA)` という名称は、現在誰でも利用可能なすべてのファイルのダウンロードを集計しているように解釈される可能性があります。

実際には、WEKO3レポートの `Open-Access No. Of File Downloads` セクションを取り込んでおり、対象は `accessrole = open_access` に限定されます。

### Proposed change

次のいずれかを実施する。

#### Option A: シート名を変更

```text
FileDownload (WEKO3 OA Setting)
```

または、

```text
FileDownload (accessrole=open_access)
```

#### Option B: 現在の名称を維持し、シート上部に注記

```text
このシートは、WEKO3でファイルのaccessroleがopen_accessに設定されている
ダウンロードのみを集計します。公開日経過後のopen_dateは含まれません。
```

### Acceptance criteria

- シート名またはシート上の注記で対象条件が明確になっている
- `open_date` が含まれないことが確認できる
- 全ダウンロードの部分集合であることが分かる

### Priority

High

---

## Issue 4: 集計列の単純加算を防ぐ注意書きを追加する

### Title

```text
ダウンロードレポートの内訳列を単純加算しない旨を明記する
```

### Background

WEKO3のレポートには次の列があります。

- Non-Logged In User
- Logged In User
- Site License
- Admin
- Registrar

これらはすべてが相互排他的な分類とは限りません。

同一ダウンロードが、ログイン利用とサイトライセンス利用など、複数の観点に該当する可能性があります。

そのため、内訳列を単純加算して総ダウンロード数を算出すると、二重計上になる可能性があります。

### Proposed change

READMEとExcelの説明欄に、次の注意を追加する。

```text
利用者区分の各列は、すべてが相互排他的とは限りません。
総ダウンロード数は各内訳列の合計ではなく、
No. Of Times Downloaded列を使用してください。

Non-Logged In Userは総数とは別の観点から利用状況を確認するための指標であり、
No. Of Times Downloadedに加算する値ではありません。
```

### Acceptance criteria

- READMEに単純加算不可の説明がある
- Excel上でも同様の注意が確認できる
- 総数に使用する列が明示されている
- `Non-Logged In User` が総数への加算対象ではないことが明記されている

### Priority

High

---

## Issue 5: 全ダウンロードとOAダウンロードの差分確認用クエリを追加する

### Title

```text
全ダウンロードとOAダウンロードの差分確認シートを追加する
```

### Background

利用者から、`No. Of File Downloads` と `Open-Access No. Of File Downloads` の数値差について問い合わせがあった場合、現在は両シートを手作業で突合する必要があります。

差分の主な原因として、次が考えられます。

- `accessrole = open_date`
- `accessrole = open_login`
- `accessrole = open_no`
- `accessrole` 未設定
- 旧データ移行時の設定差

### Proposed change

Power Queryで、次のキーを使って全体側とOA側を突合する。

```text
年月
+ File Name
+ Registered Index Name
```

出力列案：

| 列 | 内容 |
|---|---|
| Year-Month | 対象年月 |
| File Name | ファイル名 |
| Registered Index Name | 登録インデックス名 |
| All Downloads | 全ダウンロード数 |
| OA Downloads | OA側ダウンロード数 |
| Difference | All - OA |
| Status | OA一致 / OA側なし / 部分一致 |

判定例：

```text
All = OA    -> OA一致
OA = 0      -> OA側なし
All > OA>0  -> 部分一致
```

### Acceptance criteria

- 差分確認用のPower Queryまたはシートが追加されている
- 年月・ファイル名・登録インデックス名で突合される
- OA側に存在しない行を確認できる
- 差分値を確認できる

### Priority

Medium

---

## Issue 6: 統計値の目的別ガイドをUsage and Configシートに追加する

### Title

```text
Usage and Configシートに目的別の統計値選択ガイドを追加する
```

### Background

利用者が目的に応じて、どのシート・列を使うべきか判断しにくい状態です。

本ツールは複数の集計値と元データを保持しており、利用環境に応じて選択できる点が特徴です。この設計意図をExcel内でも明示する必要があります。

### Proposed change

`Usage and Config` シートに次の表を追加する。

| 目的 | 使用する値 | 注意点 |
|---|---|---|
| リポジトリ全体のダウンロード量 | No. Of Times Downloaded | ログイン利用等を含む総数 |
| 匿名利用の傾向 | Non-Logged In User | 必ずしも学外利用者のみではない |
| ログイン利用 | Logged In User | ログイン状態の利用 |
| 明示的OA設定ファイル | FileDownload (OA) | `accessrole = open_access` のみ |
| アイテム詳細閲覧 | Detailview | ファイルダウンロードとは別統計 |
| 機関独自の再集計 | 元データ | 用途に応じて任意の列を選択 |
| 現在公開中の全ファイル | 本レポートだけでは把握不可 | `open_date` 等の追加判定が必要 |

### Acceptance criteria

- Usage and Configシートに目的別ガイドがある
- `No. Of Times Downloaded` と `Non-Logged In User` の両方が選択肢として示されている
- どちらを使うかは利用環境・目的によることが明記されている
- 元データから再集計できることが説明されている
- 「現在公開中の全ファイル」は直接把握できないと明記されている

### Priority

High

---

## Issue 7: サンプルデータと期待結果を追加する

### Title

```text
集計値の違いを確認できるテスト用サンプルを追加する
```

### Background

現在の処理が正しくても、WEKO3側の統計仕様を知らない利用者には、全体側、非ログイン利用、OA側の差が不具合に見える場合があります。

### Proposed change

最小構成のサンプルTSVを追加する。

例：

- `file-a.pdf`
  - No. Of Times Downloaded: 10
  - Non-Logged In User: 10
  - OA: 10
  - `accessrole = open_access` 相当
- `file-b.pdf`
  - No. Of Times Downloaded: 8
  - Non-Logged In User: 8
  - OA側なし
  - 公開日経過後の `open_date` 相当
- `file-c.pdf`
  - No. Of Times Downloaded: 4
  - Non-Logged In User: 0
  - Logged In User: 4
- `file-d.pdf`
  - No. Of Times Downloaded: 6
  - Non-Logged In User: 4
  - Logged In User: 2

あわせて、期待する出力例をREADMEまたは `docs/` に記載する。

### Acceptance criteria

- サンプルTSVがリポジトリに含まれる
- Power Query更新後の期待結果が記載されている
- `No. Of Times Downloaded` と `Non-Logged In User` の違いを確認できる
- `open_date` がOA側に出ない例が含まれている
- 元データが保持されることを確認できる

### Priority

Low

---

## 推奨する実施順序

1. Issue 2: 指標の使い分けと設計意図の説明
2. Issue 6: Usage and Configへの目的別ガイド追加
3. Issue 3: OAシートの意味の明確化
4. Issue 4: 内訳列の単純加算防止
5. Issue 1: README全体の統計説明
6. Issue 5: 差分確認機能
7. Issue 7: サンプル・テストデータ

まず、既存の設計意図を明文化し、その後に差分確認機能やサンプルを追加する順序が適切です。
