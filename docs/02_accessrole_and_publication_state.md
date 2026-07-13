# `accessrole = open_access` の設定条件とファイル公開の設定・状態

## 1. 概要

WEKO3では、アイテム全体の公開状態と、各ファイルの公開条件を別々に管理しています。

そのため、次の状態は必ずしも一致しません。

- アイテムが公開されている
- アイテムのアクセス権メタデータが `open access` である
- 添付ファイルが現在誰でも利用できる
- 添付ファイルの `accessrole` が `open_access` である

## 2. ファイルの `accessrole`

ファイル情報には、公開条件を示す `accessrole` が保存されます。

代表的な値は次のとおりです。

| 値 | 意味 |
|---|---|
| `open_access` | 原則として誰でも利用可能 |
| `open_date` | 指定日以降に利用可能 |
| `open_login` | ログイン利用者等に限定 |
| `open_no` | 非公開 |
| `open_restricted` | 制限公開機能による条件付き公開 |
| 未設定・空欄 | 明示的な公開区分が保存されていない状態 |

`accessrole = open_access` は、ファイル登録・編集時に、そのファイルのアクセス設定として「オープンアクセス」が選択された場合に保存されます。

## 3. `open_access` の実際のアクセス条件

ファイルの `accessrole` が `open_access` の場合、原則として誰でも利用可能です。

ただし、ファイル情報に利用可能日が設定されている場合は、その日付が未来でないことも確認されます。

概念的には次の条件です。

```text
accessrole = open_access
かつ
利用可能日が未設定、または現在以前
```

## 4. `open_date` の動作

`open_date` は、指定日以降にファイルを公開する設定です。

例：

```json
{
  "accessrole": "open_date",
  "date": [
    {
      "dateType": "Available",
      "dateValue": "2019-10-17"
    }
  ]
}
```

2019年10月17日を過ぎると、一般利用者もファイルをダウンロードできます。

しかし、公開日を過ぎても、保存されている値は次のままです。

```text
accessrole = open_date
```

WEKO3は、公開日経過後に `open_date` を `open_access` へ自動変更しません。

## 5. 「現在公開中」と「open_access設定」の違い

次の二つは別の概念です。

### 現在公開中

アクセス制御を判定した結果、現在の利用者がファイルを取得できる状態です。

例：

- `open_access`
- 公開日を過ぎた `open_date`
- 権限を持つ利用者による `open_login`
- 管理者・登録者によるアクセス

### `accessrole = open_access`

ファイルメタデータに保存された設定値が、文字列として `open_access` である状態です。

OAダウンロード統計では、後者だけが使用されます。

## 6. アイテムのアクセス権との違い

アイテムには、書誌メタデータとして次のようなアクセス権情報が記録される場合があります。

```json
{
  "subitem_access_right": "open access",
  "subitem_access_right_uri": "http://purl.org/coar/access_right/c_abf2"
}
```

これは、アイテムのアクセス権を表すメタデータです。

一方、ファイルダウンロード制御と統計に使用されるのは、ファイル情報内の次の値です。

```json
{
  "accessrole": "open_date"
}
```

したがって、次のような組合せが成立します。

```text
アイテムのアクセス権 = open access
ファイルの accessrole = open_date
```

この場合、人間の理解では公開日経過後のファイルはオープンアクセスですが、WEKO3のOAダウンロード統計では対象外になります。

## 7. 実データの解釈例

確認した実データでは、次の値が設定されていました。

```json
{
  "accessrole": "open_date",
  "date": [
    {
      "dateType": "Available",
      "dateValue": "2019-10-17"
    }
  ]
}
```

この場合の解釈は次のとおりです。

- `accessrole` は未設定ではない
- `open_date` が明示的に設定されている
- 公開日はすでに経過している
- 現在は非ログイン利用者でもダウンロード可能
- ただし `accessrole` は `open_access` ではない
- OAダウンロード統計には含まれない

## 8. まとめ

正確には、次のように表現する必要があります。

> 公開日を過ぎた `open_date` ファイルは、現在誰でも利用できる状態であっても、`accessrole` は `open_date` のままであり、`open_access` には自動変更されない。

この仕様が、実際の公開状態とOA統計の対象範囲に差が生じる主な理由です。
