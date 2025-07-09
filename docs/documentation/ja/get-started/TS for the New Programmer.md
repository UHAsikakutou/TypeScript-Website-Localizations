---
title: プログラミング初心者のためのTypeScript
short: プログラミング初心者のためのTS
layout: docs
permalink: /ja/docs/handbook/typescript-from-scratch.html
oneline: ゼロからTypeScriptを学びましょう
---

あなたの最初のプログラミング言語として TypeScript を選んでいただきありがとうございます。
あなたはすでに素晴らしい選択をしています！

おそらく、すでに TypeScript は JavaScript の「フレーバー」「発展形」であるということを聞いたことがあると思います。
TypeScript (TS)と JavaScript (JS)の関係は、最近のプログラミング言語の中でも特殊なものとなっています。これを知ることで、JS に TS を追加する方法がよりわかりやすくなります。

## JavaScript って何だ？ 簡単な歴史

JavaScript (ECMAScript)は、ブラウザのためのシンプルなスクリプト言語として登場しました。
簡単な
当初は、ウェブページに埋め込まれた短いコードの断片に使われることを想定していました。
そのため、初期の Web ブラウザはこれらのコードをかなり低速に実行していました。
しかし時が経つにつれ、JS はますます普及し、Web 開発者はインタラクティブな体験を作るために JS を使い始めました。

Web ブラウザの開発者は、実行エンジンの最適化（動的コンパイル）と、JS でできることの拡張（API の追加）によって、この JS の使用率の増加に対応しました。
最近のウェブサイトでは、ブラウザは何十万行ものコードにまたがるアプリケーションを頻繁に実行しています。
これは、静的なページの単純なネットワークとして始まり、あらゆる種類のリッチな _アプリケーション_ のプラットフォームへと進化した「Web」の長く緩やかな成長でした。

それ以上に、JS は node.js を使った JS サーバーの実装など、ブラウザの枠を超えて活用されるほどに普及してきています。
JS の「どこでも実行できる」という性質は、クロスプラットフォーム開発にとって魅力的な選択肢となります。
最近では、JavaScript _だけ_ を使ってスタック全体をプログラムしている開発者もたくさんいます！

要約すると、私たちは、素早く使うために設計された言語が、何百万行ものアプリケーションを書くための本格的なツールに成長したということです。
どんな言語にも _癖_、つまり奇妙さや驚きがあるものですが、その起源のために、JavaScript にはこれらの癖が _たくさん_ 含まれています。以下がその例です。

- JavaScript の等しさを表す演算子(`==`)はオペランドを _強制_ するため、予期せぬ動作を引き起こします。

  ```js
  if ("" == 0) {
    // It is! But why??
  }
  if (1 < x < 3) {
    // True for *any* value of x!
  }
  ```

- JavaScript では、存在しないプロパティにアクセスすることもできてしまいます。

  ```js
  const obj = { width: 10, height: 15 };
  // Why is this NaN? Spelling is hard!
  const area = obj.width * obj.heigth;
  ```

ほとんどのプログラミング言語は、この種の誤りがあるとエラーが出ますが、中にはコンパイル中、つまり最初のコードが動き出す直前にエラーが出るものもあります。
小さなプログラムを書いているときは、このような癖は煩わしくとも何とかなりますが、何百行、何千行ものコードを持つアプリケーションを書くときには、このような不意打ちは深刻な問題となります。

## TypeScript: 静的型チェッカー

先ほど、バグがあるプログラムを一切実行させない言語があると述べました。
コードを実行しないままその誤りを検出する機能を _静的検査 (静的解析)_ といいます。
操作される値の種類に基づいて、何がエラーで何がエラーでないかを判断する機能を静的 _型_ 検査といいます。

TypeScript checks a program for errors before execution, and does so based on the _kinds of values_, making it a _static type checker_.
For example, the last example above has an error because of the _type_ of `obj`.
Here's the error TypeScript found:

TypeScript は、_静的型検査_ を行うことができます。つまり、実行前にプログラムの誤りをチェックし、 _値の種類_ に基づいてそれを行います。
以下のコードでは、`obj`の _型_ が間違っています。
TypeScript の指摘がこちらです。

```ts twoslash
// @errors: 2551
const obj = { width: 10, height: 15 };
const area = obj.width * obj.heigth;
```

### JavaScript の型機能付きスーパーセット

では、TypeScript は JavaScript とどのような関係があるのでしょうか？

#### 構文

TypeScript は JavaScript の _スーパーセット_ です。つまり、JS のコードはそのまま TS で動作します。
構文とは、プログラムを構成するためのテキストの書き方を指します。
たとえば、このコードには`)`がないため _構文_ エラーがあることになります。

```ts twoslash
// @errors: 1005
let a = (4
```

TypeScript では、どんな JavaScript コードでも、その構文が原因でエラーになることはありません。
つまり、どのような JavaScript コードであっても、その書き方を気にすることなく、 TypeScript ファイルとして記述できます。

#### 型

TypeScript は _型機能付き_ スーパーセットと述べましたが、これは異なる種類の値がどのようにして扱われるかに関するルールが追加されているということです。
先ほどの`obj.heigth`のエラーは、_構文_ エラーではなく、ある種類の値（ _型_ ）を間違った方法で使ったために発生したエラーです。

他の例を見てみましょう。以下の JavaScript のコードは、ブラウザで実行することができ、 _おそらく_ 何らかの値がログに残るでしょう。

```js
console.log(4 / []);
```

