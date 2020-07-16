# Hugo_公開までの作業

Hugo
https://gohugo.io/

以下の様に、マークダウンで記事を書いて、コマンドを実行すると公開用ファイルが生成されるのでそれをアップロードすると簡単に公開できる.

```
本体
 ├── 設定ファイル
 ├── マークダウンで書いた記事
 └── 公開用ファイル
 ```

## Hugo themes

Learn Theme for Hugo :: Documentation for Hugo Learn Theme
https://learn.netlify.app/en/

今回は Learn というテーマを使う。上記のサイトは、デモサイトにもなっている。

テーマの特長:
複数階層化
多言語対応
全文検索
mermaid.js によるフローチャート、シーケンス図、ガントチャート
「次へ」「前へ」リンク

## 想定の構成 
今回は、本体ごと GitHub にあげて、特定のディレクトリ配下だけを自動的に GitHub Pages として公開する。

公開URL:
`https://<アカウント>.github.io/<リポジトリ名>`

現状、無料の private リポジトリでは GitHub Pages が使えない

## Hugo のインストール
Scoop を使って Hugo をインストールする。
```
scoop install hugo 
```

## 公開までの作業

### プロジェクト作成
```
mkdir c:\hugo.site
cd C:\hugo.site\
```

```
hugo new site プロジェクト名
cd .\プロジェクト名\
```

### リポジトリ作成
```
git init
git remote add origin https://ユーザ名:パスワード@github.com/アカウント名/プロジェクト名.git
git pull
git remote add origin https://ユーザ名:パスワード@github.com/アカウント名/プロジェクト名.git
```

### コミット情報用にメールアドレスを設定
```
git config --local user.email メールアドレス
git pull origin master
git push --set-upstream origin master
git push
```

### テーマをSubmoduleで登録  
Git の Submodule 管理にしておくとテーマ作成者の変更を後々簡単に反映できるので、今回はそうした。

```
git submodule add https://github.com/matcornic/hugo-theme-learn.git themes/hugo-theme-learn
ls .\themes\hugo-theme-learn\
```

## 設定ファイルの書き換え  

### config.toml 

`webapp/` 直下にある `config.toml` を編集する

Before:
```
baseURL = "http://example.org/"
languageCode = "en-us"
title = "My New Hugo Site"
```

After:
```
baseURL = "https://アカウント名.github.io/プロジェクト名/"
publishDir = "docs"

author = "ユーザ名"
theme = "hugo-theme-learn"

DefaultContentLanguage = "ja"
languageCode = "ja-jp"
title = "タイトル"
subtitle = "サブタイトル"

logoHomeLink = "/プロジェクト名/"
```

## ローカルサーバーで動作確認  
hugo server -D

以下の URL をブラウザを開く
`http://localhost:1313/プロジェクト名`

## ページの作成
最初のページを作る。hugo new コマンドでページを追加する。

```
hugo new posts/my-first-post.md
```
-> `C:\hugo.site\プロジェクト名\content\posts\my-first-post.md created`

## ページのネスト  
次はページに階層を持たせる。

ページをネストするには、Nested Sections | Hugo にかかれているように、ディレクトリを作成して直下に _index.md ファイルを作る。

Nested Sections | Hugo
https://gohugo.io/content-management/sections/#nested-sections

以下のコマンドでディレクトリと _index.md ファイルを作成する。
```
hugo new posts/tech/_index.md
hugo new posts/tech/golang.md
```

`content` ディレクトリの構成は以下の通りになる。
```
content
└── posts
    ├── my-first-post.md
    └── tech
        ├── _index.md
        └── golang.md
```

左サイドバーのメニューを見ると、ページに階層構造ができているのが分かる。

※ページのネストに対応していないテーマもあるので注意

## 画像の配置  
ページに画像を表示させたい場合は、md ファイルと同じディレクトリに置く。
my-first-post.md ファイルに picture.png の画像を追記すると以下の通りになる。

```
---
title: "My First Post"
date: 2020-07-14T11:55:33+09:00
draft: true
---
# hello world
hello hugo
![画像](picture.png)
```

## ページの属性
`content/posts/my-first-post.md` ファイルの上のほうにあるハイフンで囲まれているブロックは、`Front-matter` といい、ページに属性を追加できる。

`title` はタイトル、date は作成日、draft は下書きを意味します。どんな属性があるかは Page Variables | Hugo を確認する。

Page Variables | Hugo 
https://gohugo.io/variables/page/

技術ドキュメントだと `title` とサイドメニューの並び順を調整できる `weight` だけで良い。

## サイト全体の設定  
ルートディレクトリ直下にある `config.toml` ファイルでサイト全体の設定がカスタマイズできる。

```
baseURL = "http://example.org/"
languageCode = "en-us"
title = "My New Hugo Site"
theme = "learn"
```

設定できる項目は `Configure Hugo | Hugo` から確認できる。

Configure Hugo | Hugo 
https://gohugo.io/getting-started/configuration/

本番環境に合わせて `baseURL` と `languageCode` は見直しておく。日本語でドキュメントを書く場合は、`languageCode` と `defaultContentLanguage` を `ja` にしておく。

`baseURL` の末尾にスラッシュがないと、`index.html` へリダイレクトされないので注意する。

## ビルド  
ビルドすると `html` や `css` が生成される。ビルドは `hugo` コマンドを実行するだけ。

```
hugo
```

```
Building sites …
                   | JA
-------------------+-----
  Pages            |  7
  Paginator pages  |  0
  Non-page files   |  0
  Static files     | 77
  Processed images |  0
  Aliases          |  0
  Sitemaps         |  1
  Cleaned          |  0

Total in 196 ms
```

デフォルトでは `public` ディレクトリ以下にコンテンツが生成される。出力先は `config.toml` ファイルや、`hugo` コマンドのオプションで変更することもできる。

## GitHub Pages にて公開 
まず、GitHub リポジトリの `Settings` タブから、GitHub Pages の Source を `master branch/docs folder` に変更する。

これで、本体を master ブランチに push すればその、`docs` ディレクトリの中身だけが GitHub Pages として公開されるようになる。