---
layout: post
title: "git command list"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

{GIT_URL} = ""


### 基本的な流れ



### Add        Add file contents to the index

// 新規ファイルを追加
$ git add . 
// ファイル名を指定して追加
$ git add {ファイル名}
// リモートに新しくブランチを追加
$ git remote add {新しいブランチ名} {GIT_URL}

### Bisect     Find by binary search the change that introduced a bug
### Branch     List, create, or delete branches

// ブランチ一覧を表示
$ git branch 
// リモートを含めたブランチ一覧確認
$ git branch -a
// リモートのブランチ一覧確認
$ git branch -r
// ブランチの作成
$ git branch {ブランチ名}
// 現在のブランチの状況を確認
$ git show-branch
// ブランチの削除 (マージされていない場合、削除できない)
$ git branch -d {ブランチ名}
// ブランチの削除 (マージの状況関係なく削除)
$ git branch -D {ブランチ名}
// ブランチ名変更
$ git branch -m {古いブランチ名} {新しいブランチ名}
// リモートのブランチに切り替え
$ git branch {ブランチ名} origin/{ブランチ名}
$ git checkout {ブランチ名}
(git checkout -b {ブランチ名} origin/{ブランチ名})
// 

### Checkout   Checkout a branch or paths to the working tree

// ブランチの変更
$ git checkout {ブランチ名}
$ git checkout {ファイル名}
$ git checkout .

// Conflict時に活用
// パスをHEADと同じ状態にする。
$ git checkout --ours [{パス}]
$ git add {パス}
// パスをマージ指定したブランチを同じ状態にする。
$ git checkout --theirs [{パス}]
$ git add {パス}

### Clone      Clone a repository into a new directory

// レポジトリのダウンロード
git clone {Gitレポジトリ}

### Commit     Record changes to the repository

// ファイルをコミット
$ git commit -a -m "メッセージ"

### Diff       Show changes between commits, commit and working tree, etc
### Fetch      Download objects and refs from another repository

// リモートブランチと同期。(リモートから最新データを取得)
$ git fetch
(git fetch origin '+refs/heads/*:refs/remotes/origin/*')
($ git merge {リモート名}/{ブランチ名})


### Grep       Print lines matching a pattern
### Init       Create an empty git repository or reinitialize an existing one
### Log        Show commit logs

// ログを表示
$ git log 
// ログを変更したファイルも表示
$ git log --stat
// ログを一行表示する。
$ git log --oneline
// ログを検索する。
$ git log -S"{検索したいキーワード}"


### Merge      Join two or more development histories together

//  リモートブランチをマージする。(その前にfetchをしておく)
$ git merge {リモート名}/{ブランチ名}
// fast-forwardでマージする。[fast-forwardマージって何？](http://maeshima.hateblo.jp/entry/20100919/1284910080)
$ git merge {ブランチ名} --no-ff
// Mergeを取り消す。
$ git reset --hard HEAD


### Mv         Move or rename a file, a directory, or a symlink
### Pull       Fetch from and merge with another repository or a local branch
// 動きとしては'git fetch'と'git merge'をまとめて実行。
// 基本的には'pull'は使わずに'fetch'を使う。


### Push       Update remote refs along with associated objects

// 新しいブランチをリモートに追加 (その前にローカルにブランチを作成しておく)
$ git push -u origin {新しいブランチ名}
// リモートに送信する。
git push {リモート名} {ブランチ名}

-u, set-upstream


### Rebase     Forward-port local commits to the updated upstream head
### Reset      Reset current HEAD to the specified state

// 元に戻す。(マージを取りやめる)
$ git reset --hard HEAD

### Rm         Remove files from the working tree and from the index
### Show       Show various types of objects
### Status     Show the working tree status

$ git status

### Tag        Create, list, delete or verify a tag object signed with GPG



### Config 
// Gitの出力をカラー表示にする
git config --global color.ui auto


## 小技

// .gitattributes
*.pbxproj -crlf -diff -merge
*.xib -crlf -diff -merge

// 'git log'や'git status'で日本語が文字化けする場合、.bash_profileに下記を記述
$ vim ~/.bash_profile
export GIT_PAGER="less"
export LESSCHARSET=utf-8

// gitで見やすいログを出力
$ git config --global --add alias.ls 'log --oneline --stat'
$ git ls

// gitマージツールを起動
$ git mergetool

### 理解が進んだサイト

・git pullの詳細な挙動を追ってみる
http://d.hatena.ne.jp/hokaccha/20120404/1333507076
・Gitでコンフリクトした時のための備忘録
http://d.hatena.ne.jp/sinsoku/20110831/1314720280



