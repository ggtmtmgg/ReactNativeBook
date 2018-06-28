# Redux

FluxというFacebookが提唱しているアーキテクチャの派生です。

Reactを使った開発においてReduxはデファクトスタンダードなので把握することが必須です。

0から学ぶ際には戸惑いますが、気合で乗り越れば手放せない存在になります。

## コアコンセプト
あなたのアプリの状態が単純なオブジェクトとして記述されているのを想像してください。例えば、todoアプリだと次のようになります。

```
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

このオブジェクトは、setterがないことを除くとモデルのようなものです。
これだと、様々な場所から状態を勝手に変更することができなくなり、バグが起こりにくくなります。



stateに何か変更を加えるためには、actionをdispatchする必要があります。
actionとは、何が起こるのかを記述するプレーンなJavaScriptオブジェクト(一切マジックがないことをおわかり頂けるだろうか?)です。
いくつかのアクションの例を以下に示します。

```
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```


すべての変更をactionとして記述するのを強制することで、アプリで何が起こっているのかを明確に把握できます。
何かが変われば、なぜ変わったのかがわかります。
actionは何が起こったのかのパンくずリストのようなものです。
最後に、stateとactionを結ぶためにreducerと呼ばれる関数を記述します。
繰りしますが、何も魔法はありません。reducer引数としてstateとactionを取り、変更後のstateを返します。
大きなアプリでこのような関数を書くのは難しいので、stateの部分ごとに管理する小さな関数を書きます。

```
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map(
        (todo, index) =>
          action.index === index
            ? { text: todo.text, completed: !todo.completed }
            : todo
      )
    default:
      return state
  }
}
```

また、対応するstateのkeyごとにreducerを呼び出すことでアプリ全体のstateを管理するためにもう一つのreducerを作ります。
```
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
}
```

これが基本的なReduxの考え方です。
Redux APIを一切使用してないことにお気づきいただけますでしょうか。
このパターンを容易にするユーティリティはいくつかありますが、主な考え方は、actionに応答してstateがどのように更新されるかを記述するだけです。
コードの90％がReduxやRedux APIやその他の魔法を使用しないただのJavaScriptです。

## 三つの原則

Reduxは、以下の3つの基本原則で描かれます。
"store", "state", "action"等見慣れない用語は後で説明します。

### 1. Single source of truth
直訳で「真実の単一ソース」。

アプリケーション全体の状態が、1つのstore内のオブジェクトツリー内に格納されます。
これにより、マルチデバイス向けのアプリを作りやすくなります。
サーバからの状態をシリアライズして、余分なコーディングすることなくクライアントサイドへhydrated(オブジェクトを使いやすい形に変える)することができます。
単一のツリーによって、アプリケーションのデバッグやインスペクトがしやすくなります。
開発サイクルを速くするために、開発中もアプリケーションの状態を維持することができます。
すべての状態が単一のツリーに格納されていれば、従来は実装が難しかった機能（元に戻す/やり直しなど）が簡単に実装できるようになります。

```
console.log(store.getState())

/* Prints
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*/
```

### 2. State is read-only
直訳で「Stateは読み込み専用」。

actionを投げることがstateを変える唯一の方法です。
これは、ビューもネットワークからのコールバックも直接stateに書き込むことはないことを確実にします。
代わりに、それらはstateを変更したい旨を表明します。
すべての変更は中央で管理され一つずつ厳格に起こるため、微妙な実行順序の条件に注意する必要はなくなります。
actionはプレーンなオブジェクトなので順番通りログに記録しておきデバッグやテストの目的で再実行することが可能です。

```
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

### 3. Changes are made with pure functions
直訳で「変更はピュアな関数で行われる」。

stateがにactionよってどのように変換されるかを指定するために、ピュアなreducerを書きます。
reducerは直前のstateとactionを受け取り次のstateを返すピュアな関数です。
最初は1つのreducer出始めてアプリが大きくなるに連れて小さなreducerに分割してstateツリーの特定の部分を管理することができます。

```
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
const reducer = combineReducers({ visibilityFilter, todos })
const store = createStore(reducer)
```

## React + Redux
Reactと合わせて使う実際の運用では、
component内でグローバルstateにアクセスしたい・必要なdispatcherを呼びたい等の問題を解決するために
react-reduxというパッケージを利用するのがデファクトスタンダードです。

