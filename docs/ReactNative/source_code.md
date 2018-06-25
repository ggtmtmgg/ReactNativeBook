# ソースコード
## Libraries/
### ART/
グラフを描写するコンポーネント郡
### ActionSheetIOS/
iOSのaction sheetを呼び出すAPI
### Alert/
Alertを呼び出すAPI
### Animated/
アニメーションを作るAPI
### AppState/
Appの状態を取得したり通知したりするAPI
### BatchedBridge/
JS <-> Nativeのブリッジを提供する NativeModulesは
この中で定義されている
### Blob/
Binarryデータを扱うAPI郡
### BugReporting/
バグレポートデータを集めるクラス
### CameraRoll/
Camera Rollを呼び出すAPI
### Color/
いろいろな色を定義しているクラス
### Components/
コンポーネント郡
### Core/
polyfillの適用等によってクラスやモジュールをグローバルに追加していく
### EventEmitter/
NativeのイベントをListenする
### Experimental/
実験的な機能を提供するディレクトリ
### Geolocation/
位置情報を取得するAPI
### Image/
画像のコンポーネント。提供する機能が多いので分離していると思われる。
### Inspector/
Inspectorのコンポーネント
### Interaction/
長いアニメーションのあとにタスクをスケジュールした
りする
### JSInspector/
### LayoutAnimation/
Layoutの変化に応じて自動でアニメーションさせてく
れる
### Linking/
Deep Linkをハンドリングする
### LinkingIOS/
Deep Linkをハンドリングする
### Lists/
Listコンポーネント郡
### Modal/
Modalのコンポーネント
### NativeAnimation/
Nativeのアニメーション系モジュールを読み込んでJS側から呼べるようにする
### Network/
Httpのメソッド郡 fetch, httpRequest等
### Performance/
デバッガー
### PermissionsAndroid/
Androidのパーミッション郡
### PushNotificationIOS/
ローカルプッシュ通知
### RCTTest/
?
### ReactNative/
ReactNativeのComponentsなどを呼んでいる
### Renderer/
レンダー部分
### Sample/
?サンプル
### Settings/
### Share/
共有（ボコンと下から出てくるクリップボードなどを）を呼び出すAPI
### Storage/
AsyncStrageについて
### StyleSheet/
### SurfaceBackedComponent/
C++で書かれている
### SurfaceHostingComponent/
C++で書かれている
### Text/
テキストコンポーネント
### Types/
イベントのflow型を定義している
### Utilities/
ユーティリティ郡
### Vibration/
バイブレーションを呼び出すAPI
### WebSocket/
WebSocketを扱うAPI ws.onmessage()等を使える
### Wrapper/
?
### YellowBox/
デバッグ時に下に出てくる黄色いコンポーネント
### fishhook/
釣り針
### polyfills/
polyfillsの実装
### react-native/
React Nativeのライブラリをimportできるようにする
getter郡
### Promise.js
### UTFSequence.js
UTFの特殊文字を定数で定義している
### promiseRejectionIsError.js

## ContainerShip/
## IntegrationTests/
## RNTester/
## React/
- ReactのVirtualDOMをAndroidネイティブビューに反映させ
る
- JSとJavaのブリッジ
- Native上でJSを動かす
## ReactAndroid/
### UIManagerModule/
Reactが差分を計算してReactShadowNode渡す、
ReactShadowNodeも差分を計算してUIManagerModuleに渡す
UIManagerModuleは受け取った差分を猫写する。
### NativeModules/
JSからJavaのモジュールを呼べるようにする
JSを実行できるようにする
#### ReactRootView/
#### ReactInstanceManager/
#### CatalystInstance/
### yoga/
レイアウトの計算をする
## ReactCommon/
## ReactNative/
空のディレクトリ
## babel-preset/
## bots/
## flow/
### $ReadOnly
## flow-github/
## gradle/wrapper/
## jest/
## keystores/
## lib/
## local-cli/
### AndroidのBuild
index.jsからindex.android.bundleを出力
## react-native-cli/
react-nativeコマンドの実装
react-nativeコマンドの実装
## react-native-git-upgrade/
## scripts/
## third-party-podspecs/

