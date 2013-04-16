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


#import "ShootViewController.h"

@interface  ShootViewController()

  @property float padding;
  @property float width;
  @property float height;
  @property float btnWidth;
  @property float btnHeight;
  @property CGRect rectBtnPlay;
  @property CGRect rectBtnStop;
  @property CGRect rectPreview;

  @property AVCaptureSession *session;
  @property AVCaptureMovieFileOutput *movieFileOutput;
  @property AVCaptureDeviceInput *deviceInput;
  @property AVCaptureDevice *device;
  @property AVCaptureVideoPreviewLayer *videoPreviewLayer;

  @property UIButton *btnPlay;
  @property UIButton *btnStop;
  @property UIView *preview;

  @property AVAssetExportSession *assetExport;

  - (void) calculateSize;
  - (void)displayCapture;
  - (AVCaptureDevice *)videoDeviceWithPosition:(AVCaptureDevicePosition)position;
  - (void) touchBtnPlay:(UIButton *)button;
  - (void) touchBtnStop:(UIButton *)button;
  - (NSURL *)compositeMovieFromUrl:(NSURL *)outputFileURL;

  @end

  @implementation ShootViewController

  // 各種サイズを計算する。
  -(void) calculateSize {
    self.padding   = 10;
    self.width     = self.view.frame.size.height;
    self.height    = self.view.frame.size.width;
    self.btnWidth  = self.width/6;
    self.btnHeight = self.height/8;
    self.rectBtnPlay = CGRectMake(self.width-self.btnWidth-self.padding,self.height-self.btnHeight-self.padding,self.btnWidth,self.btnHeight);
    self.rectBtnStop = CGRectMake(self.width-self.btnWidth*2-self.padding*2,self.height-self.btnHeight-self.padding,self.btnWidth,self.btnHeight);
    self.rectPreview = CGRectMake(0, 0, self.width*3/4, self.height*3/4);
    LOG(@"width = %f, height = %f, btnWidth = %f, btnHeight = %f", self.width, self.height, self.btnWidth, self.btnHeight);
  }

- (void)viewDidLoad {
  [super viewDidLoad];

  //***** 画面設定 *****//

  [self.navigationController.navigationBar setHidden:NO];
  self.navigationController.navigationBar.translucent = YES;
  [self.view setBackgroundColor:[UIColor brownColor]];
  [self calculateSize];

  //***** ビデオキャプチャ生成&設置 *****//

  [self displayCapture];

  //***** 各種ボタン・ラベルを作成 *****//

  // タイトルを作成
  UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 400, 44)];
  titleLabel.backgroundColor = [UIColor clearColor];
  titleLabel.font = [UIFont boldSystemFontOfSize:17.0];
  titleLabel.shadowColor = [UIColor colorWithWhite:0.0 alpha:0.5];
  titleLabel.textColor =[UIColor whiteColor];
  titleLabel.textAlignment = NSTextAlignmentCenter;
  titleLabel.text = @"動画撮影";
  self.navigationItem.titleView = titleLabel;

  // ログインフォームを表示するボタン
  self.btnPlay = [UIButton buttonWithType:UIButtonTypeRoundedRect];
  [self.btnPlay setTitle:@"Play" forState:UIControlStateNormal];
  [self.btnPlay setFrame:self.rectBtnPlay];
  [self.view addSubview:self.btnPlay];

  // 新規登録ビューへ移動するボタン
  self.btnStop = [UIButton buttonWithType:UIButtonTypeRoundedRect];
  [self.btnStop setTitle:@"Stop" forState:UIControlStateNormal];
  [self.btnStop setFrame:self.rectBtnStop];
  [self.view addSubview:self.btnStop];

  //***** ボタンにアクションを設定 *****//

  [self.btnPlay addTarget:self action:@selector(touchBtnPlay:) forControlEvents:UIControlEventTouchDown];
  [self.btnStop addTarget:self action:@selector(touchBtnStop:) forControlEvents:UIControlEventTouchDown];

}

// 終了時に呼ばれる
- (void)viewWillDisappear:(BOOL)animated {
  [super viewWillDisappear:animated];

  // ナビゲーションバーを隠す
  [self.navigationController.navigationBar setHidden:YES];
}

