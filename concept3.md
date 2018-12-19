## Fetch API

Fetch APIはXMLHttpRequestと同様にサーバーからデータを取得するための仕組みです。XMLHttpRequestに比べてリクエストの送信や、リスポンスの扱いが容易なため、XMLHttpRequestではなくFetchを利用するのが現在は主流となっています。

### Fetchのシンタックス

fetchファンクションは以下のように書きます。

```javascript
fetch(URL)

// または

fetch(URL, initオブジェクト);
```

fetchファンクションはPromiseオブジェクトを返します。

```javascript
fetch("https://example.com/users/1")
  .then((response) => {
    // fetch成功
  })
  .catch((error) => {
    // fetch失敗
  });
```

### JSONデータをGETメソッドを利用して取得する例

先ほどXMLHttpRequestの項目で書いたgetUserメソッドをfetchを使って書くと以下のようになります。

**Fetchのサンプルコードはこちら**
[js-unit02-lesson03-sample02](https://github.com/codegrit-jp-students/js-unit02-lesson03-sample02)


```javascript
const endpoint = "http://localhost:3000" // サーバーのURL

const getUser = async (userId) => {
  const url = `${endpoint}/users/${userId}`;
  const response = await fetch(url, { method: "get" })
  const json = response.json();
  if (response.status === 200) {
    return Promise.resolve(json);
  } else {
    return Promise.reject(json.error);
  }
}

{
  getUser(1)
    .then((data) => {
      console.log(data);
      const p = document.createElement('p')
      p.innerHTML = `名前: ${data.name}<br/>メールアドレス: ${data.email} `
      document.body.appendChild(p);
    })
    .catch((err) => {
      console.log(err);
    });
}

```

### Initオブジェクト

fetchファンクションの2つ目の引数にはinitオブジェクトを指定できます。

initオブジェクトは例えば、ログインリクエストを送信するときには以下のように書くことが出来ます。

```javascript
const initObj = {
  method: "POST",
  mode: 'cors', 
  cache: 'default',
  headers: { // HTTPリクエスト内のHeaderに含める情報を指定する
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ // HTTPリクエスト内のBodyに含める情報を指定する
    email: "example@codegrit.jp",
    rawPassword: "rawpassword"
  })
}
```

### Headersオブジェクト

initオブジェクト内のheadersの指定は、通常のJavascriptオブジェクトを利用する方法と、Headersオブジェクトを利用する2つの方法があります。

#### Headersオブジェクトの作成と操作

Headersオブジェクトは以下のようにして作成と操作が出来ます。

```javascript
let myHeaders1 = new Header(); // 空のHeaderオブジェクトの作成

myHeaders.append('Accept', 'application/json'); // Acceptキーと値の設定
myHeaders.get('Accept') // => 'application/json'; // キーを元に値を得る
myHeaders.set('Accept', 'text/html') // => 既に設定されている値を変更する
myHeaders.delete('Accept', 'application/json'); // => キーと値を消去する

let myHeaders2 = new Header({
  'Accept': 'application/json',
  'Content-Type': 'application/json'
}) // Headerオブジェクトの生成と同時にキーと値を設定する。
```

### Body mixin

Body mixinはFetch APIのリクエストとリスポンスのBody部分を読み取るためのプロパティとメソッド郡です。Fetch APIのリクエストとリスポンス双方にBody mixinは実装されています。(これはつまり、Body.json()というメソッドはresponse.json()、あるいはrequest.json()のように利用できることを意味します。プロパティの場合はrequest.bodyあるいはresponse.bodyのようにします。)

- Body.bodyプロパティ

Body部分のコンテンツを全て得ることが出来ます。

- Body.json()メソッド

Body部分をjsonとして読み取り、Promiseオブジェクトを返します。


### Fetchで得られるリスポンスオブジェクト

fetch成功時のPromiseオブジェクトにはリスポンスオブジェクトが含まれています。

リスポンスオブジェクトのプロパティは以下のようなものがあります。(全てはここに書かないので、必要な場合はMDNのドキュメントを参考にしてください。)

- response.headersプロパテイ

Headersオブジェクトです。

- response.statusプロパテイ

ステータスコードを返します。

- response.bodyプロパティ

リスポンスオブジェクトのBody部分を返します。

- response.json()メソッド

リスポンスオブジェクトのBody部分をJSONとして読み取り、Promiseオブジェクトを返します。

### POSTメソッドを利用する例

上述の通りPOSTメソッドはデータを保存したい場合に利用します。ここでは、ユーザー情報を保存することを想定してみます。

```javascript
const endpoint = "http://localhost:3000" // サーバーのURL

const logError = (error) => {
  console.log(error);
}

const status = (response) => {
  const json = response.json();
  if (response.status !== 200) {
    const error = new Error(json.error || json.message);
    return Promise.reject(error);
  }
  return Promise.resolve(json);
}

const createUser = async (userData) => {
  const url = `${endpoint}/users/`
  const res = await fetch(url, {
    method: 'post',
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(userData);
  })
  return res.then(status)
}

{
  let userData = {
    name: "Naoki Hanzawa",
    email: "hanzawa@tokyo-chuou-bank.jp",
    rawPassword: "naokihanzawa"
  }

  createUser(userData)
    .then((json) => {
      console.log(JSON.parse(json));
    })
    .catch(logError)
}

```

## CORS(HTTP access control)

Ajaxリクエストを利用するとサーバーから情報を取得することができますが、サーバーに誰でもアクセス出来てしまうと、セキュリティに問題が有ります。そのため、許可されたユーザーからのみアクセスが出来るようにする必要があります。これを実現するには次のレッスンで解説するHTTP authenticationと、CORSを利用します。CORSはcross-origin sharing standardの頭文字を取ったものです。

### same-origin policy

XMLHttpRequest、Fetch APIでは、デフォルトでsame-origin policyが適用されています。same-originとはどういうことかというと、プロトコル(httpあるいはhttps)、ホスト、ポートの全てが同じであることを指します。ホストとは例えばURLが https://www.codegrit.jp/ としたときの www.codegrit.jp の部分を指します。

例えば、 https://www.codegrit.jp/login というURLからサーバーにfetchリクエストを出すことを考えてみます。するとサーバーのURLごとに以下の結果が出ます。

| URL | 結果 | 理由 |
| https://www.codegrit.jp/api/authentication/ | 成功 | |
| https://api.codegrit.jp/authentication/ | 失敗 | ホストが異なるため |
| http://www.codegrit.jp/api/authentication/ | 失敗 | プロトコルが異なるため |
| http://www.codegrit.jp:3000/api/authentication/ | 失敗 | ポートが異なるため |

異なるorigin(cross-originと呼びます)からのリクエストを許可するには、サーバー側の設定が必要です。例えばExpress.jsというフレームワークをサーバー側で使っている場合であれば以下のように設定することで、 `https://api.codegrit.jp/authentication/` へのリクエストを許可することが出来ます。

```javascript
const express = require('express');

const app = express();

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "https://api.codegrit.jp");
  next();
});
...
```

CORSについてのより詳しい解説は、Javascriptコースの主題と離れるため割愛します。実際にサービスを公開する際にはよりCORSについて理解を深めておくことが、セキュリティの高いサービス運用に繋がりますので改めて勉強する必要があります。今の段階では、クライアント、サーバー間のやり取りのセキュリティを高めるためにCORSという仕組みがあるということを理解頂ければ大丈夫です。

## 更に学ぼう

### 記事で学ぶ

* [HTTPの概要 - Mozilla](https://developer.mozilla.org/ja/docs/Web/HTTP/Overview)
* [MIMEタイプ - Mozilla](https://developer.mozilla.org/ja/docs/Web/HTTP/Basics_of_HTTP/MIME_types)
* [Fetch 概説 - Mozilla](https://developer.mozilla.org/ja/docs/Web/API/Fetch_API/Using_Fetch)
* [JSON公式(日本語)](https://www.json.org/json-ja.html)
* [JSON - Mozilla](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/JSON)
* [オリジン間リソース共有 (CORS) - Mozilla](https://developer.mozilla.org/ja/docs/Web/HTTP/HTTP_access_control)