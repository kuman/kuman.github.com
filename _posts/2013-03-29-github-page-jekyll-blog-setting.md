---
layout: post
title: "Jekyll and Github Pageでブログ作成"
description: ""
published: false
category: Tech
tags: [git,jekyll]
---

　いままでワードプレスでブログ書いていたけどJekyllに移行。ブログをCUIで運用管理できるのは嬉しい。日々の更新もvim+gitで完結なのが素敵。とりあえずJekyll Bootstrapを試してみところ、いきなりそれっぽいブログが構築された。が、一体何が起きたのか不明。。あとJekyllの仕様もよくわからない。ということでメモ。

## テンプレート内でレンダーを使用。

テーマ(theme)フォルダにあるテンプレートを編集する際、サイドバーだけ別ファイルで書いてレンダーで読み込ませたいと思ったけどデフォルトだとできないっぼい?ググってみたら良さげなプラグインが公開されていました。'{JEKYLL_ROOT}/_plugins/render.rb'ファイルを作成してリンク先にあるコードをコピペ。

・ <a href="http://www.testically.org/2012/02/03/a-concise-way-to-render-a-markdown-partial-in-jekyll/" target="_blank">A concise way to render a Markdown partial in Jekyll? </a>

あとはサーバー再起動して、テンプレートファイル内に下記のように記述すれば読み込んでくれます。下記の例は'{JEKYLL_ROOT}/_includes/shared/sidebar.md'を読み込む場合。

<pre>
&#123;% render shared/sidebar.md %&#125;
</pre>



## コードハイライト
http://tech.nitoyon.com/ja/blog/2012/12/25/jekyll-0-12-0/


## プラグインの作り方

http://gosyujin.github.io/2012/10/10/jekyll-liquid-plugin/

## Disqus

[http://disqus.com/](http://disqus.com/)!



## Livefyre


## intensedebate

## Facebook

// @todo
https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter
