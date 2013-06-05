---
layout: post
title: "AVFoundationで動画を加工合成処理"
description: "AVFoundationの基本的な流れを整理。"
published: true
category: objective-c
tags: [avfoundation]
---
{% include JB/setup %}

## やりたいこと。

AVFoundationを初挑戦中。AVFoundationのドキュメントを読んでみるも難しい。やりたいことはAVCaptureMovieFileOutputで動画を録画して、その録画した動画にイラストやらコピーライトを追加したい。本当はエフェクトとか色々いれたいとろこだけど、まずは簡単そうなところから着手。今回、取り組む要件としては下記2点。

* *動画ファイルにコピーライトを合成する。*
* *動画ファイルに会社のロゴを合成する。*


## AVFoundationクラスを整理

AVFoundationのクラスは多く関係性がいまいち掴めないので、まずはAVFoundationのクラスの整理。全クラスではなく動画の録画から加工までの流れで最低限必要になるであろうクラスを列挙。大きく録画に必要なクラス(青)と加工処理に必要なクラス(オレンジ)の２つに別れる。

<img alt="AVFoundationのクラス図" src="http://img.kuman.asia/files/avfoundation-class.jpg" id="avfoundation-class">

## 動画合成の流れ

今回実装したいのは録画の後の加工処理なので主に上記の"動画の加工/編集に必要なクラス群"(オレンジ)のクラスを使用する。あとは加工用のテキストやイラストに必要なCALayerクラスも必要。大雑把な流れとしてはこんな感じ。

1. *ベースとなる動画のコンポジションを作成。*
2. *合成したいテキストやイラストをCALayerで作成して、合成用コンポジションを生成。*
3. *AVAssetExportSessionを使用して1と2のコンポジションを合成。*

で、下記がその合成までに必要なクラスとメソッドを明記したフロー(細かいけど)。ベースと合成用のコンポジションを作成してAVAssetExportSessionで合成するという流れ。

<img alt="AVFoundationの動画の加工処理の流れ" src="http://img.kuman.asia/files/avfoundation-composition-flow.jpg" id="avfoundation-composition-flow">

コードは下記。AVCaptureMovieFileOutputで動画を録画し終わった後を想定しているので、CaptureOutputメソッドから記述。


