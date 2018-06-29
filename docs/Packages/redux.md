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
