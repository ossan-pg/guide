<!--
# Forms
-->
# フォーム

---
<!--
#### [Clone the code](https://github.com/evancz/elm-architecture-tutorial/) or follow along in the [online editor](https://ellie-app.com/37gWB93n8jJa1).
-->
#### [The Elm Architecture のチュートリアル用リポジトリをクローン](https://github.com/evancz/elm-architecture-tutorial/)するか[オンラインエディタ](https://ellie-app.com/37gWB93n8jJa1)を使ってソースコードを見ていきましょう。
---

<!--
Here we will make a rudimentary form. It has a field for your name, a field for your password, and a field to verify that password. We will also do some very simple validation (do the two passwords match?) just because it is simple to add.
-->
初歩的なフォームを作ります。
名前用のフィールド、パスワード用のフィールド、そしてパスワード確認用のフィールドを持つフォームです。
簡単なのでとても単純な検証 (2つのパスワードが一致するかどうか) も実装します。

<!--
The code is a bit longer in this case, but I still think it is valuable to look through it before you get into the description of what is going on.
-->
今回はコードが少し長くなりますが、説明の前にコードを一通り確認することは有意義なので見ていきましょう。

```elm
import Browser
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)



-- MAIN


main =
  Browser.sandbox { init = init, update = update, view = view }



-- MODEL


type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }


init : Model
init =
  Model "" "" ""



-- UPDATE


type Msg
  = Name String
  | Password String
  | PasswordAgain String


update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }



-- VIEW


view : Model -> Html Msg
view model =
  div []
    [ viewInput "text" "Name" model.name Name
    , viewInput "password" "Password" model.password Password
    , viewInput "password" "Re-enter Password" model.passwordAgain PasswordAgain
    , viewValidation model
    ]


viewInput : String -> String -> String -> (String -> msg) -> Html msg
viewInput t p v toMsg =
  input [ type_ t, placeholder p, value v, onInput toMsg ] []


viewValidation : Model -> Html msg
viewValidation model =
  if model.password == model.passwordAgain then
    div [ style "color" "green" ] [ text "OK" ]
  else
    div [ style "color" "red" ] [ text "Passwords do not match!" ]
```

<!--
This is pretty similar to our [text field example](text_fields.md), just with more fields. Let's walk through how it came to be!
-->
前の節で扱った [テキストフィールドのサンプル](text_fields.md) とよく似ていますが、フィールドが増えています。
どうしてそうなっているのか、見ていきましょう。

<!--
As always, you start out by guessing at the `Model`. We know there are going to be three text fields, so let's just go with that:
-->
いつものようにモデルを推測するところから始めていきます。
3つのテキストフィールドがあることを踏まえると、モデルは次のようになるでしょう：

```elm
type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }
```

<!--
Great, seems reasonable. We expect that each of these fields can be changed separately, so our messages should account for each of those scenarios.
-->
いい感じですね。
これらのフィールドは個別に変更されるはずなので、変更時のイベントのトリガーとなるメッセージについてもそれぞれ対応するものを定義します。

```elm
type Msg
  = Name String
  | Password String
  | PasswordAgain String
```

<!--
This means our `update` is pretty mechanical. Just update the relevant field:
-->
`update` 関数の内容も単純に決まります。
メッセージに対応するモデル内のフィールドを更新するだけです。

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }
```

<!--
We get a little bit fancier than normal in our `view` though.
-->
`view` 関数については少し変わった定義になります。

```elm
view : Model -> Html Msg
view model =
  div []
    [ viewInput "text" "Name" model.name Name
    , viewInput "password" "Password" model.password Password
    , viewInput "password" "Re-enter Password" model.passwordAgain PasswordAgain
    , viewValidation model
    ]
```

<!--
We start by creating a `<div>` with four child nodes. But instead of using functions from `elm/html` directly, we call Elm functions to make our code more concise! We start with three calls to `viewInput`:
-->
4つのノードを持つ `<div>` を作ります。
ただし `elm/html` モジュールの関数を直接使用するのではなく、コードを簡潔にするため自前で実装したElmの関数を呼び出します。

`div` 内の1番目から3番目のノードを作るために呼び出している `viewInput` 関数から見ていきます。

```elm
viewInput : String -> String -> String -> (String -> msg) -> Html msg
viewInput t p v toMsg =
  input [ type_ t, placeholder p, value v, onInput toMsg ] []
```

<!--
So `viewInput "text" "Name" model.name Name` can create a node like `<input type="text" placeholder="Name" value="Bill">`. That node will also send messages like `Name "Billy"` to `update` on user input.
-->
この関数により `viewInput "text" "Name" model.name Name` を実行すると `<input type="text" placeholder="Name" value="Bill">` のようなノードを作成します (訳注1) 。
また、このノードはユーザ入力により `Name "Billy"` のようなメッセージを `update` 関数に送ります。

- 訳注1：ユーザが1番目のテキストフィールドに "Bill" と入力した状況を想定。
- 訳注2：ユーザが1番目のテキストフィールドに "Billy" と入力した状況を想定。

<!--
The fourth entry is more interesting. It is a call to `viewValidation`:
-->
4番目に呼び出している `viewValidation` 関数は少し面白いです：

```elm
viewValidation : Model -> Html msg
viewValidation model =
  if model.password == model.passwordAgain then
    div [ style "color" "green" ] [ text "OK" ]
  else
    div [ style "color" "red" ] [ text "Passwords do not match!" ]
```

<!--
This function first compares the two passwords. If they match, you get green text and a positive message. If they do not match, you get red text and a helpful message.
-->
この関数はまず2つのパスワードを比較します。
そして、もしパスワードが一致していたら、一致したことを示すメッセージを緑色で表示します。
もし一致していなかったら、一致していないことを示すメッセージを赤色で表示します。

<!--
These helper functions begin to show the benefits of having our HTML library be normal Elm code. We _could_ put all that code into our `view`, but making helper functions is totally normal in Elm, even in view code. Is this getting hard to understand? Maybe I can break out a helper function!
-->
これらのヘルパー関数はHTMLのライブラリが通常のElmコードであることの利点を示しています。
ボタンやテキストフィールドのサンプルでは `view` 関数内でライブラリの関数を直接呼び出していました。
一方、今回のサンプルではライブラリの関数を組み合わせたヘルパー関数を作り、それを `view` 関数内で呼び出しました。
ビューのコードであっても、常にHTMLのような記法である必要はないのです。
<!-- この部分の訳が文脈的によく分からない＋恐らくなくても問題ないのでカットした。
Is this getting hard to understand? Maybe I can break out a helper function!
-->

<!---->
> **Exercises:** One cool thing about breaking `viewValidation` out is that it is pretty easy to augment. If you are messing with the code as you read through this (as you should be!) you should try to:
>
>  - Check that the password is longer than 8 characters.
>  - Make sure the password contains upper case, lower case, and numeric characters.
>  - Add an additional field for `age` and check that it is a number.
>  - Add a `Submit` button. Only show errors *after* it has been pressed.
>
> Be sure to use the helpers in the [`String`](https://package.elm-lang.org/packages/elm/core/latest/String) module if you try any of these! Also, we need to learn more before we start talking to servers, so make sure you read all the way to the HTTP part before trying that. It will be significantly easier with proper guidance!

<!---->
> **Note:** It seems like efforts to make generic validation libraries have not been too successful. I think the problem is that the checks are usually best captured by normal Elm functions. Take some args, give back a `Bool` or `Maybe`. E.g. Why use a library to check if two strings are equal? So as far as we know, the simplest code comes from writing the logic for your particular scenario without any special extras. So definitely give that a shot before deciding you need something more complex!
