# react-redux
react-reduxはReact公式のReduxのためのバインディングです。
component内でグローバルstateにアクセスしたい・必要なdispatcherを呼びたい等の問題を解決するために、
componentとstoreのつなぎ込みをします。
主にProviderと`connect()`の役割を抑えておきたいです。

## Provider ([公式](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#provider-store))

低い階層のcomponent内で`connect()`を呼ぶことでRedux storeを使えるようにします。
通常、ルートのcomponentをラップすることなく`connect()`を使用することはできません。
もし本当に必要であれば、手動でstoreをpropとしてすべての`connect()`されるcomponentにわたすこともできますが、
単体テストや不完全ではないコードでだけすることをお勧めします。

受け取るProps
- store (Redux Store): アプリの単一のRedux store
- children (React Element): ルートのcomponent

### Providerで囲った子孫コンポーネント内でRedux stateの中身を扱うサンプル
簡略のためimport文等を省いております。

```Root.js
// 複数の小さなreducerを結合してルートのreducerを作る
const reducers = combineReducers({
  user: UserReducer, // Userに関するstateを操作するreducer
  coins: CoinsReducer // Coinに関するstateを操作するreducer
})
// reducerを元にルートのstoreを生成
const store = createStore(reducers)

export default function Root() {
  return (
    <Provider store={store}>
      <App />
    </Provider>
  )
}
```

```App.js
class App extends Component {
  render() {
    // `connect()`によってthis.props.userが渡されている
    const { user: { name } } = this.props
    return (
      <Text>{ name }</Text>
    )
  }
}

// redux stateツリー内のstate.user以下を返す
function mapStateToProps(state) {
  return { user: state.user }
}

// Providerコンポーネントで囲っているおかげで`connect()`を呼び出せる
// Appコンポーネント内でstate.userをpropsとして受け取れるようにする
export default connect(mapStateToProps)(App);
```

## connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options]) ([公式](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options))
コンポーネントをRedux storeに接続する機能を提供するAPIです。
コンポーネント自体には変更を加えずに新しいコンポーネントクラスを返します。

引数
- [mapStateToProps(state, [ownProps]): stateProps] (Function):
  この引数を指定すると、`connect()`によって返される新しいコンポーネントはRedux storeの更新をsubscribeした状態になります。
  これは、storeが更新されるたびに`mapStateToProps()`が呼び出されることを意味します。
  `mapStateToProps()`の返り値は、コンポーネントのpropsにマージされる単純なオブジェクトでなければなりません。
  storeの更新をsubscribeしたくない場合は、`mapStateToProps()`の代わりにnullまたはundefinedを渡します。

  `mapStateToProps()`関数が2つの引数を取る形で宣言されている場合、
  storeのstateが1つ目の引数として渡され、2つ目の引数としてpropsが渡されます。
  `shallowEqual()`によって変更があったとみなされたpropsを受け取った際に再呼び出しされます。(2番目の引数は通常、規約に従ってownPropsと呼ばれます)

  `mapStateToProps()`関数の1つ目の引数はRedux storeのstate全体で、propsとして1つのオブジェクトを返します。
  これはよくselectorと呼ばれます。
  [reselect](https://github.com/reduxjs/reselect)を使って効率的にselector群を結合し、派生データを計算できます。

- [mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function):
  もしオブジェクトが渡されたら、その中のそれぞれの関数がaction creatorになります。
  同じ関数名を持つが、おそらく直接呼び出すためにすべてのaction creatorがdispatch内にラップされたオブジェクトがコンポーネントのpropsにマージされます。
  (// TODO: 日本語おかしいので治す)

  もし関数が渡されたら、それは1つ目の引数として与えられたdispathになります。
  自分の方法でaction creatorをバインドするために何らかの形でdispatchを使うobjectを返しても良いです。

  もし2つの引数を受け取るように宣言されたら、1つ目の引数としてdispatch、2つ目の引数として接続されたコンポーネントのpropsを受け取ります。
  そして、新しいpropsを受け取った際に再呼び出しされます。(2番目の引数は通常、規約に従ってownPropsと呼ばれます)

  もし`mapDispatchToProps()`関数もしくはaction creatorのオブジェクトを渡さない場合、
  デフォルトの`mapDispatchToProps()`はコンポーネントのpropsにただdispatchへの挿入を実装です。

- [mergeProps(stateProps, dispatchProps, ownProps): props] (Function):
  もし渡せれたら、この関数は`mapStateToProps()`と`mapDispatchToProps()`の結果とpropsを渡されます。
  返り値のオブジェクトがラップされたコンポーネントのpropsとして渡されます。
  state
  おそらくpropsに応じてstateの一部を返したい場合や、propsに応じて特定の変数にaction creatorをバインドしたい場合にこの関数を使うでしょう。
  もし省略された場合は、デフォルトとして`Object.assign({}, ownProps, stateProps, dispatchProps)`が使われます。

- [options] (Object)
  もし渡されたら、connectorの振る舞いを更にカスタマイズします。
  `connectAdvanced()`に渡せる引数に加えて、`connect()`は以下の引数を受け取ることができます:

  - [pure] (Boolean): もし`true`で、対応するstateとpropsがそれぞれのチェックに基づいて変更がなければ、`connect()` 再renderと`mapStateToProps()`と`mapDispatchToProps()`とmergePropsの呼び出しを避けます。
  - [areStatesEqual] (Function): pureが`true`のとき、store stateを直前の値と比べる関数。デフォルト: strictEqual(===)
  - [areOwnPropsEqual] (Function): pureが`true`のとき、渡されるpropsを直前の値と比べる関数。デフォルト: shallowEqual(===)
  - [areStatePropsEqual] (Function): pureが`true`のとき、`mapStateToProps()`の返り値を直前の値と比べる関数。デフォルト: shallowEqual(===)
  - [areMergedPropsEqual] (Function): pureが`true`のとき、mergePropsの返り値を直前の値と比べる関数。デフォルト: shallowEqual(===)
  - [storeKey] (String): storeのどこを読み込むかのcontextのkey。複数のstoreを持つことに優位がない場合に必要になりそうです。デフォルト: 'store'
