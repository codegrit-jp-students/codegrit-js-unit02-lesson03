## AjaxとXMLHttpRequest

最近では、ページ全体を更新するのではなく、ページの一部分だけがアップデートされているサイトが大部分を増えています。例えばFacebookやTwitter、Instagramでは画面を下にスクロールしていくと新しい投稿情報のみを読み込んで表示します。こうしたことは2005年以前はまだ一般的ではありませんでしたが、2005年にGoogle Mapsがリリースされ、その中でAjaxという仕組みを用いてマップの一部分を更新されていたことで一躍脚光を浴びました。

### Ajax

Ajaxとは"Asynchronous JavaScript And XML"の頭文字を取ったものでJavascriptでの非同期通信を通してXML形式のデータを取得することを意味します。ただ、その後HTTPと似たように、非同期通信でデータを取得すること自体をAjaxと呼ぶようになり、現在ではXMLでは後述するJSON形式でデータを取得することが一般的となっています。

### XMLHttpReqeust(XHR)

Ajaxは元々XMLHttpRequest APIを利用して実装されました。XMLHttpRequestを利用したAjax通信は以下のような手順で実施されます。

1. クライアント側でXMLHttpRequestオブジェクトを作成する。
2. 作成したオブジェクトをHTTPリクエストに含めてサーバーに送信する。
3. サーバー側でXMLHttpRequestオブジェクトに要求されたデータをXMLまたはHTML形式で返します。
4. クライアント側で受け取った情報を処理する。

以下はXMLHttpReqestの例です。

```javascript
const endpoint = "http://localhost:3000" // サーバーのURL

const getUser = (userId) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest(); // ①
    const url = `${endpoint}/users/${userId}`; // ②
    xhr.onload = (e) => {
      if (xhr.status === 200) {
        resolve(xhr.response);
      } else {
        reject(JSON.parse(xhr.response));
      }
    }
    xhr.open("GET", url, true); // ③
    xhr.send();
  });
}

{
  getUser(1)
    .then((data) => {
      const p = document.createElement('p')
      p.innerHTML = data
      document.body.appendChild(p);
    })
    .catch((err) => {
      console.log(err.error);
    });
}
```

① XMLHttpRequstオブジェクトをまず作成しています。
② Userの情報をJSON形式で受け取るために、サーバーのURLを指定しています。
③ GETメソッドを利用してサーバーから情報を取得します。3つ目の引数の`true`は非同期でこのリクエストを処理することを意味します。XMLHttpRequestは同期的に行うことも出来ますがこれは非推奨です。

**XHRのサンプルコードはこちら**
[js-unit02-lesson03-sample01](https://github.com/codegrit-jp-students/js-unit02-lesson03-sample01)

## JSON

さて、上記のXMLHttpRequestの例ではJSONというデータ形式でデータをサーバーから取得しました。JSONとはJavaScript Object Notationの略です。JavaScriptと付いている通り、JavaScriptと親和性が高いため、Ajaxでのデータ通信でよく使われます。

JSONは以下のように記述します。

```json
"users": [
  {
    "id": 1,
    "name": "Steve Jobs",
    "bio": "Co-founer of Apple, Inc."
  },
  {
    "id": 2,
    "name": "Elon Musk",
    "bio": "CEO of Tesla Mortors."

  }
]
```

上記のように、JSONにはArray形式のデータも記述することが出来、各データは{"要素": "値"}という形式で記述します。

同じデータをJavascriptオブジェクトとして記述すると以下のようになります。

```javascript
const users = {
  users: [
    {
      id: 1,
      name: "Steve Jobs",
      bio: "Co-founer of Apple, Inc."
    },
    {
      id: 2,
      name: "Elon Musk",
      bio: "CEO of Tesla Mortors."
    }
  ]
}
```

ご覧の通り、ほとんど同じですね。Javascriptは以下のようにして簡単にJSONへと返還することが出来ます。

```javascript
let jsonData = JSON.stringify(users);
console.log(users); 
// => {"users":[{"id":1,"name":"Steve Jobs","bio":"Co-founer of Apple, Inc."},{"id":2,"name":"Elon Musk","bio":"CEO of Tesla Mortors."}]}
```

また、JSON形式のStringデータは以下のようにしてJavascriptオブジェクトへと返還出来ます。

```javascript
let userObj = JSON.parse(jsonData);
console.log(userObj);
/* 
{
  users: [
    {
      id: 1,
      name: "Steve Jobs",
      bio: "Co-founer of Apple, Inc."
    },
    {
      id: 2,
      name: "Elon Musk",
      bio: "CEO of Tesla Mortors."
    }
  ]
}
*/
```

そのため、サーバーからJSON形式のデータを受け取った場合、データをオブジェクトに変換することで簡単に各プロパティにアクセスすることが出来ます。

```javascript
let steve = userObj.users[0];
console.log(steve.name);
// => "Steve Jobs"
```