構文的には正しいこのプログラムは、 `Infinity` を出力します。
しかし、TypeScript は配列による数値の除算を無意味な操作とみなし、エラーを出します。

```ts twoslash
// @errors: 2363
console.log(4 / []);
```

もしかしたら、何が起きるか確認するために、_本当に_ 配列で数値を除算するつもりだったのかもしれません。しかし、大抵の場合これはプログラミングのミスです。
TypeScript の型チェッカーは、できるだけ多くの一般的なエラーを検出しつつ、正しいプログラムを通過させるように設計されています。
(後ほど、TypeScript がコードをチェックする際の厳密さの設定方法について説明します)。

JavaScript ファイルから TypeScript ファイルにコードを移動させると、コードの書き方によっては _型エラー_ が表示されることがあります。
この場合、コードに本当に問題があることも、TypeScript が過度に保守的なだけであることもあり得ます。。
このガイドでは、このようなエラーをなくすために、さまざまな TypeScript の構文を追加する方法を紹介します。

#### 実行時の動作

TypeScript は JavaScript の _実行時の動作_ を保証するプログラミング言語でもあります。
たとえば、JavaScript でゼロ除算(値を 0 で割ること)を行うと、ランタイムエラーが発生する代わりに`Infinity`が生成されます。
原則として、TypeScript は JavaScript コードの実行時の振る舞いを **絶対に** 変更しません。

JavaScript から TypeScript にコードを移動させた場合、TypeScript がそのコードに型エラーがあると考えたとしても、同じように実行されることが **保証** されています。

JavaScript と同じ実行時の挙動を維持することは、TypeScript の基本的な約束事です。なぜなら、これによってプログラムが動作しなくなるような微妙な違いを心配することなく、2 つの言語間を簡単に移行できるようになるからです。

<!--
Missing subsection on the fact that TS extends JS to add syntax for type
specification.  (Since the immediately preceding text was raving about
how JS code can be used in TS.)
-->

#### 消去される型

大雑把に言えば、TypeScript のコンパイラはコードのチェックを終えると、型を _削除_ して「コンパイル済み」のコードを生成します。
一度コードがコンパイルされると、型情報を持たないプレーンな JS が生成されます。

これはまた、TypeScript が推論した型に基づいてプログラムの _挙動_ が変わることがないことを指します。
要するに、コンパイル時に型エラーが表示されることはあっても、型システムそのものはプログラムが実行されたときにどのように動作するかということには関係がないということです。

そして最後に、TypeScript は追加のランタイムライブラリを提供しません。
プログラムは JavaScript と同じ標準ライブラリ（または外部ライブラリ）を使用するため、TypeScript 固有のフレームワークを学ぶ必要はありません。

<!--
Should extend this paragraph to say that there's an exception of
allowing you to use newer JS features and transpile the code to an older
JS, and this might add small stubs of functionality when needed.  (Maybe
with an example --- something like `?.` would be good in showing readers
that this document is maintained.)
-->

## JavaScript と TypeScript の学習

「JavaScript と TypeScript のどちらを学ぶべきか」という質問をよく目にします。

答えは、「JavaScript を学ばずに TypeScript を学ぶことはできない」です！
TypeScript は JavaScript と文法や実行時の動作を共有しているため、JavaScript について学ぶことは、同時に TypeScript を学ぶことにつながります。

プログラマが JavaScript を学ぶために利用できるリソースはたくさんあります。TypeScript を書くのであれば、これらのリソースを無視すべきでは _ありません_ 。
たとえば、`javascript`とタグ付けされた StackOverflow の質問は、`typescript`の約 20 倍もありますが、`javascript`の質問は _すべて_ TypeScript にも当てはまります。

もしあなたが「TypeScript でリストをソートする方法」のようなものを検索していたら、 **「TypeScript は JavaScript に型機能がついたランタイムである」**ということを覚えておいてください。
TypeScript でリストをソートする方法は、JavaScript でソートする方法と同じです。
TypeScript を直接使用しているリソースを見つけた場合は、それも素晴らしいことですが、実行時のタスクをどのように達成するかという日常的な質問に対して、TypeScript 固有の回答が必要だと考えて自分を縛らないでください。

## 次のステップ

以上、TypeScript で日常的に使われる構文やツールについて簡単に説明しました。
この次は、以下を参照してみてください。

- JavaScript の基礎を学ぶには、以下のどちらかをオススメします。

  - [Microsoft の JavaScript リソース](https://developer.microsoft.com/ja-jp/javascript) or
  - [Mozilla Web Docs の JavaScript ガイド](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide)

- 読み進める場合は：[JavaScript プログラマのための TypeScript](/ja/docs/handbook/typescript-in-5-minutes.html)
- ハンドブック全体を見るには： [はじめからおわりまで](/ja/docs/handbook/intro.html)
- いくつかの例を見るには： [プレイグラウンド](/play#show-examples)

<!-- Note: I'll be happy to write the following... -->
<!--
## Types

    * What's a type? (For newbies)
      * A type is a *kind* of value
      * Types implicitly define what operations make sense on them
      * Lots of different kinds, not just primitives
      * We can make descriptions for all kinds of values
      * The `any` type -- a quick description, what it is, and why it's bad
    * Inference 101
      * Examples
      * TypeScript can figure out types most of the time
      * Two places we'll ask you what the type is: Function boundaries, and later-initialized values
    * Co-learning JavaScript
      * You can+should read existing JS resources
      * Just paste it in and see what happens
      * Consider turning off 'strict' -->
