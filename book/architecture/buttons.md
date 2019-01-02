<!--
# Buttons
-->
# ボタン

---
<!--
#### [Clone the code](https://github.com/evancz/elm-architecture-tutorial/) or follow along in the [online editor](https://ellie-app.com/37gVmD7Tm9Ma1).
 -->
#### [The Elm Architecture のチュートリアル用リポジトリをクローン](https://github.com/evancz/elm-architecture-tutorial/)するか[オンラインエディタ](https://ellie-app.com/37gVmD7Tm9Ma1)を使ってソースコードを見ていきましょう。
---

<!--
Our first example is a simple counter that can be incremented or decremented. I find that it can be helpful to see the entire program in one place, so here it is! We will break it down afterwards.
-->
最初のサンプルは値を増やしたり減らしたりできるシンプルなカウンターです。どんなプログラムなのか全体をまずお見せします！　プログラムの各処理についてはこの後見ていきましょう。

```elm
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)


main =
  Browser.sandbox { init = init, update = update, view = view }


-- MODEL

type alias Model = Int

init : Model
init =
  0


-- UPDATE

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

<!--
That's everything!
-->
以上が今回見ていくサンプルの全てです！

<!--
> **Note:** This section has `type` and `type alias` declarations. You can read all about these in the upcoming section on [types](/types/index.html). You do not *need* to deeply understand that stuff now, but you are free to jump ahead if it helps.
-->

> **Note:** この節では `type` と `type alias` 宣言が出てきます。これらの宣言については [型](/types/index.html) の節で知ることができます。今の時点で `type` や `type alias` のことを詳しく理解する必要はありませんが、もし気になるなら一足先に見ておくのもよいでしょう。

<!--
When writing this program from scratch, I always start by taking a guess at the model. To make a counter, we at least need to keep track of a number that is going up and down. So let's just start with that!
-->
このようなプログラムを書き始めるとき、私はいつもモデルを推測することから始めます。カウンターを作るのなら、少なくとも増減する数字を追跡する必要がありますね。それでは始めていきましょう。

```elm
type alias Model = Int
```

<!--
Now that we have a model, we need to define how it changes over time. I always start my `UPDATE` section by defining a set of messages that we will get from the UI:
-->
これでモデルができました。次にこのモデルが時間とともにどのように変化するかを定義する必要があります。この定義は The Elm Architecture における `UPDATE` のセクションで実施しますが、そのためにモデルが変化する契機となるメッセージのセットを定義することにします：
<!-- TODO ここの my `UPDATE` section はどう訳すのが適切か悩ましい。 -->

```elm
type Msg = Increment | Decrement
```

<!--
I definitely know the user will be able to increment and decrement the counter. The `Msg` type describes these capabilities as *data*. Important! From there, the `update` function just describes what to do when you receive one of these messages.
-->
ユーザがカウンターを増減できるだろうことは確実です。
ここで定義した `Msg` 型はこれらの機能を **データ** として記述します。
そしてそして！
`update` 関数ではこれらのメッセージの1つを受け取った時に何をするかを記述するのです。

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1
```

<!--
If you get an `Increment` message, you increment the model. If you get a `Decrement` message, you decrement the model. Pretty straight-forward stuff.
-->
もし `Increment` を受け取ったのであればカウンターである `model` を増加させます。
もし `Decrement` を受け取ったのであれば `model` を減少させます。
実に素直な動きです。

<!--
Okay, so that's all good, but how do we actually make some HTML and show it on screen? Elm has a library called `elm/html` that gives you full access to HTML5 as normal Elm functions:
-->
さて、カウンターの挙動を定義することはできましたが、実際にHTMLを作成して画面に表示するにはどうすればよいでしょうか？
Elmには `elm/html` というライブラリがあり、通常のElm関数としてHTML5にフルアクセスできます：

