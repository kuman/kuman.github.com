---
layout: post
title: "Objective-Cのメンバー変数と@property,@synthesizeの記述法"
description: ""
category: Tech
tags: [objective-c]
---
{% include JB/setup %}

最近まで訳ってLegacy Objective-Cで開発してて、今月からようやくModern Objective-Cに着手。なにやら記述法が簡単になったみたいだけどだいぶ変わっていて困惑したので自分用のメモ。

### Objective-Cのメンバー変数と@property,@synthesize、要するに・・

こういうことかな。かなり省略できる。全てがこの記述という訳ではないけど、基本的にはこれで問題無さそう。@propertyを宣言すればメンバー変数の宣言や@synthesizeもコンパイル時に自動的に生成してくれるみたい。今までの書き方だと宣言が多くてかったるかったのでこれは嬉しい。@propertyのオプションも省略可 (デフォルトはstrong)

![Modern Objective-cのプロパティ記述](http://img.kuman.asia/files/modern-objective-c-property.jpg)

### @property宣言の確認。

@propertyだけでメンバー変数を宣言してくれてるか確認してみる。ついでにセッター、ゲッター周りも確認。それにしても代入方式が_message=, self.message=,[self setMessage]と3通りもあるのはややこしいけど、結果としてやっていることは同じ。使い分けとしては_deallocと_initはメンバー変数を直接参照で_message、それ以外はプロパティself.messageを使用というルールで良さそう。

![Modern Objective-cのプロパティ記述の確認](http://img.kuman.asia/files/modern-objective-c-property-check.jpg)

### 参考サイト

参考にさせて頂いたサイトと、その参考記事を一部抜粋。公式ドキュメント以下、時系列順。


* [Objective-Cプログラミング言語 - プロパティの宣と実装](https://developer.apple.com/jp/devcenter/ios/library/documentation/ObjC.pdf) by Apple

<pre>歴史的には、インターフェイスにはクラスのインスタンス変数宣言が必要でした。（中略）インスタンス変数は実装詳細であり、通常、クラス自身の外からアクセスされることはありません。さらに、実装ブロック内に宣言すること、あるいは宣言済みプロパティから自動生成させることも可能です。したがって通常は、インスタンス変数宣言をパブリックインターフェイスで行うべきではないので、波括弧も省略してください。
</pre>

* [Xcode4.4 Modern Objective-C Syntaxでコードをきれいにする方法](http://www.zero4racer.com/blog/798) by Zero4Racer (2012/7/25)

<pre>コンパイラが@synthesizeを補完してくれる様になったため、基本的な状況では、@synthesizeを書かなくてよくなりました。また、synthesizeされる相手のivarも、同名の変数ではなくて、アンダーバーを最初に付けた物がデフォルトとなります。これまで議論されて来たことがappleのコンパイラによって、これからは定義されているため、安心ですね。ちなみに、これは以前からですが、(strong)というのも，デフォルトがstrongなので、書かなくてよくなりました。
</pre>

* [プロパティに対応するインスタンス変数の命名規則について](http://www.awaresoft.jp/ios-dev/item/115-ivar-naming-convention.html) by Awaresoft (2012/3/18)

<pre>1.プロパティに対応するインスタンス変数はヘッダファイルで宣言する必要はまずない。
 (iOSのバージョンに関係なく） 
2.プロパティに対応するインスタンス変数はsynthesizeで常に別名をつけるべき。
3.インスタンス変数名はプロパティ名の前に_をつけたものにする。 
4.インスタンス変数にアクセスするときは、クラス内からでも常にアクセッサメソッドを通して行うようにする。（setter/getter内やinit, dealloc以外）
</pre>

* [第3回 Objective-Cのクラス定義を理解しよう](http://www.atmarkit.co.jp/fcoding/articles/objc/03/objc03b.html) by @IT (2008/11/10) 

* [プロパティの定義](http://www.objectivec-iphone.com/introduction/property/property.html) by 逆引きObjectiv-C


* [Objective-C プログラミング言語](https://developer.apple.com/jp/devcenter/ios/library/japanese.html)

### サンプルコード

Before

```java
# ヘッダーファイル Smaple.h
@interface Sample {
  NSString* _message1;
  NSString* _message2;
  NSString* _message3;
}
@property (nonatomic, strong) NSString *message1;
@property (nonatomic, strong) NSString *message2;
@property (nonatomic, strong) NSString *message3;
@end

# 実装ファイル Sample.m
@implementation Sample

@synthesize message = _message1;
@synthesize message = _message2;
@synthesize message = _message3;

@end
```

After (Xcode4.4以降+LLVM)

```java
# ヘッダーファイル Smaple.h
@interface Sample
  /* 記述不要 */
@end

# 実装ファイル Sample.m
@interface Sample() // 無名カテゴリ

@property (nonatomic, strong) NSString *message1;
@property (nonatomic, strong) NSString *message2;
@property (nonatomic, strong) NSString *message3;

@end

@implementation Sample

@synthesize message = _message1;
@synthesize message = _message2;
@synthesize message = _message3;

@end
```

確認用メソッド

```java

- (void)test {

  _message = [NSString stringWithFormat:@"インスタンス変数"];

  LOG(@"message = %@", _message);
  LOG(@"message = %@", self.message);
  LOG(@"message = %@", [self message]);

  self.message = [NSString stringWithFormat:@"プロパティ"];

  LOG(@"message = %@", _message);
  LOG(@"message = %@", self.message);
  LOG(@"message = %@", [self message]);

  [self setMessage:[NSString stringWithFormat:@"セッター"]];

  LOG(@"message = %@", _message);
  LOG(@"message = %@", self.message);
  LOG(@"message = %@", [self message]);

}

@end
```
