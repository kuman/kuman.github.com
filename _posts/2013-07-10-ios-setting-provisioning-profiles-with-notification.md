---
layout: post
title: "ios setting provisioning profiles with notification"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

#

iOS Adhoc及びReleaseの設定とNotification Serviceの設定についてメモ。
iOS Dev Centerの指示通りやれぱできるけど、全体の流れの再確認も含めて。

# iOS Dev Center

## Certificateを新規作成

1. iOS Dev Centerに移動。

"Certificates, Indentifiers & Profiles"
https://developer.apple.com/account/ios/

2. Certificatesをクリック。


3. プラスボタンを押しでCertificateを新規登録。

a. "What type of certificate do you need?"
 -> "Apple Store and Ad Hoc"を選択して"Continue"をクリック。

b. "About Creating a Certificate Signing Request (CSR)"
 -> CSRファイルの生成方法(英語表記)が記述されているので、指示に従いCSRを作成しておく。

c. "Generate your certificate."
 -> bで生成したCSRファイルをアップロードして"Generate"をクリック。

d. "Your certificate is ready."
 -> "Download"を押してCERファイルをインストールする。

e. CerファイルをKeychainAccessに登録。
 -> dでダウンロードしたCERファイルをダブルクリックしてKeychainAccessに登録する。
 -> bでCSR作成時に入力した"Common Name"でprivate/publicの２つのKeyが登録されていればOK。

## Apple IDを新規作成

1. iOS Dev Centerに移動。

2. "AppleIDs"をクリック。

3. プラスボタンを押しでAppleIDを新規登録。

a. "Registering an App ID"
 -> 



## Provisioning Profileを新規作成

1. iOS Dev Centerで"Provisioning profile"をクリック。

2. プラスボタンを押しでCertificateを新規登録。

3. "What type of provisioning profile do you need?"
 -> Adhocを選択して"Continue"をクリック。


# CSRの生成方法

a. Macのデフォルトでインストールされている"Keychain Access"アプリを起動する。
b. Keychain Accessのメニューから、"Keychain Access" -> "Certificate Assistant" -> "Request a Certificate from a Certificate Authority"を選択。
c. 




