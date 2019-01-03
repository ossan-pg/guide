<!--
# Text Fields
-->
# テキストフィールド

---
<!--
#### [Clone the code](https://github.com/evancz/elm-architecture-tutorial/) or follow along in the [online editor](https://ellie-app.com/37gW7sj9wPVa1).
-->
#### [The Elm Architecture のチュートリアル用リポジトリをクローン](https://github.com/evancz/elm-architecture-tutorial/)するか[オンラインエディタ](https://ellie-app.com/37gW7sj9wPVa1)を使ってソースコードを見ていきましょう。
---

<!--
We are about to create a simple app that reverses the contents of a text field.
-->
テキストフィールドの文字列の順序を逆にする簡単なアプリを作成しようとしています。

<!--
Again this is a pretty short program, so I have included the whole thing here. Skim through to get an idea of how everything fits together. Right after that we will get into the details!
-->
これもかなり短いプログラムなので、ここですべてを取り上げました。
どんな構成か把握するため一読してください。
その後詳細に見ていきます。

```elm
import Browser
import Html exposing (Html, Attribute, div, input, text)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)



-- MAIN


main =
  Browser.sandbox { init = init, update = update, view = view }



-- MODEL


type alias Model =
  { content : String
  }


init : Model
init =
  { content = "" }



-- UPDATE


type Msg
  = Change String


update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }



-- VIEW


view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", value model.content, onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

<!--
This code is a slight variant of the counter from the previous section. You set up a model. You define some messages. You say how to `update`. You make your `view`. The difference is just in how we filled this skeleton in. Let's walk through that!
-->
このコードは、前の節で扱ったカウンターのコードを少しだけ変えたものです。
モデル、メッセージ、`update` 関数、`view` 関数をそれぞれ定義しています。
違いは、このスケルトンをどのように埋めたかという点だけです。それを見ていきましょう。

<!--
As always, you start by guessing at what your `Model` should be. In our case, we know we are going to have to keep track of whatever the user has typed into the text field. We need that information so we know how to render the reversed text.
-->
いつものように、モデルがどうあるべきかを推測することから始めます。
この場合、ユーザがテキストフィールドに入力した内容を追跡する必要があることがわかりました。
となると、その内容を表現する方法が必要です。

```elm
type alias Model =
  { content : String
  }
```

<!--
This time I chose to represent the model as a record. (You can read more about records [here](https://guide.elm-lang.org/core_language.html#records) and [here](https://elm-lang.org/docs/records).) For now, the record stores the user input in the `content` field.
-->
今回はモデルをレコードとして表現することにしました。
とりあえず、レコードはユーザの入力を `content` フィールドに格納します。

<!--
> **Note:** You may be wondering, why bother having a record if it only holds one entry? Couldn't you just use the string directly? Sure! But starting with a record makes it easy to add more fields as our app gets more complicated. When the time comes where we want *two* text inputs, we will have to do much less fiddling around.
-->
> **Note:** 1つの項目だけを保持するために、なぜわざわざレコードを用意するのか？ 文字列を直接モデルにすればいいのではないか？ と疑問に思うかもしれません。
もっともな話です！
が、レコードとして定義しておくと、アプリが複雑になった場合でもモデルを拡張するためにフィールドを追加するだけでよく、変更がしやすいのです。
例えば今回のアプリでテキスト入力を2つに増やしたい場合でも、レコードで定義しているおかげで拡張の手間はとても小さくなります。

<!--
Okay, so we have our model. Now in this app there is only one kind of message really. The user can change the contents of the text field.
-->
…というわけでモデルについては用意できました。
今このアプリでは一種類のメッセージしかありません。
そしてユーザはテキストフィールドの内容を変更できるので、次のようなメッセージの型を定義しましょう。

```elm
type Msg
  = Change String
```

<!--
This means our update function just has to handle this one case:
-->
`view` 関数が処理しなければならないメッセージはこの1つのケースのみということになります：

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }
```

<!--
When we receive new content, we use the record update syntax to update the contents of `content`.
-->
新しい文字列を受け取ったら、レコード更新の構文 `{ record | field = newValue }` を使って `content` の内容を更新します。

<!--
Finally we need to say how to view our application:
-->
最後にアプリケーションのビューを定義します。

```elm
view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", value model.content, onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

<!--
We create a `<div>` with two children.
-->
2つの子ノードをもつ `div` 要素を作りました。

<!--
The interesting child is the `<input>` node. In addition to the `placeholder` and `value` attributes, it uses `onInput` to declare what messages should be sent when the user types into this input.
-->
気になるのは `input` ですね。
`placeholder` と `value` 属性に加えて、ユーザがテキスト入力したときにどのメッセージを送信するか宣言するために `onInput` を使用しています。

<!--
This `onInput` function is kind of interesting. It takes one argument, in this case the `Change` function which was created when we declared the `Msg` type:
-->
この `onInput` 関数はちょっとおもしろいです。
これは1つの引数ーーこの場合は `Msg` 型を宣言したときに作成された `Change` 関数ーーを取ります。

```elm
Change : String -> Msg
```

<!--
This function is used to tag whatever is currently in the text field. So let's say the text field currently holds `glad` and the user types `e`. This triggers an `input` event, so we will get the message `Change "glade"` in our `update` function.
-->
この `Change` 関数は現在テキストフィールドに入力されているものにタグを付けるために使用されます。
たとえば、テキストフィールドに現在 `glad` が入力されていて、ユーザが `e` を入力したとしましょう。
これは入力イベントのトリガーとなり、`update` 関数に `Change "glade"` というメッセージが渡されます。

<!--
So now we have a simple text field that can reverse user input. Neat! Now on to putting a bunch of text fields together into a more traditional form.
-->
以上でユーザが入力した文字列を反転できる単純なテキストフィールドができました。
実際のアプリではもう少しきちんとした入力処理を扱います。
次の節では複数のテキストフィールドをまとめた入力フォームを作っていきましょう。
