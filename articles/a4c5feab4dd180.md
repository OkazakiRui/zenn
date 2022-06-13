---
title: '5分で簡単にscssの環境を作成する'
emoji: '🗿'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['javascript']
published: false
---

はじめまして！岡崎です！
今回は scss の環境を作っていきたいと思います！
最近 scss の勉強を始めた方の参考になればと思います！

# 前提

nodejs がインストールされていて、バージョンが 12.2.0 以上であること

## 確認方法

(mac の場合)
ターミナル.app を開いて下記コマンドを貼り付けて Enter を押す

```
node -v
```

![](/images/zenn/a4c5feab4dd180/t1.jpg)

# 環境の作り方

## ターミナルで作業したい場所に移動し、プロジェクトを作成するコマンドを実行する

今回は desktop に作りたいと思います！
下記コマンドをターミナルに貼り付けて実行してください！

```
cd ~/Desktop/ && npm create vite@latest
```

実行するとプロジェクト名を聞かれるので好きな名前にしましょう！（英数字のみ）
今回は`scss-sandbox`という名前にします！

```
Project name: scss-sandbox
```

次は framework を使用するか聞かれますが、今回は使用しないのでそのまま Enter で ok です。

```
Select a framework: vanilla
```

次は TypeScript を使用するか聞かれますが、そのまま Enter で ok です。

```
Select a variant: vanilla
```

実行し終わった場合下記の文字が表示されていると思います！

```
Scaffolding project in /Users/hogehoge/Desktop/scss-sandbox...

Done. Now run:

  cd scss-sandbox
  npm install
  npm run dev
```

## node_modules をインストールする

先ほど表示されたコマンドを 2 つ実行していきます。

`change dir` コマンドでファイルに移動します。

```
cd scss-sandbox
```

下記コマンドにて`node_modules`をインストールします。
`npm install` は `npm i` でも大丈夫です。

```
npm install
```

## scss と autoprefixer をインストールする

```
npm i -D sass autoprefixer
```

## 設定ファイルを作成する

`index.html`がある階層に`vite.config.js`ファイルを作成する
下記のコードを貼り付ける

```
import { defineConfig } from 'vite';

export default defineConfig({
  css: {
    postcss: {
      plugins: [require('autoprefixer')],
    },
  },
});
```

![](/images/zenn/a4c5feab4dd180/t2.jpg)

## css ファイルを scss ファイルに書き換える

`style.css`から`style.scss`に名前を書き換え、ファイルの中身を下記に上書きする

```
html {
  background: #38bdf8;
  h1 {
    text-align: center;
    color: white;
  }
}
```

`main.js`の中身を下記の 1 行だけにする

```
import './style.scss';
```

## `index.html` の内容を変更する

```
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <h1>初めての環境構築！</h1>
    <script type="module" src="/main.js"></script>
  </body>
</html>
```

## 実際にローカルで確認してみる

```
npm run dev
```

`http://localhost:3000/`にアクセスしてみる

正しく表示されてたら完成〜！！

[途中でつまづいた場合はこちらのコードを参考にしてください](https://github.com/study-okazaki/scss-sandbox)

## 今後開発をする場合

ターミナルで作業フォルダに移動(vscode 内蔵のターミナルの場合は不要)

```
cd ~/Desktop/scss-sandbox
```

### 開発用サーバーを立ち上げる

```
npm run dev
```

停止する場合は `Control` + `c`

### 本番用ファイルを作成する

dist フォルダの下にファイルサイズが小さくなった html, css, js ファイルが生成されます

```
npm run build
```

# おわりに

いかがだったでしょうか！
この記事が誰かの参考になれば幸いです！