// ビデオキャプチャを表示する
- (void)displayCapture {

  /*** 1. セッションの作成 ***/

  self.session = [[AVCaptureSession alloc] init];

  [self.session beginConfiguration];
  /* 設定 */
  [self.session commitConfiguration];

  /*** 2. カメラデバイスの取得 ***/

  // 端末背面のカメラデバイス取得
  self.device = [self videoDeviceWithPosition:AVCaptureDevicePositionBack];

  /*** 3. 入力デバイス作成 ***/

  NSError *error = nil;
  self.deviceInput = [AVCaptureDeviceInput deviceInputWithDevice:self.device error:&error];

  // エラーがないか確認
  if (!self.deviceInput) {
    NSLog(@"CaptureDeviceInput error = %@", error);
  }

  // セッションに入力デバイスが追加できるか確認
  if ([self.session canAddInput:self.deviceInput]) {
    NSLog(@"Can add deviceInput");
    // 入力デバイス追加
    [self.session addInput:self.deviceInput];
  }
  else {
    NSLog(@"Can't add deviceInput");
  }

  /*** 4. 出力デバイス作成 ***/

  self.movieFileOutput = [[AVCaptureMovieFileOutput alloc] init];

  // 出力デバイスの設定
  CMTime maxDuration = CMTimeMake(3600, 600); // 6秒
  self.movieFileOutput.maxRecordedDuration = maxDuration;
  self.movieFileOutput.minFreeDiskSpaceLimit = 1024*1024; // in bytes

  // セッションに出力デバイスが追加できるか確認
  if ([self.session canAddOutput:self.movieFileOutput]) {
    NSLog(@"Can add movieFileOutput");
    [self.session addOutput:self.movieFileOutput];
  } else {
    NSLog(@"Can't add movieFileOutput");
  }

  /*** 5. プレビューレイヤー作成 ***/

  self.preview = [[UIView alloc] initWithFrame:self.rectPreview];
  [self.preview setBackgroundColor: [UIColor greenColor]];
  [self.view addSubview:self.preview];

  self.videoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:self.session];
  [self.videoPreviewLayer setVideoGravity:AVLayerVideoGravityResizeAspect];
  [self.videoPreviewLayer setFrame:self.preview.frame];
  [[self.videoPreviewLayer connection]setVideoOrientation:AVCaptureVideoOrientationLandscapeRight];
  [self.preview.layer addSublayer:self.videoPreviewLayer];

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


// ログインフォームを表示する
-(void) touchBtnPlay:(UIButton *)button {
  LOG();
  //----- START RECORDING -----

  //Create temporary URL to record to
  NSString *outputPath = [[NSString alloc] initWithFormat:@"%@%@", NSTemporaryDirectory(), @"output.mov"];
  NSURL *outputURL = [[NSURL alloc] initFileURLWithPath:outputPath];
  NSFileManager *fileManager = [NSFileManager defaultManager];
  if ([fileManager fileExistsAtPath:outputPath])
  {
    NSError *error;
    if ([fileManager removeItemAtPath:outputPath error:&error] == NO)
    {
      LOG(@"error = %@", error)
        //Error - handle if requried
    }
  }
  //Start recording
  [self.movieFileOutput startRecordingToOutputFileURL:outputURL recordingDelegate:self];
}

// 新規登録ビューへ移動
-(void) touchBtnStop:(UIButton *)button {
  LOG();
  [self.session stopRunning];
}

#pragma marks - AVCaptureFileOutputRecordingDelegate methods

// 動画撮影終了時に呼び出される。
- (void)captureOutput:(AVCaptureFileOutput *)captureOutput
didFinishRecordingToOutputFileAtURL:(NSURL *)outputFileURL
fromConnections:(NSArray *)connections
error:(NSError *)error
{
  LOG(@"didFinishRecordingToOutputFileAtURL - enter");

  LOG(@"outputFileURL = %@", outputFileURL);
  NSURL *compositeURL = [self compositeMovieFromUrl:outputFileURL];
  LOG(@"compositeURL = %@", compositeURL);

  BOOL RecordedSuccessfully = YES;
  if ([error code] != noErr)
  {
    // A problem occurred: Find out if the recording was successful.
    id value = [[error userInfo] objectForKey:AVErrorRecordingSuccessfullyFinishedKey];
    if (value)
    {
      RecordedSuccessfully = [value boolValue];
    }
  }
  if (RecordedSuccessfully)
  {
    //----- RECORDED SUCESSFULLY -----
    NSLog(@"didFinishRecordingToOutputFileAtURL - success");
    //        ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
    //        if ([library videoAtPathIsCompatibleWithSavedPhotosAlbum:outputFileURL])
    //        {
    //            [library writeVideoAtPathToSavedPhotosAlbum:outputFileURL
    //                                        completionBlock:^(NSURL *assetURL, NSError *assetError)
    //                                        {
    //                                            if (assetError)
    //                                            {
    //
    //                                            }
    //                                        }];
    //        }
  }
}

