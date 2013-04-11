---
layout: post
title: "objective c properties and variables"
description: ""
category: Tech
tags: [objective-c]
---
{% include JB/setup %}

## Objective-Cのインスタンス変数とプロパティ


* [プロパティに対応するインスタンス変数の命名規則について](http://www.awaresoft.jp/ios-dev/item/115-ivar-naming-convention.html) by Awaresoft (2013/3/18)

* [第3回 Objective-Cのクラス定義を理解しよう](http://www.atmarkit.co.jp/fcoding/articles/objc/03/objc03b.html) by @IT (2008/11/10) 

* [プロパティの定義](http://www.objectivec-iphone.com/introduction/property/property.html) by 逆引きObjectiv-C

* [Xcode4.4 Modern Objective-C Syntaxでコードをきれいにする方法](http://www.zero4racer.com/blog/798) by Zero4Racer (2012/7/25)

* [Objective-C プログラミング言語](https://developer.apple.com/jp/devcenter/ios/library/japanese.html)

<pre>
  歴史的には、インターフェイスにはクラスのインスタンス変数宣言が必要でした。（中略）インスタンス変数は実装詳細であり、通常、クラス自身の外からアクセスされることはありません。さらに、実装ブロック内に宣言すること、あるいは宣言済みプロパティから自動生成させることも可能です。したがって通常は、インスタンス変数宣言をパブリックインターフェイスで行うべきではないので、波括弧も省略してください。
</pre>

```objective-c
@interface Sample() {
      NSString* _message;
}

@property (nonatomic, strong) NSString *message;

@end

@implementation Sample

@synthesize message = _message;

- (NSString)message {
     return _message;
}

- (void)setMessage:(NSString)message {
  _message = message;
}

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

@end
```