```objectivec

#pragma marks - AVCaptureFileOutputRecordingDelegate methods

// 動画撮影終了時に呼び出される。
- (void)captureOutput:(AVCaptureFileOutput *)captureOutput
didFinishRecordingToOutputFileAtURL:(NSURL *)outputFileURL
fromConnections:(NSArray *)connections
error:(NSError *)error
{
  // 合成処理実行。動画ファイルのURLを渡す。
  [self compositeMovieFromUrl:outputFileURL];
}

// 動画の合成処理。コピーライトと会社のロゴを合成する。
- (void)compositeMovieFromUrl:(NSURL *)outputFileURL {

  //***** 1. ベースとなる動画のコンポジションを作成。*****//

  // 動画URLからアセットを生成
  AVURLAsset *videoAsset = [[AVURLAsset alloc] initWithURL:outputFileURL options:nil];

  // コンポジション作成
  AVMutableComposition* mixComposition = [AVMutableComposition composition];
  AVMutableCompositionTrack *compositionVideoTrack = 
      [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo
      preferredTrackID:kCMPersistentTrackID_Invalid];

  // アセットからトラックを取得
  AVAssetTrack *videoTrack = [[videoAsset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0];

  // コンポジションの設定
  [compositionVideoTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, videoAsset.duration) ofTrack:videoTrack atTime:kCMTimeZero error:nil];
  [compositionVideoTrack setPreferredTransform:[[[videoAsset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0] preferredTransform]];

  //***** 2. 合成したいテキストやイラストをCALayerで作成して、合成用コンポジションを生成。*****//

  // ロゴのCALayer作成
  UIImage *logoImage = [UIImage imageNamed:@"logo.png"];
  CALayer *logoLayer = [CALayer layer];
  logoLayer.contents = (id) logoImage.CGImage;
  logoLayer.frame = CGRectMake(5, 25, 57, 57); 
  logoLayer.opacity = 0.9; 

  // 動画のサイズを取得
  CGSize videoSize = videoTrack.naturalSize;

  // コピーライトのCALayerを作成
  CATextLayer *copyrightLayer = [CATextLayer layer];
  copyrightLayer.string = @"kuman.com";
  [copyrightLayer setFont:@"Helvetica"];
  copyrightLayer.fontSize = videoSize.height / 6;
  copyrightLayer.shadowOpacity = 0.5;
  copyrightLayer.alignmentMode = kCAAlignmentCenter;
  copyrightLayer.bounds = CGRectMake(0, 0, videoSize.width, videoSize.height / 6);

  // 親レイヤーを作成
  CALayer *parentLayer = [CALayer layer];
  CALayer *videoLayer = [CALayer layer];
  parentLayer.frame = CGRectMake(0, 0, videoSize.width, videoSize.height);
  videoLayer.frame  = CGRectMake(0, 0, videoSize.width, videoSize.height);
  [parentLayer addSublayer:videoLayer];
  [parentLayer addSublayer:logoLayer];
  [parentLayer addSublayer:copyrightLayer];

  // 合成用コンポジション作成
  AVMutableVideoComposition* videoComp = [AVMutableVideoComposition videoComposition];
  videoComp.renderSize = videoSize;
  videoComp.frameDuration = CMTimeMake(1, 30);
  videoComp.animationTool =
    [AVVideoCompositionCoreAnimationTool 
    videoCompositionCoreAnimationToolWithPostProcessingAsVideoLayer:videoLayer 
    inLayer:parentLayer];

  // インストラクション作成
  AVMutableVideoCompositionInstruction *instruction = 
    [AVMutableVideoCompositionInstruction videoCompositionInstruction];
  instruction.timeRange = CMTimeRangeMake(kCMTimeZero, [mixComposition duration]); // 時間を設定
  AVMutableVideoCompositionLayerInstruction* layerInstruction =
    [AVMutableVideoCompositionLayerInstruction 
    videoCompositionLayerInstructionWithAssetTrack:videoTrack];
  instruction.layerInstructions = [NSArray arrayWithObject:layerInstruction];

  // インストラクションを合成用コンポジションに設定
  videoComp.instructions = [NSArray arrayWithObject: instruction];

  //***** 3. AVAssetExportSessionを使用して1と2のコンポジションを合成。*****//

  // 1のコンポジションをベースにAVAssetExportを生成
  _assetExport = [[AVAssetExportSession alloc] initWithAsset:mixComposition 
    presetName:AVAssetExportPresetMediumQuality];
  // 2の合成用コンポジションを設定
  _assetExport.videoComposition = videoComp;

  // エクスポートファイルの設定
  NSString* videoName = @"kuman.mov";
  NSString *exportPath = [NSTemporaryDirectory() stringByAppendingPathComponent:videoName];
  NSURL *exportUrl = [NSURL fileURLWithPath:exportPath];
  _assetExport.outputFileType = AVFileTypeQuickTimeMovie;
  _assetExport.outputURL = exportUrl;
  _assetExport.shouldOptimizeForNetworkUse = YES;

  // ファイルが存在している場合は削除
  if ([[NSFileManager defaultManager] fileExistsAtPath:exportPath])
  {
    [[NSFileManager defaultManager] removeItemAtPath:exportPath error:nil];
  }

  // エクスポード実行
  [_assetExport exportAsynchronouslyWithCompletionHandler:
    ^(void ) {
      // 端末に保存
      ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
      if ([library videoAtPathIsCompatibleWithSavedPhotosAlbum:exportUrl])
      {
        [library writeVideoAtPathToSavedPhotosAlbum:exportUrl
          completionBlock:^(NSURL *assetURL, NSError *assetError)
          {
            if (assetError) { }
          }];
      }
    }
  ];
}

```

## 参考サイト

* [iOS Developer AVFoundation](https://developer.apple.com/jp/devcenter/ios/library/documentation/AVFoundationPG.pdf)