// 動画を加工する
- (NSURL *)compositeMovieFromUrl:(NSURL *)outputFileURL {

  // 動画の取得
  AVURLAsset *videoAsset = [[AVURLAsset alloc] initWithURL:outputFileURL options:nil];
  LOG(@"asset = %@, trackCount = %d", videoAsset, [videoAsset.tracks count]);

  //***** 動画を合成するためのコンポジションクラスを生成 *****//
  AVMutableComposition* mixComposition = [AVMutableComposition composition];
  // コンポジショントラックを生成
  AVMutableCompositionTrack *compositionVideoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];

  // AVURLAssetからトラックを取得
  AVAssetTrack *videoTrack = [[videoAsset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0];

  // If you need audio as well add the Asset Track for audio here
  // もし音声が必要ならば、ここにAudioトラックを追加する。

  // コンポジションの設定
  [compositionVideoTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, videoAsset.duration) ofTrack:videoTrack atTime:kCMTimeZero error:nil];
  [compositionVideoTrack setPreferredTransform:[[[videoAsset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0] preferredTransform]];

  // アイコンとレイヤーの生成
  UIImage *iconImage = [UIImage imageNamed:@"icon.png"];
  CALayer *iconLayer = [CALayer layer];
  iconLayer.contents = (id) iconImage.CGImage;
  iconLayer.frame = CGRectMake(5, 25, 57, 57); //Needed for proper display. We are using the app icon (57x57). If you use 0,0 you will not see it
  iconLayer.opacity = 0.9; //Feel free to alter the alpha here

  // If you want a text instead of image here is the code
  // 画像の代わりにテキストを表示したい場合はここにコードを記述

  // トラックの取得
  //    AVAssetTrack *track = [videoAsset.tracks objectAtIndex:0];
  CGSize videoSize = videoTrack.naturalSize;

  // テキストレイヤー生成
  CATextLayer *titleLayer = [CATextLayer layer];
  titleLayer.string = @"Text goes here";
  [titleLayer setFont:@"Helvetica"];
  titleLayer.fontSize = videoSize.height / 6;
  titleLayer.shadowOpacity = 0.5;
  titleLayer.alignmentMode = kCAAlignmentCenter;
  titleLayer.bounds = CGRectMake(0, 0, videoSize.width, videoSize.height / 6); //You may need to adjust this for proper display

  // The following code sorts the layer in proper order:
  // 下記のコードを適切な順に並べる。

  // 親レイヤーとビデオレイヤーを作成
  CALayer *parentLayer = [CALayer layer];
  CALayer *videoLayer = [CALayer layer];
  parentLayer.frame = CGRectMake(0, 0, videoSize.width, videoSize.height);
  videoLayer.frame  = CGRectMake(0, 0, videoSize.width, videoSize.height);
  [parentLayer addSublayer:videoLayer];
  [parentLayer addSublayer:iconLayer]; // アイコンを追加
  [parentLayer addSublayer:titleLayer]; // テキストを追加

  // Now we are creating the composition and add the instructions to insert the layer:
  // レイヤーにコンポジションとレイヤーを追加する。

  // ビデオ合成用オブジェクト作成
  AVMutableVideoComposition* videoComp = [AVMutableVideoComposition videoComposition];
  videoComp.renderSize = videoSize;
  videoComp.frameDuration = CMTimeMake(1, 30);
  // ビデオレイヤーを追加
  videoComp.animationTool = [AVVideoCompositionCoreAnimationTool videoCompositionCoreAnimationToolWithPostProcessingAsVideoLayer:videoLayer inLayer:parentLayer];

  // instruction

  AVMutableVideoCompositionInstruction *instruction = [AVMutableVideoCompositionInstruction videoCompositionInstruction];
  instruction.timeRange = CMTimeRangeMake(kCMTimeZero, [mixComposition duration]); // 時間を設定
  //    AVAssetTrack *videoTrack = [[mixComposition tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0];
  // トラックからレイヤーインストラクションを作成。Array型
  AVMutableVideoCompositionLayerInstruction* layerInstruction = [AVMutableVideoCompositionLayerInstruction videoCompositionLayerInstructionWithAssetTrack:videoTrack];
  instruction.layerInstructions = [NSArray arrayWithObject:layerInstruction];

  // インストラクションを追加
  videoComp.instructions = [NSArray arrayWithObject: instruction];

  // And now we are ready to export:
  // 合成した動画を出力する

  // AVMutableCompositionであるmixCompositionを元にAVAssetExportSessionを作成
  _assetExport = [[AVAssetExportSession alloc] initWithAsset:mixComposition presetName:AVAssetExportPresetMediumQuality]; //AVAssetExportPresetPassthrough
  _assetExport.videoComposition = videoComp;

  // 動画ファイル名とエクスポート先のPATHとURLを作成
  NSString* videoName = @"mynewwatermarkedvideo.mov";
  NSString *exportPath = [NSTemporaryDirectory() stringByAppendingPathComponent:videoName];
  NSURL *exportUrl = [NSURL fileURLWithPath:exportPath];

  if ([[NSFileManager defaultManager] fileExistsAtPath:exportPath])
  {
    [[NSFileManager defaultManager] removeItemAtPath:exportPath error:nil];
  }

  // エクスポート設定
  _assetExport.outputFileType = AVFileTypeQuickTimeMovie;
  _assetExport.outputURL = exportUrl;
  _assetExport.shouldOptimizeForNetworkUse = YES;

  //    [strRecordedFilename setString: exportPath];
  LOG(@"exportPath = %@", exportPath);
  LOG(@"exportURL  = %@", exportUrl);

  [_assetExport exportAsynchronouslyWithCompletionHandler:
    ^(void ) {
      //YOUR FINALIZATION CODE HERE
      LOG(":::::::::");
      ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
      if ([library videoAtPathIsCompatibleWithSavedPhotosAlbum:exportUrl])
      {
        [library writeVideoAtPathToSavedPhotosAlbum:exportUrl
          completionBlock:^(NSURL *assetURL, NSError *assetError)
          {
            if (assetError)
            {

            }
          }];
      }
    }
  ];


  return exportUrl;
}

@end

```
