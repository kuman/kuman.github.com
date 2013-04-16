---
layout: post
title: "git command list"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

### Add        Add file contents to the index

// 新規ファイルを追加
$ git add . 
// ファイル名を指定して追加
$ git add {ファイル名}

### Bisect     Find by binary search the change that introduced a bug
### Branch     List, create, or delete branches

// ブランチ一覧を表示
$ git branch 
// ブランチの作成
$ git branch {ブランチ名}
// ブランチの確認
$ git show-branch
// ブランチの削除 (マージされていない場合、削除できない)
$ git branch -d {ブランチ名}
// ブランチの削除 (マージの状況関係なく削除)
$ git branch -D {ブランチ名}

### Checkout   Checkout a branch or paths to the working tree

// ブランチの変更
$ git checkout {ブランチ名}
$ git checkout {ファイル名}
$ git checkout .

### Clone      Clone a repository into a new directory

// レポジトリのダウンロード
git clone {Gitレポジトリ}

### Commit     Record changes to the repository
### Diff       Show changes between commits, commit and working tree, etc
### Fetch      Download objects and refs from another repository
### Grep       Print lines matching a pattern
### Init       Create an empty git repository or reinitialize an existing one
### Log        Show commit logs
### Merge      Join two or more development histories together
### Mv         Move or rename a file, a directory, or a symlink
### Pull       Fetch from and merge with another repository or a local branch
### Push       Update remote refs along with associated objects
### Rebase     Forward-port local commits to the updated upstream head
### Reset      Reset current HEAD to the specified state

// 元に戻す。(マージを取りやめる)
$ git reset --hard HEAD

### Rm         Remove files from the working tree and from the index
### Show       Show various types of objects
### Status     Show the working tree status

$ git status

### Tag        Create, list, delete or verify a tag object signed with GPG

