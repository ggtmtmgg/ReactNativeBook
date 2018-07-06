# React Navigation v2
## react-navigationの概要

アプリライクな画面遷移の実装を楽にしてくれるライブラリ。
名前はreact-navigationだが React Native のためのライブラリ。

## その他のNavigationライブラリとの比較

Navigationライブラリは、 大きく**naviteプラットフォームベース**と**JSベース**なものに分けられます。

nativeベースなものはフィードバックのアニメーションが速くて表現もネイティブのものに近くなる傾向があり、
JSベースは導入やカスタマイズをすべてJS上で解決できるので実装が楽な傾向があります。

native-navigationのREADME(意訳)によると

> native-navigationは作られたがまだbeta版です。 プロダクション用のnavigationとしてnaitiveプラットフォームベースのnavigationを使いたいのであれば、react-native-navigationを見てみるのがおすすめです。 JSベースの解決策で充分なのであれば、react-navigationがおすすめです。

といった感じです。

### Navigation系ライブラリの比較表(2018/7/5 現在)
| | Star数    | ベース | 特徴                         |
|--------------------------|--------|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| react-native-router-flux | ★7221   | JSベース                     | ・react-navigationより昔からある                                                                                                       |
| react-navigation         | ★12431  | JSベース                     | ・公式ドキュメントの"Navigating Between Screens"という項で使い方を紹介されている<br>・2018/3月にv2.0.0がリリースされてから4ヶ月でv2.4.1までリリースされている |
| react-native-navigation  | ★7857   | Nativeプラットフォームベース | ・iOSとAndroidプラットフォームのナビゲーションコンポーネント上に構築されている                                                         |
| native-navigation        | ★3008   | Nativeプラットフォームベース | ・iOSとAndroidプラットフォームのナビゲーションコンポーネント上に構築されている<br>・airbnb製でまだbeta版                                   |
## React Navigation v2

### Router
TODO: 編集


### Navigation
全screenにprops.navigationの形で渡ってくるプロパティ。 navigationの操作に必要なものが詰まっている。

- `navigate()`: 別のスクリーンへ行く。必要であればスクリーンのインスタンスをつくる
- `goBack()`: 現在のアクティブなスクリーンを閉じて、手前のスタックに戻る
- `addListener()`: navigationのライフサイクルにアップデートを登録する
  - イベントはwillBlur, willFocus, didFocus, didBlurがある
- `isFocused()`: スクリーンがフォーカスされていれば `true` を返す
- `state`: 今の state/route の情報を返す。 routeNameやparams等が入っている。Reactのstateとはちょっと違った概念に見える。
- `setParams()`: routesのparamsを変更する
- `getParam()`: fallback付きで特定のparamを取得しようとする
- `dispatch()`: routerへactionを送る

#### StackNavigationでのみ渡されるもの

- push()
- pop()
- popToTop()
- replace()

### NavigationActions
navigationの操作をするActionの定義。navigation.dispatch()を使って、routerに送って実行する。

#### 例

```
const backAction = NavigationActions.back({ key: 'Profile' });
this.props.navigation.dispatch(backAction);
```

### アクション

- `Navigate`: 他のrouteへ移動する
- `Back`: 直前の状態に戻る
- `Set Params`: routeにparamsをセットする
- `Init`: stateが未定義な時にstateをイニシャライズするのに使う

#### StackActionsのみ持つアクション
- Reset
- Replace
- Push
- Pop
- PopToTop

###  Navigator

- `StackNavigator`: screenをstackしていくNavigator
- `SwitchNavigator`: 一時的なスクリーンを表示するNavigator。 サインインのフローを想定して作られている。
- `DrawerNavigator`: Drawerを表示すうるNavigator。 Drawerは左側から画面横幅60%くらいでメニューの用のものがでてくるやつ。
- `TabNavigator`: タブでスクリーンを切り替えれるNavigator
- `BottomTabNavigator`: TabNavigatorのタブが画面下にあるNavigator


## サンプル
### スイッチの形
### スタック > タブの形
