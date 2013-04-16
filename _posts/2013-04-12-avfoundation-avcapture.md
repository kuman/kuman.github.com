---
layout: post
title: "avfoundation avcapture"
description: ""
published: false
category: 
tags: []
---
{% include JB/setup %}

```java
#import "SampleViewController.h"

@interface  SampleViewController()

@property AVCaptureSession* session;
@property AVCaptureMovieFileOutput* movieFileOutput;
@property AVCaptureDeviceInput* deviceInput;
@property AVCaptureDevice* device;
@property AVCaptureVideoPreviewLayer* videoPreviewLayer;

- (AVCaptureDevice *)videoDeviceWithPosition:(AVCaptureDevicePosition)position;
- (void)displayCapture;

@end

@implementation SampleViewController

- (void)viewDidLoad {
  [super viewDidLoad];
}

// ビデオキャプチャを表示する
- (void)displayCapture {

  /* 1. セッションの作成 */

  self.session = [[AVCaptureSession alloc] init];

  /* 2. カメラデバイスの取得 */

  // 端末背面のカメラデバイス取得
  self.device = [self videoDeviceWithPosition:AVCaptureDevicePositionBack];

  /* 3. 入力デバイス作成 */

  NSError *error = nil;
  self.deviceInput = [AVCaptureDeviceInput deviceInputWithDevice:self.device error:&error];
  [self.session addInput:self.deviceInput];

  /* 4. 出力デバイス作成 */

  self.movieFileOutput = [[AVCaptureMovieFileOutput alloc] init];
  [self.session addOutput:self.movieFileOutput];

  /* 5. プレビューレイヤー作成 */

  self.videoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:self.session];
  [self.videoPreviewLayer setFrame:[self.view.layer bounds]];
  [self.view.layer addSublayer:self.videoPreviewLayer];

  [self.session startRunning];

}

// ビデオデバイスを取得
- (AVCaptureDevice *)videoDeviceWithPosition:(AVCaptureDevicePosition)position
{
  NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
  for (AVCaptureDevice *device in devices) {
    if ([device position] == position)
      LOG();
    return device;
  }
  return nil;
}

@end
```

### 参考サイト

* [AVFoundation Preview with AVCaptureMovieFileOutput](http://qiita.com/items/517185293ccf520f14f7) by Qiita (2013/3/19)

* [OpenCVを利用したリアルタイムフィルタリングの基本](http://dev.classmethod.jp/smartphone/iphone/opencv-video/) by ClassMethods (2013/4/7)

* [ビデオカメラアプリの作り方](http://news.mynavi.jp/column/iphone/041/index.html) by マイナビ (2013/4/12)
AVFoundationについて丁寧な説明。

* [iOSゲーム本”iPhoneソーシャルゲーム開発”の写経8：動画の再生](http://yamakadoh.net/weblog/?p=439)
端末からの動画取得と再生方法

* [How to get movie with AVFoundation](http://qiita.com/items/838b47c36be0e1d368da)

* [RosyWritter](http://developer.apple.com/library/ios/#samplecode/RosyWriter/Introduction/Intro.html)
エフェクトのサンプル動画を

* [iOSのカメラ機能を使う方法](http://dev.classmethod.jp/smartphone/ios-camera-intro/)

* [AVFoundationむずかしい](http://blog.azzip-azzip.com/2012/04/)

* [obj-c AVFoundationのAVCapture〜クラスのメモ](http://blog.azzip-azzip.com/2012/03/avfoundation/)

* [GPUImageを使わずに高速リアルタイムフィルタ！iOS向けinstagram風フィルタ +カメラアプリができるまで(その0)](http://enator.hatenablog.com/entry/2013/01/31/083351)

* [GPUImageを使わずに高速リアルタイムフィルタ！iOS向けinstagram風フィルタ +カメラアプリができるまで(その1)](http://enator.hatenablog.com/entries/2013/02/01)


```
キャプチャ出力を使用したセッションからの出力の取得
キャプチャセッションから出力を取得するには、1つ以上の出力を追加します。出力は、 AVCaptureOutputの具象サブクラスのインスタンスです。
AVCaptureMovieFileOutputは、ムービーファイルに出力する場合に使用します。 AVCaptureVideoDataOutputは、キャプチャ中のビデオのフレームを処理する場合に使用しま
す。
AVCaptureAudioDataOutputは、キャプチャ中のオーディオデータを処理する場合に使用しま す。
AVCaptureStillImageOutputは、付随するメタデータを使用して静止画像をキャプチャする場 合に使用します。
```
