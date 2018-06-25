# Android

## Android開発基礎
- https://www.amazon.co.jp/dp/4798152021/
類似の本なら何でもよかったのですが、比較的新しいことと、知り合いが監修しているということで挙げました。
by Nkznさん

### Androidを支える技術
- [第一章 Androidを支える技術〈Ⅰ〉──60fpsを達成するモダンなGUIシステム](http://gihyo.jp/book/2017/978-4-7741-8759-4)
第二章はあまり役立ちませんでしたが、第一章はAndroid側のViewの仕組みやスレッドの扱いが分かり、ReactAndroidの内部実装を読む際に非常に役立ちました。
by Nkznさん

### カスタムビューの知見を得る
- [Androidでxmlファイルを用いてカスタムViewを作る方法 | TechBooster](https://techbooster.org/android/7361/)
- [Android Viewをカスタマイズ(独自実装)する方法、ViewでのonTouchEventの使い方 | TechBooster](https://techbooster.org/android/application/3013/)
ネイティブコンポーネントを自作する場合は、Androidのカスタムビューに関する知識が必須です。
実はAndroidを支える技術の内容が理解できればカスタムビューも割と理解できるのですが、流石に低レイヤーすぎてハードルが高いので、もう少し高レイヤーな記事も紹介します。
by Nkznさん

### Android実践プログラミング - 現場で生まれた設計パターン
次々に開発環境が変わっていくものを体系的に学ぼうとするよりも、実用上の問題から学んだほうが早い場合があります。

アンチパターンやTIPS集としては、この巻は別格です。
実際に問題にぶつかったときには、大きな力になってくれるでしょう。
by Nkznさん

## ビルドツールへの理解を深める
RN製のプロジェクトをAndroidアプリとしてビルドする際には、通常のAndroidと同じくGradleの仕組みを用います。
そのため、Gradleの使い方を覚えるのは重要です。
by Nkznさん

### Gradle徹底入門 次世代ビルドツールによる自動化基盤の構築
- https://www.amazon.co.jp/dp/4798136433/
概要ならこれ。
ただ、この頃からGradleのメジャーバージョンが上がったりして、命名が変わっているところもあるので、↓のマイグレーションガイドも読んだほうがいいかもです。

Android Plugin for Gradle 3.0.0 への移行  |  Android Developers
前述の「Androidアプリ開発の教科書」のほうには最新のものが最低限は載っているので、もう少し広いところの考え方を知りたい場合には読んだほうがいい、という立ち位置の本になります。
by Nkznさん


### プログラミングGROOVY
- https://www.amazon.co.jp/dp/4774147273/
GradleはGroovyのシンタックスシュガーを全力で使ったDSLなので、文法としてはGroovyです。
Gradleを学びながら「え、なんでこの記法が許されてんの？」と疑問に思っても、Gradleの文脈では解説してもらえないことも多いです。
なので、少し遠回りになりますが、Groovyの文法を覚えてしまったほうが楽、みたいな部分があります。

懸念点として、GradleのDSLをGroovyベースではなくKotlinベースにする動きが進んでいます。
でもまあ、まだしばらくかかりそうですし、Kotlin製DSLはGroovy文化の影響を多大に受けているので、学んでおいて損はないと思い

## Kotlin
Android界のトップランナーたちは続々とKotlinへの移行を済ませています。有名カンファレンスに参加すると、スライド内にJavaのサンプルコードがほとんど出てこなくなったくらいです。
とはいえ、React Nativeの文脈ではKotlinが出てくることはないため、学ぶ意味はあるのか・・・？という部分もあります。

という前提で、「とはいえKotlin触りたいなー」という場合に読むといい本を紹介します。
by Nkzn


### Kotlin入門までの助走読本
- kotlin-prior-learning-book.pdf
サラッとKotlinを知るには良い教材です。
by Nkznさん

### Kotlin Koans
- Kotlin Koans - Kotlin Programming Language
一問一答でテストケースを通していくタイプの公式チュートリアルです。
オンライン版がおすすめ。
by Nkznさん

### Kotlinスタートブック -新しいAndroidプログラミング
- https://www.amazon.co.jp/dp/4865940391/
ちょっと古くなってきましたが、KotlinをAndroid内で活かすための示唆に富んだ本です。
by Nkznさん