```elm
view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

<!--
One thing to notice is that our `view` function is producing a `Html Msg` value. This means that it is a chunk of HTML that can produce `Msg` values. And when you look at the definition, you see the `onClick` attributes are set to give out `Increment` and `Decrement` values. These will get fed directly into our `update` function, driving our whole app forward.
-->
`view` 関数が `Html Msg` を返していることに注目しましょう。
これは、`view` 関数の戻り値が `Msg` を生成できるHTML要素の集合であることを意味します。
さらに `view` 関数の定義に目を向けると、`onClick` 属性があり、値として `Increment` と `Decrement` が指定されています。
これらの値は `update` 関数へ直接渡され、そこからアプリ全体が動いていきます。

---
**訳注：** 訳すのがしんどくなってきたのでココからは「こんな感じのこと言ってる気がする」という **雰囲気訳** をお送りします。原文を引用の形で提示し、その後に雰囲気訳を提示します。

---

> Another thing to notice is that `div` and `button` are just normal Elm functions. These functions take (1) a list of attributes and (2) a list of child nodes. It is just HTML with slightly different syntax. Instead of having `<` and `>` everywhere, we have `[` and `]`. We have found that folks who can read HTML have a pretty easy time learning to read this variation. Okay, but why not have it be *exactly* like HTML? **Since we are using normal Elm functions, we have the full power of the Elm programming language to help us build our views!** We can refactor repetitive code out into functions. We can put helpers in modules and import them just like any other code. We can use the same testing frameworks and libraries as any other Elm code. Everything that is nice about programming in Elm is 100% available to help you with your view. No need for a hacked together templating language!

`view` 関数の内容ってHTMLによく似てるよね。
`div` や `button` は普通のElm関数で、いずれも第1引数に `属性のリスト` を、第2引数に `子ノードのリスト` を指定するようになってる。出てきてないけど他のHTMLタグっぽい関数も一緒だよ。

HTMLと違ってElmはプログラミング言語だから次のようなことができるよ。
- 繰り返し現れる同じような処理を関数へまとめる
- 他のコードと同じようにヘルパーをモジュールに入れてそのヘルパーをインポ​​ートする
- 他のElmで書かれたコードと同じテスティングフレームワークやライブラリを使う

つまりElmだとプログラミング言語のアプローチでビューを構築できるということだね。テンプレート言語で頑張らなくてもいいんだ。Elm万歳！

> There is also something a bit deeper going on here. **The view code is entirely declarative**. We take in a `Model` and produce some `Html`. That is it. There is no need to mutate the DOM manually. Elm takes care of that behind the scenes. This gives Elm [much more leeway to make optimizations](https://elm-lang.org/blog/blazing-fast-html) and ends up making rendering *faster* overall. So you write less code and the code runs faster. The best kind of abstraction!

もう少し深いことを言うと `view` 関数で定義するようなビューのコードは完全に宣言的なんだ。
`Model` を基に `Html` を決定する、これ。
DOM操作を頑張る必要はない。
Elmがよしなにやってくれる。
DOM操作の実装がElmの担当になっているおかげでその最適化もElmがやるし、最終的なレンダリングも高速になる。
プログラマがDOM操作に介入しない分だけ、書かれるコードは少なくなるし、変なDOM操作が発生しないから実行速度も速くなる。
プログラマは本当にやりたいことに集中できる。最高だよね。

> This pattern is the essence of The Elm Architecture. Every example we see from now on will be a slight variation on this basic pattern: `Model`, `update`, `view`.

このパターンが The Elm Architecture のエッセンスなんだ。
これから見ていくサンプルコードは `Model`、`update`、`view` からなる基本的なパターンを少し変えたものになってるよ。

> > **Exercise:** One cool thing about The Elm Architecture is that it is super easy to extend as our product requirements change. Say your product manager has come up with this amazing "reset" feature. A new button that will reset the counter to zero.
> >
> > To add the feature you come back to the `Msg` type and add another possibility: `Reset`. You then move on to the `update` function and describe what happens when you get that message. Finally you add a button in your view.
> >
> > See if you can implement the "reset" feature!

> **やってみよう：** The Elm Architecture のステキなところの1つは製品要件の拡張がやりやすいことだ。
> カウンターを0にリセットするための機能を追加したい、とプロダクトマネージャーが言い出したら、次のような流れになるはずだ。
> 1. `Msg` 型に `Reset` を追加する
> 2. `update` 関数に `Reset` を受け取った時の処理を追加する
> 3. `view` 関数にリセット用のボタンを追加する
> 
> 実際にリセット機能を実装できるかやってみよう！
