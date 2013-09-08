---
layout: post
title: "Cocos2d-x 入門レシピ"
description: ""
category: 
published: true
tags: [cocos2d-x]
---
{% include JB/setup %}

クロスプラットフォーム開発を目指し、2DゲームエンジンであるCocos2d-xを試してみた。初めての言語ということもあり、基本的な実装で時間を取られる。なんでよく使う基本的な記述を列挙してみる。 前提条件としてはCocos2d-xをインストールして、新規プロジェクトを作成できたという段階です。またCocos2d-xのバージョンは3.0系での構文になります。
(随時更新予定)

#### Index

* Recipe1. [ディレクターを取得したい](#recipe1)
* Recipe2. [ログを表示したい](#recipe2)
* Recipe3. [0座標を取得したい](#recipe3)
* Recipe4. [画面解像度を取得したい](#recipe4)
* Recipe5. [FPSを表示したい](#recipe5)
* Recipe6. [FPSを変更したい](#recipe6)
* Recipe7. [定期的にタスクを実行したい](#recipe7)
* Recipe8. [画像を表示したい](#recipe8)
* Recipe9. [文字列を表示したい](#recipe9)
* Recipe10. [メニューボタン(リンク)を表示したい](#recipe10)
* Recipe11. [シーンを切り替えたい](#recipe11)
* Recipe12. [EGLViewを取得したい](#recipe12)
* Recipe13. [背景画像を設定したい](#recipe13)
* Recipe14. [BGMを鳴らしたい](#recipe14)
* Recipe15. [効果音を鳴したい](#recipe15)
* Recipe16. [画像をアニメーションさせたい](#recipe16)

## <span id="recipe1">Recipe1.</span> ディレクターを取得したい

```cpp
Director* director = Director::getInstance();
```

## <span id="recipe2">Recipe2.</span> ログを表示したい
```cpp
log("YOUR LOG MESSAGE");
```

## <span id="recipe3">Recipe3.</span> 0座標を取得したい
```cpp
origin = Director::getInstance()->getVisibleOrigin();
log("x = %f, y = %f", origin.x, origin.y);
```

## <span id="recipe4">Recipe4.</span> 画面解像度を取得したい
```cpp
visibleSize = Director::getInstance()->getVisibleSize();
log("width = %f, height = %f", visibleSize.x, visibleSize.y);
```

## <span id="recipe5">Recipe5.</span> FPSを表示したい
```cpp
director->setDisplayStats(true); // 画面左下に表示される
```

## <span id="recipe6">Recipe6.</span> FPSを変更したい
```cpp
director->setAnimationInterval(1.0 / 60); // Default = 1.0 / 60
```

## <span id="recipe7">Recipe7.</span> 定期的にタスクを実行したい。
```cpp
float duration = 10.0f; // 間隔
schedule(schedule_selector(TestScene::task), duration);

// (下記関数は適切な場所に記述)
void TestScene::task(float dt) {
    log("TestScene task");
}
```

## <span id="recipe8">Recipe8.</span> 画像を表示したい
```cpp
Sprite* sprite = Tree::create("dog.png");
sprite->setPosition( Point(100, 100) );
this->addChild(sprite, 1); // 第2引数は表示順
```

## <span id="recipe9">Recipe9.</span> 文字列を表示したい
```cpp
LabelTTF* messageLabel = LabelTTF::create("Cocos2d-x勉強中", "Arial", 16);
messageLabel->setPosition(Point(100,100)); // 表示場所を設定
messageLabel->enableShadow( Size(0,1), 0.9f, 2 ); // シャドウをつける
messageLabel->setColor(Color3B::BLACK); // 文字色を設定
addChild(messageLabel); 
```

## <span id="recipe10">Recipe10.</span> メニューボタン(リンク)を表示したい
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

## <span id="recipe11">Recipe11.</span> シーンを切り替えたい
```cpp
Scene *scene = NextScene::scene();
Director::getInstance()->replaceScene(TransitionFade::create(0.3f, scene))
```

## <span id="recipe12">Recipe12.</span> EGLViewを取得したい
```cpp
EGLView* eglView = EGLView::getInstance();
```

## <span id="recipe13">Recipe13.</span> 背景画像を設定したい
````cpp
origin = Director::getInstance()->getVisibleOrigin();
visibleSize = Director::getInstance()->getVisibleSize();
Sprite* background = Sprite::create("background.jpg");
background->setPosition(Point(origin.x + visibleSize.width/2, origin.y + visibleSize.height/2));
this->addChild(background, 0);
```

## <span id="recipe14">Recipe14.</span> BGMを鳴らしたい
```cpp
CocosDenshion::SimpleAudioEngine::getInstance()->setEffectsVolume(1.0);
CocosDenshion::SimpleAudioEngine::getInstance()->setBackgroundMusicVolume(1.0);
CocosDenshion::SimpleAudioEngine::getInstance()->playBackgroundMusic("bgm.mp3", true);
```

## <span id="recipe15">Recipe15.</span> 効果音を鳴したい
```cpp
CocosDenshion::SimpleAudioEngine::getInstance()->preloadEffect("sound01.mp3");
CocosDenshion::SimpleAudioEngine::getInstance()->playEffect("sound01.mp3");
```

## <span id="recipe16">Recipe16.</span> 画像をアニメーションさせたい
```cpp
// アニメーション時間
float duration = 1.0f;

// Spriteで画像を表示
Sprite *sprite = Sprite::create("cat.png");
sprite->setPosition(this->getPosition());
this->addChild(sprite, 1); // 第2引数は表示順

// 基本例.1 画像を拡大(縮小)させる
float scale = 1.5f;
ActionInterval* actionScale  = ScaleTo::create(duration, scale);
sprite->runAction(actionScale);

// 基本例.2 画像を回転させる
float rotate = 360;
ActionInterval* actionRotate = RotateBy::create(duration, rotate);
sprite->runAction(actionRotate);

// 基本例.3 ペジェ曲線に沿って移動させる
float x        = this->getPosition().x;
float y        = this->getPosition().y;
float height   = 300;
float width    = 300;
ccBezierConfig bezier;
bezier.controlPoint_1 = Point(x, y);
bezier.controlPoint_2 = Point(x + width/2, y + height * 2);
bezier.endPosition    = Point(x + width, y + height);
ActionInterval* actionBezier = BezierTo::create(duration, bezier);
sprite->runAction(actionBezier);

// 応用例.1 フェイドアウト後、関数を呼び出す
ActionInterval* actionFade   = FadeOut::create(duration);
CallFuncN* callFunction = CallFuncN::create(this, callfuncN_selector(CrashBeanSprite::callbackActionFinished));
Sequence* callAction = Sequence::create(actionFade, callFunction, NULL);
sprite->runAction(callAction);

// (下記関数は適切な場所に記述)
void TestScene::callbackActionFinished(Sprite *sprite) {
}
```