### react-redux
react-reduxはReact公式のReduxのためのバインディング。
componentとstoreのつなぎ込みをします。
主にProviderとconnect()が重要なAPIです。

#### Provider ([公式](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#provider-store))

低い階層のcomponent内でconnect()を呼ぶことでRedux storeを使えるようにします。
通常、ルートのcomponentをラップすることなくconnect()を使用することはできません。
もし本当に必要であれば、手動でstoreをpropとしてすべてのconnect()されるcomponentにわたすこともできますが、
単体テストや不完全ではないコードでだけすることをお勧めします。

受け取るProps
- store (Redux Store): アプリの単一のRedux store
- children (React Element): ルートのcomponent

##### Providerで囲った子孫コンポーネント内でRedux stateの中身を扱うサンプル
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
    // connect()によってthis.props.userが渡されている
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

// Providerコンポーネントで囲っているおかげでconnect()を呼び出せる
// Appコンポーネント内でstate.userをpropsとして受け取れるようにする
export default connect(mapStateToProps)(App);
```

#### connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options]) ([公式](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options))
コンポーネントをRedux storeに接続する機能を提供するAPIです。
コンポーネント自体には変更を加えずに新しいコンポーネントクラスを返します。

引数
- [mapStateToProps(state, [ownProps]): stateProps] (Function):
  この引数を指定すると、connect()によって返される新しいコンポーネントはRedux storeの更新をsubscribeした状態になります。
  これは、storeが更新されるたびにmapStateToPropsが呼び出されることを意味します。
  mapStateToPropsの返り値は、コンポーネントのpropsにマージされる単純なオブジェクトでなければなりません。
  storeの更新をsubscribeしたくない場合は、mapStateToPropsの代わりにnullまたはundefinedを渡します。

  mapStateToProps関数が2つの引数を取る形で宣言されている場合、
  storeのstateが1つ目の引数として渡され、2つ目の引数としてpropsが渡されます。
  shallowEqual()によって変更があったとみなされたpropsを受け取った際に再呼び出しされます。(2番目の引数は通常、規約に従ってownPropsと呼ばれます)

  mapStateToProps関数の1つ目の引数はRedux storeのstate全体で、propsとして1つのオブジェクトを返します。
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

  もしmapDispatchToProps関数もしくはaction creatorのオブジェクトを渡さない場合、
  デフォルトのmapDispatchToPropsはコンポーネントのpropsにただdispatchへの挿入を実装です。

- [mergeProps(stateProps, dispatchProps, ownProps): props] (Function):
  もし渡せれたら、この関数はmapStateToProps()とmapDispatchToProps()の結果とpropsを渡されます。
  返り値のオブジェクトがラップされたコンポーネントのpropsとして渡されます。
  state
  おそらくpropsに応じてstateの一部を返したい場合や、propsに応じて特定の変数にaction creatorをバインドしたい場合にこの関数を使うでしょう。
  もし省略された場合は、デフォルトとして`Object.assign({}, ownProps, stateProps, dispatchProps)`が使われます。

- [options] (Object) If specified, further customizes the behavior of the connector. In addition to the options passable to connectAdvanced() (see those below), connect() accepts these additional options:

  [pure] (Boolean): If true, connect() will avoid re-renders and calls to mapStateToProps, mapDispatchToProps, and mergeProps if the relevant state/props objects remain equal based on their respective equality checks. Assumes that the wrapped component is a “pure” component and does not rely on any input or state other than its props and the selected Redux store’s state. Default value: true
  [areStatesEqual] (Function): When pure, compares incoming store state to its previous value. Default value: strictEqual (===)
  [areOwnPropsEqual] (Function): When pure, compares incoming props to its previous value. Default value: shallowEqual
  [areStatePropsEqual] (Function): When pure, compares the result of mapStateToProps to its previous value. Default value: shallowEqual
  [areMergedPropsEqual] (Function): When pure, compares the result of mergeProps to its previous value. Default value: shallowEqual
  [storeKey] (String): The key of the context from where to read the store. You probably only need this if you are in the inadvisable position of having multiple stores. Default value: 'store'

#### HOC - High-Order Component
#### compose

### Action
### Reducer
### Store
### dispatch
### conbine
### selector
#### reselect
## サンプルコード
- [reduxjs/redux](https://github.com/reduxjs/redux/tree/master/examples)
### counter
### async
### shopping-cart
## react-redux
- [reactjs/redux](https://github.com/reactjs/redux/tree/master/examples)
