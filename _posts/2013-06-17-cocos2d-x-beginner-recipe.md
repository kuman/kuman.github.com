---
layout: post
title: "Cocos2d-x 入門レシピ"
description: ""
category: 
published: true
tags: [cocos2d-x]
---
{% include JB/setup %}

クロスプラットフォーム開発を目指し、2DゲームエンジンであるCocos2d-xを試してみた。初めての言語ということもあり、基本的な実装で時間を取られる。なんでよく使う基本的な記述を列挙してみる。(随時更新予定)

前提条件としてはCocos2d-xをインストールして、新規プロジェクトを作成できたという段階です。

## ディレクターを取得したい

```cpp
Director* director = Director::getInstance();
```

## 0座標を取得したい
```cpp
origin = Director::getInstance()->getVisibleOrigin();
log("x = %f, y = %f", origin.x, origin.y);
```

## 画面解像度を取得したい
```cpp
visibleSize = Director::getInstance()->getVisibleSize();
log("width = %f, height = %f", visibleSize.x, visibleSize.y);
```

## FPSを表示したい
```cpp
director->setDisplayStats(true); // 画面左下に表示される
```

## FPSを設定したい
```cpp
director->setAnimationInterval(1.0 / 60); // Default = 1.0 / 60
```

## 定期的にタスクを実行したい。
```cpp
float duration = 10.0f; // 間隔
schedule(schedule_selector(TestScene::task), duration);

// (下記関数は適切な場所に記述)
void TestScene::task(float dt) {
    log("TestScene task");
}
```

## 文字列を表示したい
```cpp
LabelTTF* messageLabel = LabelTTF::create("Cocos2d-x勉強中", "Arial", 16);
messageLabel->setPosition(Point(100,100)); // 表示場所を設定
messageLabel->setColor(Color3B::BLACK); // 文字色を設定
addChild(messageLabel); 
```

## メニューボタン(リンク)を表示したい
```cpp
// メニューオブジェクト生成
menu = Menu::create(NULL);
menu->setPosition(Point::ZERO);
this->addChild(menu, 1);

// メニューアイテム(ボタン)を生成
MenuItemImage *item = MenuItemImage::create(
        "nextButton.png", "nextButtonSelected.png",
        CC_CALLBACK_1(ResultScene::menuNextButtonCallback, this));
item->setPosition(Point( 100, 100 ));
menu->addChild(item);

// コールバック関数 (下記関数は適切な場所に記述)
void TestScene::menuNextButtonCallback(Object* pSender) {
    Scene *scene = ResultScene::scene();
    Director::getInstance()->replaceScene(TransitionFade::create(0.3f, scene));
}
```

## シーンを切り替えたい
```cpp
Scene *scene = NextScene::scene();
Director::getInstance()->replaceScene(TransitionFade::create(0.3f, scene))
```

## EGLViewを取得したい
```cpp
EGLView* eglView = EGLView::getInstance();
```

## 背景画像を設定したい
````cpp
origin = Director::getInstance()->getVisibleOrigin();
visibleSize = Director::getInstance()->getVisibleSize();
Sprite* background = Sprite::create("background.jpg");
background->setPosition(Point(origin.x + visibleSize.width/2, origin.y + visibleSize.height/2));
this->addChild(background, 0);
```

## BGMを鳴らしたい
```cpp
CocosDenshion::SimpleAudioEngine::getInstance()->setEffectsVolume(1.0);
CocosDenshion::SimpleAudioEngine::getInstance()->setBackgroundMusicVolume(1.0);
CocosDenshion::SimpleAudioEngine::getInstance()->playBackgroundMusic("bgmCrashBean.mp3", true);
```

## 効果音を鳴したい
```cpp
CocosDenshion::SimpleAudioEngine::getInstance()->preloadEffect("explosion01.mp3");
CocosDenshion::SimpleAudioEngine::getInstance()->playEffect("explosion02.mp3");
```

