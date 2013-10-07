---
layout: post
title: "cocos2d-x-release"
description: ""
published: false
category: 
tags: []
---

Cocos2d-x3.0alphaがリリースされたのでいじってみました。なにやらEventDispatcherたるものができて、今まであった各種Dispatcher(TouchDispatcher, KeypadDispatcher, KeyboardDispatcher, AccelerometerDispatcher)が集約されたようです。また、タッチイベント関数の引数にC++の標準ライブラリが当てられており、ちょい困惑したのでメモ。(C++ヘッポコレベルなので・・)

・Cocos2d-x-alpha Release Note (2013/9/25)
http://www.cocos2d-x.org/wiki/Release_Notes_for_Cocos2d-x_v300
・Cocos2d-x-alpha API Documents
http://www.cocos2d-x.org/wiki/Reference

```c++
#ifndef __TEST_SCENE_H__
#define __TEST_SCENE_H__

#include "BaseScene.h"
#include "GLES-Render.h"

USING_NS_CC;

class TestScene : public BaseScene {
    public:
        ~TestScene();
        virtual bool init();
        void onTouchesEnded(const std::vector<Touch*>& touches, Event* event);
        void onTouchesMoved(const std::vector<Touch*>& touches, Event* event);
    private:
        EventListenerTouch* listener;
        void createEventListener();
};

#endif // __TEST_SCENE_H__
```

```c++
TestScene::~TestScene() {
    EventDispatcher::getInstance()->removeEventListener(listener);
}

bool TestScene::init() {
    if (!Layer::init()) {
        return false;
    }
    createEventListener();
    return true;
}

void TestScene::createEventListener() {
    listener = EventListenerTouch::create(Touch::DispatchMode::ALL_AT_ONCE);
    listener->onTouchesEnded = CC_CALLBACK_2(TestScene::onTouchesEnded, this);
    listener->onTouchesMoved = CC_CALLBACK_2(TestScene::onTouchesMoved, this);
    EventDispatcher::getInstance()->addEventListenerWithSceneGraphPriority(listener, this);
}

/**
   * @brief タッチアクション
    */
void TestScene::onTouchesEnded(const std::vector<Touch*>& touches, Event* event) {
    for(auto touch : touches) {
        Point location = touch->getLocationInView();
        location = Director::getInstance()->convertToGL(location);
        log("location x = %f, y = %f", location.x, location.y);
    }
}

/**
 * @brief タッチアクション
 */
void TestScene::onTouchesMoved(const std::vector<Touch*>& touches, Event* event) {
    for(auto touch : touches) {
        Point location = touch->getLocationInView();
        location = Director::getInstance()->convertToGL(location);
        log("location x = %f, y = %f", location.x, location.y);
    }
}
```

あとはちょこちょこ記述が変わってる。
ccTouch -> onTouch
Label::HAlignment:: -> TextHAlignment::
didAccelerate -> onAccelerate
Director::getInstance()->getAccelerometer()->setAccelerometerInterval(INTERVAL_VALUE) 
    -> Device::setAccelerometerInterval(INTERVAL_VALUE)

