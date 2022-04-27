---
title: "Reactで作成したアプリをfirebaseにデプロイするときに注意すること"
emoji: "🗿"
type: "tech"
topics: ["firebase", "nextjs", "react"]
published: true
---

# はじめに
この記事は、`react`, `react-router`で作ったアプリをFirebase Hostingを利用してデプロイしたときに自分がコケた場所を紹介するものです。何かの役に立つと幸いです。

## 環境
- react: ^17.0.2
- react-dom: ^17.0.2
- react-router: ^6.0.0-beta.0
- react-router-dom: ^6.0.0-beta.0

# Reactで階層を含んだURLを直接叩くと404になる；；
## 状況
react-router でルーティングしている
```jsx:App.tsx
<Routes>
	<Route path="/" element={<Home />} />
	<Route path="hoge/:piyoId" element={<Hoge />} />
	<Route path="*" element={<Navigate to="/" replace />} />
</Routes>
```

## 階層を含んだURLを直接アクセスするとエラーになる
![](https://storage.googleapis.com/zenn-user-upload/ea42dc3de8bd-20220121.jpg)

### 上手く行くケース
1. `http://my-app.com/`にアクセス、`<Home />`が表示される。
2. `<Home />`から`http://my-app.com/hoge/1`にアクセス、`<Hoge />`が表示される。

### 上手く行かないケース
- `<Home />`から`http://my-app.com/hoge/1`にアクセスしリロードする → `Page Not Found`
- 階層を含んだページでリロードする → `Page Not Found`
- `http://my-app.com/hoge/1`に直接アクセスする → `Page Not Found`

## 原因
- SPAなのでリソースがないため404になる?

## 解決策
firebaseのドキュメントを見ているとリライトというのを設定すると良いらしいので設定してみる。
リライト：複数のURLで同じコンテンツを表示するために使用するもの
参考 : [ホスティング動作を構成する  |  Firebase Documentation](https://firebase.google.com/docs/hosting/full-config?hl=ja#rewrites)
https://firebase.google.com/docs/hosting/full-config?hl=ja#rewrites


今回は存在しないファイルまたはディレクトリへのリクエストに対してindex.htmlを返したいので下記を設定する。
```diff json:firebase.json
{
  "hosting": {
    "public": "build",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
+    "rewrites": [
+      {
+        "source": "**",
+        "destination": "/index.html"
+      }
+    ]
  }
}
```

これでデプロイして`http://my-app.com/hoge/1`アクセスして確認して終わり〜！！
と思いきや…

## Uncaught SyntaxError: Unexpected token '<' エラーが出てる！！
![](https://storage.googleapis.com/zenn-user-upload/a9209212525b-20220121.jpg)

コンソールを見てみると何やらエラーが出てる…とりあえずjavascriptの読み込みのパスを見てみる
```HTML:index.html
<body>
	...
	<script src="./static/js/2.c291979f.chunk.js"></script>
	<script src="./static/js/main.ca9cdfff.chunk.js"></script>
</body>
```
`./static/js/2.c291979f.chunk.js`と相対パスになっているのが確認できる。
相対パスなので`http://my-app.com/hoge/`からの読み込みなのがわかる。

## 原因
検証の為にネットワークの欄を見てみる
![](https://storage.googleapis.com/zenn-user-upload/4f2a113f71dd-20220121.jpg)

やっぱり相対パスになっているのが原因で別のURLからソースを取得していた！！
読み込まれていたURL : `https://portfolio-abee2.web.app/works/static/css/main.a180d1c6.chunk.css`
期待していたURL : `https://portfolio-abee2.web.app/static/css/main.a180d1c6.chunk.css`

## 解決策
```diff json:package.json
{
  ...
  "version": "0.1.0",
-  "homepage": "./",
+  "homepage": "/",
```
`package.json`の`homepage`が`./`になっており相対パスになっているみたいだったので、`/`に変更してデプロイしてみる。
参考 : [Deployment | Create React App](https://create-react-app.dev/docs/deployment/#building-for-relative-paths)
https://create-react-app.dev/docs/deployment/#building-for-relative-paths


```HTML:index.html
<body>
	...
	<script src="/static/js/2.c291979f.chunk.js"></script>
	<script src="/static/js/main.ca9cdfff.chunk.js"></script>
</body>
```
正しく表示されるようになった！！よかった！！