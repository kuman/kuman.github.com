---
layout: post
title: "objective c with oauth1.0"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

### Objective-CでOAuth2.0


### OAuth1.0の仕組み

### OAuth1.0と1.0aの違い。

OAuth1.0aのドキュメントに以下のように記されていますが、要は、1.0aで"session fixation attack"(セッション固定攻撃)への対応をしたとのことです。詳細はリビジョンをみてとのこと。[Changes to /spec/core/1.0a/oauth-core-1_0a.xml](https://code.google.com/p/oauth/source/diff?spec=svn1058&old=991&r=1058&format=unidiff&path=%2Fspec%2Fcore%2F1.0a%2Foauth-core-1_0a.xml)

```
Changes from OAuth Core 1.0
OAuth Core 1.0 Revision A was created to address a session fixation attack identified in the OAuth Core 1.0 specification as detailed in http://oauth.net/advisories/2009-1. A full inventory of changes is available in the specification repository.
```




### OAuth1.0を導入しているサービス

1.登録されたConsumerの確認。
2.ユーザーから承諾を得る。
3.1,2から得られる鍵を元に

### Facebook iOS SDK

1. Facebook iOS SDKをダウンロードする。
2. 1を導入先アプリの"Frameworks"フォルダにドラッグ。
3. Facebookアプリケーション登録して"FacebookAppleID"と"FacebookDisplayName"を取得する。
4. 3の"FacebookAppleID"と"FacebookDisplayName"を、アプリプロジェクトの



### Objective-CのOAuthライブラリ

・様々なOAuthCoreライブラリが提供されている。
https://bitbucket.org/atebits/oauthcore (updated at 2013/3/18)
MasterリポジトリはARC対応ではないが、別ブランチで他の方がARC対応版を作成してくれてるので、そちらを利用。

### Oauth1.0aの理解が深まるサイト

・Oauthの中身を解説してみるよ by ゆるよろ日記 (2010/5/6)
http://yuroyoro.hatenablog.com/entry/20100506/1273137673
視覚的で非常にわかりやすい。

・Flicker and OAuth by DirtyHandsPHP (2012/3/24)
http://www.dirtyhandsphp.com/2012/03/flickr-and-oauth.html
FlikerでOAuthについての説明(英語)

・Using OAuth with Desc.com by dev.desc.com
http://dev.desk.com/guides/oauth/
Desc.comのOAuthフロー。rubyのサンプルコードもあり。

・OAuth1.0a認証フロー by SalesForce
http://help.salesforce.com/HTViewHelpDoc?id=remoteaccess_oauth_1_flows.htm&language=ja
OAuth1.0aの説明。

### OAuth1.0aを採用しているサービス

・Twitter
・Flicker
・Desc.com
