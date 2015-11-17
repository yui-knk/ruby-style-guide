# Ruby コーディング規約

これは [マネーフォワード][moneyforward-corp] Ruby コーディング規約です.

これらを、お手本（ベース）にしています 

* [Bozhidar Batsov's 規約(これを元にしています)][bbatsov-ruby] 
* [Github規約][github-ruby]
* [クックパッド規約][cookpad-styleguide] 
* [コーディング規約をまとめてみた (Ruby編)][bojovs-com]

次の目標は、Railsコーディング規約です.
 
* [マネーフォワード 規約(Rails版)][moneyforward-rails]

以下は、ドメインの前提条件です
* Rubyのバージョンは2.1以降を前提とする（バージョンの異なるプロダクトが無い為）
 * 複数の表記がある場合、新しく採用された表記を強く推奨します
* 複数の同じ振る舞いのメソッドやシンタックスシュガーが存在する場合、短い方を利用します
 * ex) mapとcollectは双方の価値を認めつつ、mapを採用します
 * ex) []とArray.newは双方の価値を認めつつ、[]を採用します
* 強い理由がない場合は統一性の為合わせてください。これは可読性を担保する為です。
 * 本規約に沿わない表記を否定するものではありません
* 他のメンバーを説得出来る強い理由がある場合は、理由を説明した上で任意の表記を行ってください
 * これに該当するケースでは、本規約へのフィードバックを検討してください

## 目次

* [レイアウト](#レイアウト)
* [構文](#構文)
* [命名規則](#命名規則)
* [コメント](#コメント)
  * [注釈](#注釈)
* [クラス](#クラス)
* [例外](#例外)
* [変数](#変数)
* [Collections](#collections)
* [文字列](#文字列)
* [正規表現](#正規表現)
* [代入式](#代入式)
* [パーセントリテラル](#パーセントリテラル)
* [ブロック](#ブロック)
* [メタプログラミング](#メタプログラミング)
* [雑則](#雑則)
* [ツール](#ツール)

## レイアウト
* <a name="utf-8"></a>
  ソースファイルのエンコーディングには`UTF-8`を用いましょう。
 * ソースコード中にマジックコメントが書かれることはありません
<sup>[[link](#utf-8)]</sup>

* <a name="spaces-indentation"></a>
  インデントには **スペース** 2つ(別名ソフトタブ)。 ハードタブを用いてはいけません。
<sup>[[link](#spaces-indentation)]</sup>

  ```Ruby
  # 悪い例 - 4つのスペース
  def some_method
      do_something
  end

  # 良い例
  def some_method
    do_something
  end
  ```

* <a name="crlf"></a>
  Unix-styleの改行にしましょう。
(*BSD/Solaris/Linux/OS X ユーザーはデフォルトで設定されています。
  Windows ユーザーは特に注意が必要です。)
<sup>[[link](#crlf)]</sup>

  * もしGitを使っていれば、プロジェクトにWindowsの改行が紛れ込まないように、以下の設定を追加したほうがよいかもしれません:

    ```bash
    $ git config --global core.autocrlf true
    ```

* <a name="no-semicolon"></a>
  命令文や式の区切りに`;`を用いてはいけません。
  当然、１行につき式１つにしましょう。
<sup>[[link](#no-semicolon)]</sup>

  ```Ruby
  # 悪い例
  puts 'foobar'; # 余分なセミコロンです。

  puts 'foo'; puts 'bar' # ２つの式が１行にあります。

  # 良い例
  puts 'foobar'

  puts 'foo'
  puts 'bar'

  puts 'foo', 'bar' # 特にputsでは適用されます。
  ```

* <a name="single-line-classes"></a>
  本文のないクラスは１行のフォーマットが好まれます。
<sup>[[link](#single-line-classes)]</sup>

  ```Ruby
  # 悪い例
  class FooError < StandardError
  end

  # 悪くない例
  class FooError < StandardError; end

  # 良い例
  FooError = Class.new(StandardError)
  ```

* <a name="no-single-line-methods"></a>
  １行のメソッドは避けましょう。
  いくらか使われているところもありますが、
  それらの定義構文の仕様が望ましくないとさせるいくつかの特殊性があります。
  ともかく - １行メソッドには多くとも式１つまでにすべきです。
<sup>[[link](#no-single-line-methods)]</sup>

  ```Ruby
  # 悪い例
  def too_much; something; something_else; end

  # 悪くない例 - 最初の ; は必要です。
  def no_braces_method; body end

  # 悪くない例 - ２つ目の ; は任意です。
  def no_braces_method; body; end

  # 悪くない例 - 文法上は正しいです。ただし、; がない記述は少し読みづらいです。
  def some_method() body end

  # 良い例
  def some_method
    body
  end
  ```

  本文が空のメソッドはこのルールの例外です。

  ```Ruby
  # 良い例
  def no_op; end
  ```

* <a name="spaces-operators"></a>
  演算子の前後、コンマ、コロン、セミコロンの後ろに、`{`の前後、`}`の前にはスペースを入れましょう。
  スペースはRubyのインタープリタには(ほとんどの場合)重要ではありませんが、
  スペースの適切な使用は、読みやすいコードを容易に書くための鍵です。
<sup>[[link](#spaces-operators)]</sup>

  ```Ruby
  sum = 1 + 2
  a, b = 1, 2
  1 > 2 ? true : false; puts 'Hi'
  [1, 2, 3].each { |e| puts e }
  ```

  演算子についてただひとつの例外は、指数演算子です:

  ```Ruby
  # 悪い例
  e = M * c ** 2

  # 良い例
  e = M * c**2
  ```

  `{` と `}`は、構文の明確化のために有用です。
  だから、文字列に式を埋め込む時と同様に、
  ブロックやハッシュ構文に使われます。
  ハッシュ構文には、２つのスタイルが許容できます。

  ```Ruby
  # 良い例 - スペースを { の後と } の前に入れる
  { one: 1, two: 2 }

  # 良い例 - スペースを { の後と } の前に入れない
  {one: 1, two: 2}
  ```

  １つ目の構文は、わずかながら少し読みやすいです(そして、ほぼ間違いなく一般的なRubyコミュニティで人気があります)。
  ２つ目の構文は、ブロックとハッシュを視覚的に差別化できるという点で有利です。
  どちらでも片方を採用すれば、常に同じ構文を採用しましょう。

  文字列に埋め込む構文も、２つのスタイルが許容できます:

  ```Ruby
  # 良い例 - スペースを入れない
  "string#{expr}"

  # 良い例 - ほぼ間違いなくにこちらのほうが読みやすい
  "string#{ expr }"
  ```

  １つ目の式は、他の式よりも非常に人気があり、一般的にこちらを使うように進められる書き方です。
  一方２つ目は、(間違いなく)少し読みやすいです。
  ハッシュと同じように - 片方を採用すれば、常に同じ方を採用しましょう。

* <a name="no-spaces-braces"></a>
  `(`、 `[`の後と、`]`、 `)`の前にはスペースは入れません。
<sup>[[link](#no-spaces-braces)]</sup>

  ```Ruby
  some(arg).other
  [1, 2, 3].size
  ```

* <a name="no-space-bang"></a>
  `!`の後にはスペースは入れません。
<sup>[[link](#no-space-bang)]</sup>

  ```Ruby
  # 悪い例
  ! something

  # 良い例
  !something
  ```

* <a name="no-space-inside-range-literals"></a>
  範囲リテラルの内側にスペースは不要です。
<sup>[[link](#no-space-inside-range-literals)]</sup>

    ```Ruby
    # 悪い例
    1 .. 3
    'a' ... 'z'

    # 良い例
    1..3
    'a'..'z'
    ```

* <a name="indent-when-to-case"></a>
  `when`は`case`と同じ深さに揃えましょう。
  多くの人が同意できないのを知っていますが、
  このスタイルは"The Ruby Programming Language"、"Programming Ruby"
  双方で確立されたものなのです。
<sup>[[link](#indent-when-to-case)]</sup>

  ```Ruby
  # 悪い例
  case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
  end

  # 良い例
  case
  when song.name == 'Misty'
    puts 'Not again!'
  when song.duration > 120
    puts 'Too long!'
  when Time.now.hour > 21
    puts "It's too late"
  else
    song.play
  end
  ```

* <a name="indent-conditional-assignment"></a>
  条件式を変数に代入するときは、
  その式の通常のアラインメントを維持しましょう。
<sup>[[link](#indent-conditional-assignment)]</sup>

  ```Ruby
  # 悪い例 - かなり複雑です
  kind = case year
  when 1850..1889 then 'Blues'
  when 1890..1909 then 'Ragtime'
  when 1910..1929 then 'New Orleans Jazz'
  when 1930..1939 then 'Swing'
  when 1940..1950 then 'Bebop'
  else 'Jazz'
  end

  result = if some_cond
    calc_something
  else
    calc_something_else
  end

  # 良い例 - 何が行われているか明らかです
  kind = case year
         when 1850..1889 then 'Blues'
         when 1890..1909 then 'Ragtime'
         when 1910..1929 then 'New Orleans Jazz'
         when 1930..1939 then 'Swing'
         when 1940..1950 then 'Bebop'
         else 'Jazz'
         end

  result = if some_cond
             calc_something
           else
             calc_something_else
           end

  # 良い例 (少しだけ幅の効率がよいです)
  kind =
    case year
    when 1850..1889 then 'Blues'
    when 1890..1909 then 'Ragtime'
    when 1910..1929 then 'New Orleans Jazz'
    when 1930..1939 then 'Swing'
    when 1940..1950 then 'Bebop'
    else 'Jazz'
    end

  result =
    if some_cond
      calc_something
    else
      calc_something_else
    end
  ```

* <a name="empty-lines-between-methods"></a>
  定義式の間には空行をいれ、
  メソッド内の論理的段落ごとに分割しましょう。
<sup>[[link](#empty-lines-between-methods)]</sup>

  ```Ruby
  def some_method
    data = initialize(options)

    data.manipulate!

    data.result
  end

  def some_method
    result
  end
  ```

* <a name="no-trailing-params-comma"></a>
  メソッド呼び出しの最後の引数の後ろのコンマは避けましょう。
  引数が複数行にわかれていない時は、特に避けましょう。
<sup>[[link](#no-trailing-params-comma)]</sup>

  ```Ruby
  # 悪い例 - 簡単に引数を移動・追加・削除できますが、それでもお奨めできません。
  some_method(
               size,
               count,
               color,
             )

  # 悪い例
  some_method(size, count, color, )

  # 良い例
  some_method(size, count, color)
  ```

* <a name="spaces-around-equals"></a>
  メソッドの引数に初期値を割り当てるとき、
  `=`演算子の周りにはスペースを入れましょう。
<sup>[[link](#spaces-around-equals)]</sup>

  ```Ruby
  # 悪い例
  def some_method(arg1=:default, arg2=nil, arg3=[])
    # do something...
  end

  # 良い例
  def some_method(arg1 = :default, arg2 = nil, arg3 = [])
    # do something...
  end
  ```

  いくつかのRuby本は最初のスタイルを提案しているけど、
  ２つ目の方が、実用的により優れています
  (そして、ほぼ間違いなく少し読みやすいです)。

* <a name="no-trailing-backslash"></a>
  不要な`\`を用いた行の継続は避けましょう。
  実際、文字列連結以外での行の継続は避けましょう。
<sup>[[link](#no-trailing-backslash)]</sup>

  ```Ruby
  # 悪い例
  result = 1 - \
           2

  # 良い例 (しかしそれでも地獄のように醜い)
  result = 1 \
           - 2

  long_string = 'First part of the long string' \
                ' and second part of the long string'
  ```

* <a name="consistent-multi-line-chains"></a>
  一貫した複数行のメソッドチェーンのスタイルを採用しましょう。
  Rubyコミュニティには2つの有名なスタイル - 先頭に`.`を付けるもの (Option A)、
  末尾に`.`を付けるもの (Option B) - があり、
  どちらも良いと考えられています。
  我々は先頭に`.`を付けるもの (Option A)を採用します。
<sup>[[link](#consistent-multi-line-chains)]</sup>

  * **(Option A)** メソッドチェーンを次の行へつなげる時は、
    `.`は次の行に置きましょう。

    ```Ruby
    # 悪い例 - ２行目を理解するのに１行目を調べなければなりません
    one.two.three.
      four

    # 良い例 - ２行目で何が行われているかすぐに理解できます
    one.two.three
      .four
    ```

  双方のスタイルのメリットに関する議論は[こちら](https://github.com/bbatsov/ruby-style-guide/pull/176)
  で見ることができます。

* <a name="no-double-indent"></a>
  メソッド呼び出しが複数行に及ぶときは、引数は揃えましょう。
  １行の長さの制約のために、引数を揃えるのに適していない時は、
  最初の引数以降をインデント１つ分で揃えるスタイルも許容できます。
<sup>[[link](#no-double-indent)]</sup>

  ```Ruby
  # 初期値 (１行がとても長いです)
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
  end

  # 悪い例 (インデント２つで揃えています)
  def send_mail(source)
    Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text)
  end

  # 良い例
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com',
                   from: 'us@example.com',
                   subject: 'Important message',
                   body: source.text)
  end

  # 良い例 (普通のインデントです)
  def send_mail(source)
    Mailer.deliver(
      to: 'bob@example.com',
      from: 'us@example.com',
      subject: 'Important message',
      body: source.text
    )
  end
  ```

* <a name="align-multiline-arrays"></a>
  複数行に及ぶ配列は、要素を揃えましょう。
<sup>[[link](#align-multiline-arrays)]</sup>

  ```Ruby
  # 悪い例 - インデント１つです
  menu_item = ["Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
    "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"]

  # 良い例
  menu_item = [
    "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
    "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"
  ]

  # 良い例
  menu_item =
    ["Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam", "Spam",
     "Baked beans", "Spam", "Spam", "Spam", "Spam", "Spam"]
  ```

* <a name="underscores-in-numerics"></a>
  可読性のため、大きな数値にはアンダースコアをつけましょう。
<sup>[[link](#underscores-in-numerics)]</sup>

  ```Ruby
  # 悪い例 - 0はいくつありますか？
  num = 1000000

  # 良い例 - 人の頭でもより簡単に解析できます
  num = 1_000_000
  ```

* <a name="underscores-in-numerics-hex"></a>
桁数が大きな2進法および16進法の数値リテラルは4桁毎に下線を入れて読みやすくする。
<sup>[[link](#underscores-in-numerics-hex)]</sup>

  - 例: `0xABCD_1234`

* 16進法はアルファベットの大文字で書いても小文字で書いても良いが、1つのファイルの中では統一すること。

* <a name="fraction"></a>
分数は `r` サフィックスを用いて書くこと。
<sup>[[link](#fraction)]</sup>
  - 例: `1/2r #=> (1/2)`

* <a name="complex-number"></a>
複素数は `i` または `ri` サフィックスを用いて書くこと。
<sup>[[link](#complex-number)]</sup>
  - 例: `1 + 2i #=> (1+2i)`




* <a name="rdoc-conventions"></a>
  APIのドキュメントのため、RDocの規約に従いましょう。
  コメント行と`def`の間に空行を入れてはいけません。
<sup>[[link](#rdoc-conventions)]</sup>

* <a name="80-character-limits"></a>
  [推奨] 特殊な事情が無い限りは1行の文字数を80文字以内に納めること (全角文字は2文字と数えること)。
 * どんな事情があろうとも1行の文字数が100文字を越えてはならない
<sup>[[link](#80-character-limits)]</sup>

* <a name="no-trailing-whitespace"></a>
  行末のスペースは避けましょう。
<sup>[[link](#no-trailing-whitespace)]</sup>

* <a name="newline-eof"></a>
  ファイルの終端には改行を入れましょう。
<sup>[[link](#newline-eof)]</sup>

* <a name="no-block-comments"></a>
  ブロックコメントは使ってはいけません。
  前にスペースが入ると機能しませんし、
  通常のコメントと違い、簡単に見分けが付きません。
<sup>[[link](#no-block-comments)]</sup>

  ```Ruby
  # 悪い例
  == begin
  comment line
  another comment line
  == end

  # 良い例
  # comment line
  # another comment line
  ```

## 構文

* <a name="double-colons"></a>
  `::`は、定数(クラスやモジュールも含みます)や
  コンストラクタ(例えば`Array()`や`Nokogiri::HTML()`)を参照するときにのみ使いましょう。
  通常のメソッド呼び出しでは`::`の使用は避けましょう。
<sup>[[link](#double-colons)]</sup>

  ```Ruby
  # 悪い例
  SomeClass::some_method
  some_object::some_method

  # 良い例
  SomeClass.some_method
  some_object.some_method
  SomeModule::SomeClass::SOME_CONST
  SomeModule::SomeClass()
  ```

* <a name="method-parens"></a>
  引数があるとき、`def`は`()`と共に使いましょう。
  引数がない場合は`()`は除きましょう。
<sup>[[link](#method-parens)]</sup>

   ```Ruby
   # 悪い例
   def some_method()
     # body omitted
   end

   # 良い例
   def some_method
     # body omitted
   end

   # 悪い例
   def some_method_with_arguments arg1, arg2
     # body omitted
   end

   # 良い例
   def some_method_with_arguments(arg1, arg2)
     # body omitted
   end
   ```

* <a name="no-for-loops"></a>
  あなたが使ってはならない理由を正確に知っていなければ、決して`for`を使ってはいけません。
  代わりにイテレータが使われるべきです。
  `for`は`each`の観点で実装されています(だから、あなた方は遠回りでも使っています)が、
  `for`は(`each`と違い)新しいスコープを導入せず、
  そのブロック内で定義された変数は、ブロックの外からも見えるようになってしまいます。
<sup>[[link](#no-for-loops)]</sup>

  ```Ruby
  arr = [1, 2, 3]

  # 悪い例
  for elem in arr do
    puts elem
  end

  # elemはルーブの外からも参照できることに注意しましょう
  elem # => 3

  # 良い例
  arr.each { |elem| puts elem }

  # elemはeachブロックの外からは参照できません
  elem # => NameError: undefined local variable or method `elem'
  ```

* <a name="no-then"></a>
  `then`は複数行にまたがる`if/unless`では使ってはいけません。
 * `if/unless` 式には `then` を付加せず記述します。
<sup>[[link](#no-then)]</sup>

  ```Ruby
  # 悪い例
  if some_condition then
    # 本文省略
  end

  # 良い例
  if some_condition
    # 本文省略
  end
  ```

* <a name="same-line-condition"></a>
  複数行にまたがる`if/unless`では、条件式は常に`if/unless`と同じ行に置きましょう。
<sup>[[link](#same-line-condition)]</sup>

  ```Ruby
  # 悪い例
  if
    some_condition
    do_something
    do_something_else
  end

  # 良い例
  if some_condition
    do_something
    do_something_else
  end
  ```

* <a name="ternary-operator"></a>
  `if/then/else/end`構文よりも三項演算子(`?:`)を好みます。
  そちらの方がより明快で簡潔です。
<sup>[[link](#ternary-operator)]</sup>

  ```Ruby
  # 悪い例
  result = if some_condition then something else something_else end

  # 良い例
  result = some_condition ? something : something_else
  ```

* <a name="no-nested-ternary"></a>
  三項演算子は１つの式につき１つまでにしましょう。
  つまり、三項演算子はネストしてはいけません。
  このケースでは`if/else`の方がよいです。
<sup>[[link](#no-nested-ternary)]</sup>

  ```Ruby
  # 悪い例
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # 良い例
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
  ```

* <a name="no-1.8-if-syntax"></a>
  `if x: ...`は使ってはいけません - Ruby 1.9現在は廃止されました。
  代わりに三項演算子を使いましょう。
<sup>[[link](#no-1.8-if-syntax)]</sup>

  ```Ruby
  # 悪い例
  result = if some_condition: something else something_else end

  # 良い例
  result = some_condition ? something : something_else
  ```

* <a name="no-semicolon-ifs"></a>
  `if x; ...`を使ってはいけません。代わりに三項演算子を使いましょう。
<sup>[[link](#no-semicolon-ifs)]</sup>

* <a name="use-if-case-returns"></a>
  結果を返す`if`や`case`式の値を活用しましょう。
<sup>[[link](#use-if-case-returns)]</sup>

  ```Ruby
  # 悪い例
  if condition
    result = x
  else
    result = y
  end

  # 良い例
  retuls =
    if condition
      x
    else
      y
    end
  ```

* <a name="one-line-cases"></a>
  １行の`case`文では`when x then ...`を使いましょう。
  代わりの表現である`when x: ...`は、Ruby 1.9で廃止されました。
<sup>[[link](#one-line-cases)]</sup>

* <a name="no-when-semicolons"></a>
  `when x; ...`を使ってはいけません。前のルールを見てください。
<sup>[[link](#no-when-semicolons)]</sup>

* <a name="bang-not-not"></a>
  `not`の代わりに`!`を使いましょう。
<sup>[[link](#bang-not-not)]</sup>

  ```Ruby
  # 悪い例 - 評価順のため、()が必要になります
  x = (not something)

  # 良い例
  x = !something
  ```

* <a name="no-bang-bang"></a>
  `!!`は避けましょう。
<sup>[[link](#no-bang-bang)]</sup>

  ```Ruby
  # 悪い例
  x = 'test'
  # obscure nil check
  if !!x
    # body omitted
  end

  x = false
  # 二重否定はbooleanとして役に立ちません。
  !!x # => false

  # 良い例
  x = 'test'
  unless x.nil?
    # body omitted
  end
  ```

* <a name="no-and-or-or"></a>
  `and`と`or`と`not`の使用は禁止です。それらにその価値はありません。
   常に、代わりに`&&`と`||`と`!`を使いましょう。
 * ただし、`式 or raise 'message'`の時のみ`or`を使っても良い。
<sup>[[link](#no-and-or-or)]</sup>

  ```Ruby
  # 悪い例
  # boolean式
  if some_condition and some_other_condition
    do_something
  end

  # 制御構文
  document.saved? or document.save!

  # 良い例
  # boolean式
  if some_condition && some_other_condition
    do_something
  end

  # 制御構文
  document.saved? || document.save!
  ```

* <a name="no-multiline-ternary"></a>
  複数行にまたがる三項演算子`?:`は避けましょう; 代わりに`if/unless`を使いましょう。
<sup>[[link](#no-multiline-ternary)]</sup>

* <a name="if-as-a-modifier"></a>
  本文が１行のときは、`if/unless`修飾子を利用するのが好まれます。
  他の良い代替案としては`&&/||`を使った制御構文があります。
<sup>[[link](#if-as-a-modifier)]</sup>

  ```Ruby
  # 悪い例
  if some_condition
    do_something
  end

  # 良い例
  do_something if some_condition

  # もう１つの良い例
  some_condition && do_something
  ```

* <a name="no-multiline-if-modifiers"></a>
  重要な複数行のブロックに`if/unless`修飾子を用いるのは避けましょう。
<sup>[[link](#no-multiline-if-modifiers)]</sup>

  ```Ruby
  # 悪い例
  10.times do
    # 複数行のbody省略
  end if some_condition

  # 良い例
  if some_condition
    10.times do
      # 複数行のbody省略
    end
  end
  ```

* <a name="unless-for-negatives"></a>
  否定形のときは`if`より`unless`が好まれます。(もしくは`||`構文を使いましょう)。
<sup>[[link](#unless-for-negatives)]</sup>

  ```Ruby
  # 悪い例
  do_something if !some_condition

  # 悪い例
  do_something if not some_condition

  # 良い例
  do_something unless some_condition

  # もう１つの良い例
  some_condition || do_something
  ```

* <a name="no-else-with-unless"></a>
  `unless`を`else`付きで使ってはいけません。
  肯定条件を先にして書き換えましょう。
<sup>[[link](#no-else-with-unless)]</sup>

  ```Ruby
  # 悪い例
  unless success?
    puts 'failure'
  else
    puts 'success'
  end

  # 良い例
  if success?
    puts 'success'
  else
    puts 'failure'
  end
  ```
* <a name="no-unless-with-multi-condition"></a>
`unless` および `until` の条件式に複数の項を `||` で結合した論理式 (加法標準形) を書いてはならない。
<sup>[[link](#no-unless-with-multi-condition)]</sup>

* <a name="no-parens-if"></a>
  `if/unless/while/until`構文では`()`の使用は避けましょう.
<sup>[[link](#no-parens-if)]</sup>

  ```Ruby
  # 悪い例
  if (x > 10)
    # body omitted
  end

  # 良い例
  if x > 10
    # body omitted
  end
  ```
* <a name="no-while-not"></a>
`while !condition` の代わりに `until condition` と書くこと。
<sup>[[link](#no-while-not)]</sup>

* <a name="no-multiline-while-do"></a>
  複数行の`while/until`では、`while/until condition do`を使ってはいけません。
<sup>[[link](#no-multiline-while-do)]</sup>

  ```Ruby
  # 悪い例
  while x > 5 do
    # 本文省略
  end

  until x > 5 do
    # 本文省略
  end

  # 良い例
  while x > 5
    # 本文省略
  end

  until x > 5
    # 本文省略
  end
  ```

* <a name="while-as-a-modifier"></a>
  本文が１行のときは、`while/until`修飾子を利用しましょう。
<sup>[[link](#while-as-a-modifier)]</sup>

  ```Ruby
  # 悪い例
  while some_condition
    do_something
  end

  # 良い例
  do_something while some_condition
  ```

* <a name="until-for-negatives"></a>
  否定形のときは、`while`よりも`until`の方が好まれます。
<sup>[[link](#until-for-negatives)]</sup>

  ```Ruby
  # 悪い例
  do_something while !some_condition

  # 良い例
  do_something until some_condition
  ```

* <a name="infinite-loop"></a>
  無限ループが必要な時は、`while/until`の代わりに`Kernel#loop`を用いましょう。
<sup>[[link](#infinite-loop)]</sup>

  ```Ruby
  # 悪い例
  while true
    do_something
  end

  until false
    do_something
  end

  loop do
    do_something
  end
  ```

* <a name="loop-with-break"></a>
  後判定ループの場合、`begin/end/until`や`begin/end/while`より、`break`付きの`Kernel#loop`が好まれます。
<sup>[[link](#loop-with-break)]</sup>

  ```Ruby
  # 悪い例
  begin
    puts val
    val += 1
  end while val < 0

  # 良い例
  loop do
    puts val
    val += 1
    break unless val < 0
  end
  ```

* <a name="no-dsl-parens"></a>
  内部DSL(例えばRake,Rails,RSpecなど)、
  Ruby内で"キーワード"となるステータスを持ったメソッド(例えば`attr_reader` や`puts`など)や
  アトリビュートにアクセスするメソッドでは、
  引数の周りの`()`を省略しましょう。
  それ以外のメソッドでは、メソッド呼び出しの時に`()`を付けましょう。
<sup>[[link](#no-dsl-parens)]</sup>

  ```Ruby
  class Person
    attr_reader :name, :age

    # 省略
  end

  temperance = Person.new('Temperance', 30)
  temperance.name

  puts temperance.age

  x = Math.sin(y)
  array.delete(e)

  bowling.score.should == 0
  ```

* <a name="no-braces-opts-hash"></a>
  暗黙のオプションハッシュの外側の`{}`は省略しましょう。
<sup>[[link](#no-braces-opts-hash)]</sup>

  ```Ruby
  # 悪い例
  user.set({ name: 'John', age: 45, permissions: { read: true } })

  # 良い例
  user.set(name: 'John', age: 45, permissions: { read: true })
  ```

* <a name="no-braces-opts-hash2"></a>
パラメータリストの末尾にハッシュリテラルを書く場合は、ハッシュリテラルの括弧を省略すること。
<sup>[[link](#no-braces-opts-hash2)]</sup>

    ```ruby
    # 良い例
    foo(1, 2, foo: :bar, baz: 42)

    # bad
    foo(1, 2, { foo: :bar, baz: 42 })
    ```

* <a name="no-dsl-decorating"></a>
  内部DSLの一部として使われるメソッドの引数では、外側の`()`、`{}`は省略しましょう
<sup>[[link](#no-dsl-decorating)]</sup>

  ```Ruby
  class Person < ActiveRecord::Base
    # 悪い例
    validates(:name, { presence: true, length: { within: 1..10 } })

    # 良い例
    validates :name, presence: true, length: { within: 1..10 }
  end
  ```

* <a name="no-args-no-parens"></a>
  引数のないメソッド呼び出しの`()`は省略しましょう。
<sup>[[link](#no-args-no-parens)]</sup>

  ```Ruby
  # 悪い例
  Kernel.exit!()
  2.even?()
  fork()
  'test'.upcase()

  # 良い例
  Kernel.exit!
  2.even?
  fork
  'test'.upcase
  ```

* <a name="single-line-blocks"></a>
  １行のブロックでは`do...end`より`{...}`の方が好まれます。
  複数行のブロックでは`{...}`は避けましょう
  (複数行のメソッドチェーンは常に醜いです)。
  "制御構文"や"メソッド定義"では常に`do...end`を使いましょう
  (例えばRakefilesや特定のDSLなど)
  メソッドチェーンでの`do...end`は避けましょう。
<sup>[[link](#single-line-blocks)]</sup>

  ```Ruby
  names = ['Bozhidar', 'Steve', 'Sarah']

  # 悪い例
  names.each do |name|
    puts name
  end

  # 良い例
  names.each { |name| puts name }

  # 悪い例
  names.select do |name|
    name.start_with?('S')
  end.map { |name| name.upcase }

  # 良い例
  names.select { |name| name.start_with?('S') }.map { |name| name.upcase }
  ```

  `{...}`を用いた複数行のメソッドチェーンをOKと主張する人もいるかもしれないが、
  自問してみてほしい - このコードは本当に読みやすいだろうか？
  また、このブロックの本文は素早く展開できるだろうか？


* <a name="indent-do-end"></a>
`do`/`end` によるブロックでは、`do`の前後に空白を1つ入れ、ブロックパラメータの後で改行し、`end` は独立した行に書くこと。ブロック本体のインデントは1レベル下げ、`end` のインデントはメソッド呼び出しの1行目にあわせること。
<sup>[[link](#indent-do-end)]</sup>

    ```ruby
    # 良い例
    [1, 2, 3].each do |num|
      puts num
    end

    # 悪い例
    [1, 2, 3].each do |num|
        puts num
      end

    # 悪い例
    [1, 2, 3].each do |num|
                     puts num
                   end

    # bad
    [1, 2, 3].each do |num| puts num end
    ```

* <a name="brace-block-space"></a>
中括弧によるブロックでは、`{` の前に空白を1つ入れること。
<sup>[[link](#brace-block-space2)]</sup>

* <a name="brace-block-space"></a>
中括弧によるブロックを1行で書く場合は、`{`　またはブロックパラメータと本体コードの間、および本体コードと `}` の間に空白を1つずつ入れること。
<sup>[[link](#brace-block-space2)]</sup>

    ```ruby
    # 良い例
    [1, 2, 3].each {|num| puts num }
    [1, 2, 3].each { |num| puts num }

    # 悪い例
    [1, 2, 3].each {|num| puts num}

    # 悪い例
    [1, 2, 3].each { |num| puts num}

    # 良い例
    10.times { puts 'Hello world' }

    # 悪い例
    10.times {puts 'Hello world' }

    # 悪い例
    10.times {puts 'Hello world'}

    # 悪い例
    10.times { puts 'Hello world'}
    ```
	
* <a name="long-method-chain"></a>
長いメソッドチェインの最後のメソッド呼び出しでブロックを渡す場合、最後のメソッド呼び出しのレシーバをローカル変数として抽出し、ブロック付きメソッド呼び出しを独立した式として書くこと。
<sup>[[link](#long-method-chain)]</sup>

    ```ruby
    # 良い例
    posts = Post.joins(:user)
      .merge(User.paid)
      .where(created_at: target_date)
    posts.each do |post|
      next if stuff_ids.include?(post.user_id)
      comment_count += post.comments.size
    end

    # 悪い例
    posts = Post.joins(:user)
      .merge(User.paid)
      .where(created_at: target_date).each do |post|
        next if stuff_ids.include?(post.user_id)
        comment_count += post.comments.size
      end
    ```

* <a name="method-inline-newline"></a>
式の途中で改行する場合は、2行目以降を1行目より1段深くインデントすること。
<sup>[[link](#method-inline-newline)]</sup>

    ```ruby
    # 良い例
    User.active
      .some_scope(foo)
      .other_scope(bar)

    # 悪い例
    User.active.
    some_scope(foo).
    other_scope(bar)
    ```

* <a name="block-argument"></a>
  単に他のブロックに引数を渡すだけのブロックリテラルを避けるため、
  ブロック引数を明示することを検討しましょう。
  ただしブロックが`Proc`に変換されることでのパフォーマンスに気をつけましょう。
<sup>[[link](#block-argument)]</sup>

  ```Ruby
  require 'tempfile'

  # 悪い例
  def with_tmp_dir
    Dir.mktmpdir do |tmp_dir|
      Dir.chdir(tmp_dir) { |dir| yield dir }  # block just passes arguments
    end
  end

  # 良い例
  def with_tmp_dir(&block)
    Dir.mktmpdir do |tmp_dir|
      Dir.chdir(tmp_dir, &block)
    end
  end

  with_tmp_dir do |dir|
    puts "dir is accessible as parameter and pwd is set: #{dir}"
  end
  ```

* <a name="no-explicit-return"></a>
  制御構文上不要な`return`は避けましょう。
<sup>[[link](#no-explicit-return)]</sup>

  ```Ruby
  # 悪い例
  def some_method(some_arr)
    return some_arr.size
  end

  # 良い例
  def some_method(some_arr)
    some_arr.size
  end
  ```

* <a name="no-self-unless-required"></a>
  不要な`self`は避けましょう (自身のアトリビュートへの書き込みでのみ必要です)。
<sup>[[link](#no-self-unless-required)]</sup>

  ```Ruby
  # 悪い例
  def ready?
    if self.last_reviewed_at > self.last_updated_at
      self.worker.update(self.content, self.options)
      self.status = :in_progress
    end
    self.status == :verified
  end

  # 良い例
  def ready?
    if last_reviewed_at > last_updated_at
      worker.update(content, options)
      self.status = :in_progress
    end
    status == :verified
  end
  ```

* <a name="no-shadowing"></a>
  当然の帰結として、ローカル変数でメソッドを隠すのは、
  それらが等価なものでない限り避けましょう。
<sup>[[link](#no-shadowing)]</sup>

  ```Ruby
  class Foo
    attr_accessor :options

    # ok
    def initialize(options)
      self.options = options
      # both options and self.options are equivalent here
    end

    # 悪い例
    def do_something(options = {})
      unless options[:when] == :later
        output(self.options[:message])
      end
    end

    # 良い例
    def do_something(params = {})
      unless params[:when] == :later
        output(options[:message])
      end
    end
  end
  ```

* <a name="safe-assignment-in-condition"></a>
  代入部分を`()`で囲まずに、`=`の返り値を条件式に用いてはいけません。
  これは、Rubyistの中では *条件式内での安全な代入* としてとても有名です。
<sup>[[link](#safe-assignment-in-condition)]</sup>

  ```Ruby
  # 悪い例 (+ 警告が出ます)
  if v = array.grep(/foo/)
    do_something(v)
    ...
  end

  # 良い例 (MRIはこれでも文句を言いますが、RuboCopでは問題ありません)
  if (v = array.grep(/foo/))
    do_something(v)
    ...
  end

  # 良い例
  v = array.grep(/foo/)
  if v
    do_something(v)
    ...
  end
  ```

* <a name="self-assignment"></a>
  利用できるときには省略された自己代入演算子を用いましょう。
<sup>[[link](#self-assignment)]</sup>

  ```Ruby
  # 悪い例
  x = x + y
  x = x * y
  x = x**y
  x = x / y
  x = x || y
  x = x && y

  # 良い例
  x += y
  x *= y
  x **= y
  x /= y
  x ||= y
  x &&= y
  ```

* <a name="double-pipe-for-uninit"></a>
  変数の初期化には、`||=`を自由に使いましょう。
<sup>[[link](#double-pipe-for-uninit)]</sup>

  ```Ruby
  # nameがnilかfalseでなければ、Bozhidarで初期化します
  name ||= 'Bozhidar'
  ```

* <a name="no-double-pipes-for-bools"></a>
  boolean変数には`||=`を用いてはいけません
  (現在の値が`false`であったときに何が起こるか考えてみましょう)。
<sup>[[link](#no-double-pipes-for-bools)]</sup>

  ```Ruby
  # 悪い例 - たとえenabledがfalseでもtrueが入ります
  enabled ||= true

  # 良い例
  enabled = true if enabled.nil?
  ```

* <a name="double-amper-preprocess"></a>
  値が入っているかわからない変数の前処理のは`&&=`を用いましょう。
  `&&=`を使えば変数が存在するときのみ値を変更するので、
  存在確認に用いている不要な`if`を除去できます。
<sup>[[link](#double-amper-preprocess)]</sup>

  ```Ruby
  # 悪い例
  if something
    something = something.downcase
  end

  # 悪い例
  something = something ? something.downcase : nil

  # ok
  something = something.downcase if something

  # 良い例
  something = something && something.downcase

  # より良い例
  something &&= something.downcase
  ```

* <a name="no-case-equality"></a>
  等価演算子`===`の露骨な使用は避けましょう。
  その名が示す通り、`case`の条件判定で用いられており、
  その外で用いられると混乱のもとになります。
<sup>[[link](#no-case-equality)]</sup>

  ```Ruby
  # 悪い例
  Array === something
  (1..100) === 7
  /something/ === some_string

  # 良い例
  something.is_a?(Array)
  (1..100).include?(7)
  some_string =~ /something/
  ```

* <a name="no-cryptic-perlisms"></a>
  Perlスタイルの(`$:`や`$;`などのような)特別な変数の使用は避けましょう。
  それらは極めて不可解で、
  １行のスクリプト以外でそれらが使われるとやる気が削がれます。
  `English`ライブラリから提供される人にやさしいエイリアスを用いましょう。
<sup>[[link](#no-cryptic-perlisms)]</sup>

  ```Ruby
  # 悪い例
  $:.unshift File.dirname(__FILE__)

  # 良い例
  require 'English'
  $LOAD_PATH.unshift File.dirname(__FILE__)
  ```

* <a name="parens-no-spaces"></a>
  メソッド名と引数の始まりの`(`の間にスペースを入れてはいけません。
<sup>[[link](#parens-no-spaces)]</sup>

  ```Ruby
  # 悪い例
  f (3 + 2) + 1

  # 良い例
  f(3 + 2) + 1
  ```

* <a name="parens-as-args"></a>
  メソッドの最初の引数が`(`で始まるならば、
  常にメソッド呼び出しに`()`を用いましょう。
  例えば次のように書きます
`f((3 + 2) + 1)`。
<sup>[[link](#parens-as-args)]</sup>

* <a name="always-warn-at-runtime"></a>
  Rubyインタープリタを走らせるときは、常に`-w`オプションを付けましょう。
  これまでのルールのどれかを忘れてしまった時に警告を出してくれます！
<sup>[[link](#always-warn-at-runtime)]</sup>

* <a name="lambda-multi-line"></a>
  １行の本文を持つラムダには新しいリテラルを持ちましょう。
  `lambda`は複数行にまたがるときに使いましょう。
<sup>[[link](#lambda-multi-line)]</sup>

  ```Ruby
  # 悪い例
  l = lambda { |a, b| a + b }
  l.call(1, 2)

  # 正しい例、ですがギクシャクしています
  l = ->(a, b) do
    tmp = a * 7
    tmp * b / 50
  end

  # 良い例
  l = ->(a, b) { a + b }
  l.call(1, 2)

  l = lambda do |a, b|
    tmp = a * 7
    tmp * b / 50
  end
  ```

* <a name="proc"></a>
  `Proc.new`より`proc`を好みます。
<sup>[[link](#proc)]</sup>

  ```Ruby
  # 悪い例
  p = Proc.new { |n| puts n }

  # 良い例
  p = proc { |n| puts n }
  ```

* <a name="proc-call"></a>
  ラムダやprocの呼び出しには`proc[]`や`proc.()`より`proc.call()`を好みます。
<sup>[[link](#proc-call)]</sup>

  ```Ruby
  # 悪い例 - 列挙型のアクセスに似ているように見えます
  l = ->(v) { puts v }
  l[1]

  # こちらも悪い例 - 珍しい構文です
  l = ->(v) { puts v }
  l.(1)

  # 良い例
  l = ->(v) { puts v }
  l.call(1)
  ```

* <a name="underscore-unused-vars"></a>
  使わないブロック引数やローカル変数の先頭には`_`を付けましょう。
  単に`_`を用いるのも許容されます
  (少し説明不足ではありますが)。
  この慣習はRubyインタープリタやRubocopのようなツールには認識されており、
  変数を使っていないという警告を抑えることでしょう。
<sup>[[link](#underscore-unused-vars)]</sup>

  ```Ruby
  # 悪い例
  result = hash.map { |k, v| v + 1 }

  def something(x)
    unused_var, used_var = something_else(x)
  end

  # 良い例
  result = hash.map { |_, v| v + 1 }

  def something(x)
    _, used_var = something_else(x)
  end
  ```

* <a name="global-stdout"></a>
  `STDOUT/STDERR/STDIN`の代わりに`$stdout/$stderr/$stdin`を用いましょう。
  `STDOUT/STDERR/STDIN`は定数であり、
  Rubyでの定数は、実際は再割当てできますが(他のストリームへのリダイレクトも可能)、
  もし再割当てするとインタープリタからの警告が出るでしょう。
<sup>[[link](#global-stdout)]</sup>

* <a name="warn"></a>
  `$stderr.puts`の代わりに`warn`を用いましょう。
  簡潔さや明快さはさておき、
  `warn`は必要であれば警告を抑制することができます
  (警告レベルを`-W0`を用いて0に設定することによって実現できます)。
<sup>[[link](#warn)]</sup>

* <a name="sprintf"></a>
  不可解な`String#%`メソッドより`sprintf`や`format`を好みます。
<sup>[[link](#sprintf)]</sup>

  ```Ruby
  # 悪い例
  '%d %d' % [20, 10]
  # => '20 10'

  # 良い例
  sprintf('%d %d', 20, 10)
  # => '20 10'

  # 良い例
  sprintf('%{first} %{second}', first: 20, second: 10)
  # => '20 10'

  format('%d %d', 20, 10)
  # => '20 10'

  # 良い例
  format('%{first} %{second}', first: 20, second: 10)
  # => '20 10'
  ```

* <a name="array-join"></a>
  不可解な`Array#*`メソッドよりも`Array#join`を好みます。
<sup>[[link](#array-join)]</sup>

  ```Ruby
  # 悪い例
  %w(one two three) * ', '
  # => 'one, two, three'

  # 良い例
  %w(one two three).join(', ')
  # => 'one, two, three'
  ```

* <a name="splat-arrays"></a>
  引数が配列かどうかわからないが、それを配列として扱って処理したいとき、
  配列のチェックを明示するより、`[*var]`や`Array()`を代わりに使いましょう。
<sup>[[link](#splat-arrays)]</sup>

  ```Ruby
  # 悪い例
  paths = [paths] unless paths.is_a? Array
  paths.each { |path| do_something(path) }

  # 良い例
  [*paths].each { |path| do_something(path) }

  # 良い例 (そして少し読みやすいです)
  Array(paths).each { |path| do_something(path) }
  ```

* <a name="ranges-or-between"></a>
  複雑な比較ロジックの代わりに、
  可能な限り`Range`や`Comparable#between?`を用いましょう。
<sup>[[link](#ranges-or-between)]</sup>

  ```Ruby
  # 悪い例
  do_something if x >= 1000 && x <= 2000

  # 良い例
  do_something if (1000..2000).include?(x)

  # 良い例
  do_something if x.between?(1000, 2000)
  ```

* <a name="predicate-methods"></a>
  `==`を明示した比較よりも判定メソッドを用いましょう。
  数値の比較はOKです。
<sup>[[link](#predicate-methods)]</sup>

  ```Ruby
  # 悪い例
  if x % 2 == 0
  end

  if x % 2 == 1
  end

  if x == nil
  end

  # 良い例
  if x.even?
  end

  if x.odd?
  end

  if x.nil?
  end

  if x.zero?
  end

  if x == 0
  end
  ```

* <a name="no-non-nil-checks"></a>
  ブーリアン値を扱わない限り、露骨な`nil`でないかの検査は避けましょう。
<sup>[[link](#no-non-nil-checks)]</sup>

  ```Ruby
  # 悪い例
  do_something if !something.nil?
  do_something if something != nil

  # 良い例
  do_something if something

  # 良い例 - ブーリアン値を扱うとき
  def value_set?
    !@some_boolean.nil?
  end
  ```

* <a name="no-BEGIN-blocks"></a>
  `BEGIN`ブロックの使用は避けましょう。
<sup>[[link](#no-BEGIN-blocks)]</sup>

* <a name="no-END-blocks"></a>
  `END`ブロックを使ってはいけません。代わりに`Kernel#at_exit`を使いましょう。
<sup>[[link](#no-END-blocks)]</sup>

  ```ruby
  # 悪い例

  END { puts 'Goodbye!' }

  # 良い例

  at_exit { puts 'Goodbye!' }
  ```

* <a name="no-flip-flops"></a>
  フリップフロップの使用は避けましょう。
<sup>[[link](#no-flip-flops)]</sup>

* <a name="no-nested-conditionals"></a>
  制御構文で条件式のネストは避けましょう。
<sup>[[link](#no-nested-conditionals)]</sup>

  不正なデータをアサートするにはガード節を好みます。
  ガード節は、可能な限り関数から出ていくために、
  関数の先頭付近で宣言される条件式です。

  ```Ruby
  # 悪い例
  def compute_thing(thing)
    if thing[:foo]
      update_with_bar(thing)
      if thing[:foo][:bar]
        partial_compute(thing)
      else
        re_compute(thing)
      end
    end
  end

  # 良い例
  def compute_thing(thing)
    return unless thing[:foo]
    update_with_bar(thing[:foo])
    return re_compute(thing) unless thing[:foo][:bar]
    partial_compute(thing)
  end
  ```

  ループ内では条件判定ブロックよりも`next`が好まれます。
  ```Ruby
  # 悪い例
  [0, 1, 2, 3].each do |item|
    if item > 1
      puts item
    end
  end

  # 良い例
  [0, 1, 2, 3].each do |item|
    next unless item > 1
    puts item
  end
  ```

## 命名規則

* <a name="english-identifiers"></a>
  識別子は英語で名づけましょう。
 * 変数名は英単語を省略せずに使って名付けること。
 * ただし、変数名が長くなってしまう場合は、単語の頭文字を除く母音を削除したり、一般的な略語を選択するなどの方法で短縮化してよい。
 * また、慣習的な名前 (ループカウンタとしての `i` や `j` など) は許可する。
<sup>[[link](#english-identifiers)]</sup>

  ```Ruby
  # 悪い例 - 識別子がnon-asciiな文字列です
  заплата = 1_000

  # 悪い例 - (キリル文字の代わりに)ラテン文字で書かれてはいますが、識別子がブルガリア語です
  zaplata = 1_000

  # 良い例
  salary = 1_000
  ```

* <a name="snake-case-symbols-methods-vars"></a>
  シンボル、メソッド、変数には`snake_case`を用いましょう。
<sup>[[link](#snake-case-symbols-methods-vars)]</sup>

  ```Ruby
  # 悪い例
  :'some symbol'
  :SomeSymbol
  :someSymbol

  someVar = 5

  def someMethod
    ...
  end

  def SomeMethod
   ...
  end

  # 良い例
  :some_symbol

  def some_method
    ...
  end
  ```

* <a name="camelcase-classes"></a>
  クラスやモジュールには`CamelCase`を用いましょう。(HTTP, RFC, XMLのような頭字語は大文字を保ちましょう)。
<sup>[[link](#camelcase-classes)]</sup>

  ```Ruby
  # 悪い例
  class Someclass
    ...
  end

  class Some_Class
    ...
  end

  class SomeXml
    ...
  end

  # 良い例
  class SomeClass
    ...
  end

  class SomeXML
    ...
  end
  ```

* <a name="snake-case-files"></a>
  ファイル名には`snake_case`を用いましょう。例えば`hello_world.rb`のように。
<sup>[[link](#snake-case-files)]</sup>

* <a name="snake-case-dirs"></a>
  ディレクトリ名には`snake_case`を用いましょう。例えば`lib/hello_world/hello_world.rb`のように。
<sup>[[link](#snake-case-dirs)]</sup>


* <a name="one-class-per-file"></a>
  １つの`class/module`につき、ちょうど１つのファイルを持つことを目指しましょう。
  ファイル名は`CamlCase`を`snake_case`に置き換えた`class/module`名を用いて命名しましょう。
<sup>[[link](#one-class-per-file)]</sup>

* <a name="screaming-snake-case"></a>
  他の定数は`SCREAMING_SNAKE_CASE`を用いましょう。
<sup>[[link](#screaming-snake-case)]</sup>

  ```Ruby
  # 悪い例
  SomeConst = 5

  # 良い例
  SOME_CONST = 5
  ```

* <a name="bool-methods-qmark"></a>
  条件判定メソッド(boolean値が返る)には`?`を末尾に付けましょう
  (すなわち`Array#empty?`のように)。
  メソッドがboolean値を返さなければ、末尾に`?`を付けてはいけません。
<sup>[[link](#bool-methods-qmark)]</sup>

* <a name="dangerous-method-bang"></a>
  *危険* な可能性のあるメソッド
  (すなわち`self`のアトリビュートに変更が加えられるものや、
  `exit!`(`exit`と違ってファイナライザが走らない)のようなもの)
  は、 *危険* であることを示すため、
  末尾に`!`を付けましょう。
<sup>[[link](#dangerous-method-bang)]</sup>

  ```Ruby
  # 悪い例 - '安全'なメソッドです
  class Person
    def update!
    end
  end

  # 良い例
  class Person
    def update
    end
  end

  # 良い例
  class Person
    def update!
    end

    def update
    end
  end
  ```

* <a name="safe-because-unsafe"></a>
  可能な限り、危険なメソッドの観点から安全なメソッドを定義しましょう。
<sup>[[link](#safe-because-unsafe)]</sup>

  ```Ruby
  class Array
    def flatten_once!
      res = []

      each do |e|
        [*e].each { |f| res << f }
      end

      replace(res)
    end

    def flatten_once
      dup.flatten_once!
    end
  end
  ```

* <a name="reduce-blocks"></a>
  短いブロックと共に`reduce`を使うとき、引数は`|a, e|`と名づけましょう。
  (accumulator, element).
<sup>[[link](#reduce-blocks)]</sup>

* <a name="other-arg"></a>
  二項演算子を定義するとき、引数は`other`を用いましょう
  (`<<`と`[]`は意味が違ってくるので、このルールの例外です)。
<sup>[[link](#other-arg)]</sup>

  ```Ruby
  def +(other)
    # body omitted
  end
  ```

* <a name="map-fine-select-reduce-size"></a>
  `collect`より`map`、`detect`より`find`、`find_all`より`select`
  `inject`より`reduce`、`length`より`size`を好みます。
  これは厳しい要件ではありません;
  もしエイリアスを用いるほうが可読性が上がるのであれば、
  使うのもOKです。
  それらの同韻のメソッドはSmalltalkから継承されており、
  他の言語ではあまり一般的ではありません。
  `find_all`よりも`select`が推奨される理由は、
  `reject`と共に用いた時、その名前が極めて自己説明的だからです。
<sup>[[link](#map-fine-select-reduce-size)]</sup>

* <a name="count-vs-size"></a>
  `size`の代わりに`count`を用いてはいけません。
  `Array`以外の`Enumerable`オブジェクトでは、
  サイズを求めるためにコレクション全てをイテレートしてしまいます。
<sup>[[link](#count-vs-size)]</sup>

  ```Ruby
  # 悪い例
  some_hash.count

  # 良い例
  some_hash.size
  ```

* <a name="flat-map"></a>
  `map`と`flatten`の組み合わせの代わりに、`flat_map`を用いましょう。
  これは深さが２以上の配列には適用できません。
  すなわち、`users.first.songs == ['a', ['b','c']]`のときは、
  `flat_map`より`map + flatten`を用いましょう。
  `flatten`は配列を全て平坦にするのに対し、`flat_map`は配列を１次元だけ平坦にします。
<sup>[[link](#flat-map)]</sup>

  ```Ruby
  # 悪い例
  all_songs = users.map(&:songs).flatten.uniq

  # 良い例
  all_songs = users.flat_map(&:songs).uniq
  ```

* <a name="reverse-each"></a>
  `reverse.each`の代わりに`reverse_each`を用いましょう。
  `reverse_each`は新しい配列を作らないので、それが利点です。
<sup>[[link](#reverse-each)]</sup>

  ```Ruby
  # 悪い例
  array.reverse.each { ... }

  # 良い例
  array.reverse_each { ... }
  ```

## コメント

* <a name="no-comments"></a>
  コードそのものがドキュメントになるような説明的なコードを書いて、このセクションの残りのパートは無視しましょう。本当に！
<sup>[[link](#no-comments)]</sup>

* <a name="english-comments"></a>
  コメントは英語で書きましょう。
<sup>[[link](#english-comments)]</sup>

* <a name="hash-space"></a>
  最初の`#`とコメントの間にスペースを１つ入れましょう。
<sup>[[link](#hash-space)]</sup>

* <a name="english-syntax"></a>
  １語より長いコメントを活用し、句読点を使いましょう。
  ピリオドの後に[one space](http://en.wikipedia.org/wiki/Sentence_spacing)を使いましょう。
<sup>[[link](#english-syntax)]</sup>

* <a name="no-superfluous-comments"></a>
  余計なコメントは避けましょう。
<sup>[[link](#no-superfluous-comments)]</sup>

  ```Ruby
  # 悪い例
  counter += 1 # Increments counter by one.
  ```

* <a name="comment-tomdoc"></a>
ドキュメンテーションコメントは[TomDoc](http://tomdoc.org/)のフォーマットに従うこと。
 * モジュールやクラスが外部に公開するメソッドとアクセサに対してドキュメンテーションコメントを Markdown 形式で書くこと。

<sup>[[link](#comment-tomdoc)]</sup>

* <a name="comment-upkeep"></a>
  コメントは最新に保ちましょう。
  古くなったコメントは、コメントがないより悪いです。
<sup>[[link](#comment-upkeep)]</sup>

* <a name="refactor-dont-comment"></a>
  悪いコードを説明するコメントは避けましょう。
  自己説明的なコードへのリファクタリングを行いましょう
<sup>[[link](#refactor-dont-comment)]</sup>

* <a name="inline-code-comment"></a>メソッドの本体コードにコメントを書かなければ理解できないようなコードを書いてはならない。
 * メソッド本体内にコメントを書くよりも、別のメソッドに分けて適切な名前を付ける方が可読性が向上する。
 * ただし、数式に対する補足や出展などはコード本体中にコメントとして書いても良い。
<sup>[[link](#inline-code-comment)]</sup>

### 注釈

* <a name="annotate-above"></a>
  注釈は、通常関連するコードのすぐ上に書きましょう。
<sup>[[link](#annotate-above)]</sup>

* <a name="annotate-keywords"></a>
  注釈のキーワードの後ろは`: `を続けましょう。
  その後ろに問題点を書きましょう。
<sup>[[link](#annotate-keywords)]</sup>

* <a name="indent-annotations"></a>
  もし問題点の記述に複数行かかる場合は、
  後続の行は`#`の後ろにスペース２つでインデントしましょう。
<sup>[[link](#indent-annotations)]</sup>

  ```Ruby
  def bar
    # FIXME: This has crashed occasionally since v3.2.1. It may
    #   be related to the BarBazUtil upgrade.
    baz(:quux)
  end
  ```

* <a name="rare-eol-annotations"></a>
  もし問題が明らかで、記述が冗長な場合は、
  問題のある行の末尾に、本文なしの注釈だけ付けましょう。
  この用法は例外であり、ルールではありません。
<sup>[[link](#rare-eol-annotations)]</sup>

    ```Ruby
    def bar
      sleep 100 # OPTIMIZE
    end
    ```

* <a name="todo"></a>
  あとで追加されるべき、今はない機能や関数を書き留めるには`TODO`を使いましょう。
<sup>[[link](#todo)]</sup>

* <a name="fixme"></a>
  直す必要がある壊れたコードを書き留めるには`FIXME`を使いましょう。
<sup>[[link](#fixme)]</sup>

* <a name="optimize"></a>
  パフォーマンスに問題を及ぼすような、遅い、または非効率なコードを書き留めるには`OPTIMIZE`を使いましょう。
<sup>[[link](#optimize)]</sup>

* <a name="hack"></a>
  疑わしくリファクタリングで除去すべきコードの匂いを書き留めるには`HACK`を使いましょう。
<sup>[[link](#hack)]</sup>

* <a name="review"></a>
  意図したとおりに動くか確認する必要がある箇所を書き留めるには`REVIEW`を使いましょう。
  例: `REVIEW: Are we sure this is how the client does X currently?`
<sup>[[link](#review)]</sup>

* <a name="document-annotations"></a>
  適切に感じるのであれば、他の独自のキーワードを用いても構いませんが、
  それらのキーワードは`README`やそれに類するものに書いておきましょう。
<sup>[[link](#document-annotations)]</sup>

## クラス

* <a name="consistent-classes"></a>
  クラス定義は置いて一貫性のある構造にしましょう。
<sup>[[link](#consistent-classes)]</sup>

  ```Ruby
  class Person
    # extend や include を最初に行います
    extend SomeModule
    include AnotherModule

    # 定数定義はその次です
    SOME_CONSTANT = 20

    # その後ろはアトリビュートマクロです
    attr_reader :name

    # 他のマクロが続きます(もしあれば)
    validates :name

    # public class methods が次に来ます
    def self.some_method
    end

    # public instance methods が続きます
    def some_method
    end

    # protected and private methods は後ろの方にまとめます
    protected

    def some_protected_method
    end

    private

    def some_private_method
    end
  end
  ```

* <a name="file-classes"></a>
  複数行のクラスの中に複数行のクラスをネストさせてはいけません。
  そのようにネストされたクラスは、それが含まれるクラス名のフォルダの中に
  それぞれのクラスのファイルを置くように努めましょう。
<sup>[[link](#file-classes)]</sup>

  ```Ruby
  # 悪い例

  # foo.rb
  class Foo
    class Bar
      # 中に30メソッド
    end

    class Car
      # 中に20メソッド
    end

    # 中に30メソッド
  end

  # 良い例

  # foo.rb
  class Foo
    # 中に30メソッド
  end

  # foo/bar.rb
  class Foo
    class Bar
      # 中に30メソッド
    end
  end

  # foo/car.rb
  class Foo
    class Car
      # 中に20メソッド
    end
  end
  ```

* <a name="modules-vs-classes"></a>
  クラスメソッドしかないクラスはモジュールであることが好まれます。
  クラスはインスタンスを生成することにのみ意味があります。
<sup>[[link](#modules-vs-classes)]</sup>

  ```Ruby
  # 悪い例
  class SomeClass
    def self.some_method
      # body omitted
    end

    def self.some_other_method
    end
  end

  # 良い例
  module SomeClass
    module_function

    def some_method
      # body omitted
    end

    def some_other_method
    end
  end
  ```

* <a name="module-function"></a>
  モジュールのインスタンスメソッドをクラスメソッドにしたいときは、
  `extend self`よりも`module_function`が好まれます。
<sup>[[link](#module-function)]</sup>

  ```Ruby
  # 悪い例
  module Utilities
    extend self

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end

  # 良い例
  module Utilities
    module_function

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end
  ```

* <a name="liskov"></a>
  クラス階層の設計を行うときは、
  [リスコフの置換原則](http://ja.wikipedia.org/wiki/%E3%83%AA%E3%82%B9%E3%82%B3%E3%83%95%E3%81%AE%E7%BD%AE%E6%8F%9B%E5%8E%9F%E5%89%87).
  に従いましょう。
<sup>[[link](#liskov)]</sup>

* <a name="solid-design"></a>
  あなたのクラスを可能な限り
  [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design))
  に保ちましょう。
<sup>[[link](#solid-design)]</sup>

* <a name="define-to-s"></a>
  クラスの領分を説明するため、常に`to_s`メソッドを提供しましょう。
<sup>[[link](#define-to-s)]</sup>

  ```Ruby
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    def to_s
      "#{@first_name} #{@last_name}"
    end
  end
  ```

* <a name="attr_family"></a>
  単純なアクセサやミューテータの定義には、`attr`群を用いましょう。
<sup>[[link](#attr_family)]</sup>

  ```Ruby
  # 悪い例
  class Person
    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    def first_name
      @first_name
    end

    def last_name
      @last_name
    end
  end

  # 良い例
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end
  end
  ```

* <a name="attr"></a>
  `attr`の使用は避けましょう。代わりに`attr_reader`や`attr_accessor`を使いましょう。
<sup>[[link](#attr)]</sup>

  ```Ruby
  # 悪い例 - １つのアクセサしか作れません(1.9で廃止されました)
  attr :something, true
  attr :one, :two, :three # attr_readerと同じです

  # 良い例
  attr_accessor :something
  attr_reader :one, :two, :three
  ```

* <a name="struct-new"></a>
  `Struct.new`の使用を考えましょう、
  それは、単純なアクセサ、コンストラクタや比較演算子を定義してくれます。
<sup>[[link](#struct-new)]</sup>

  ```Ruby
  # 良い例
  class Person
    attr_accessor :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end
  end

  # より良い例
  Person = Struct.new(:first_name, :last_name) do
  end
  ````

* <a name="no-extend-struct-new"></a>
  `Struct.new`を拡張してはいけません - それは既に新しいクラスです。
  それは余分なクラスレベルをもたらし、
  複数回`require`された時に、奇妙なエラーの原因にもなります。
<sup>[[link](#no-extend-struct-new)]</sup>

* <a name="factory-methods"></a>
  あるクラスのインスタンス生成する追加の方法を提供したいときは、
  ファクトリメソッドの追加を検討しましょう。
<sup>[[link](#factory-methods)]</sup>

  ```Ruby
  class Person
    def self.create(options_hash)
      # body omitted
    end
  end
  ```

* <a name="duck-typing"></a>
  継承より[ダック・タイピング](http://ja.wikipedia.org/wiki/%E3%83%80%E3%83%83%E3%82%AF%E3%83%BB%E3%82%BF%E3%82%A4%E3%83%94%E3%83%B3%E3%82%B0)が好まれます。
<sup>[[link](#duck-typing)]</sup>

  ```Ruby
  # 悪い例
  class Animal
    # abstract method
    def speak
    end
  end

  # 継承
  class Duck < Animal
    def speak
      puts 'Quack! Quack'
    end
  end

  # 継承
  class Dog < Animal
    def speak
      puts 'Bau! Bau!'
    end
  end

  # 良い例
  class Duck
    def speak
      puts 'Quack! Quack'
    end
  end

  class Dog
    def speak
      puts 'Bau! Bau!'
    end
  end
  ```

* <a name="no-class-vars"></a>
  継承での振る舞いが"扱いづらい"ので、クラス変数(`@@`)の使用は避けましょう。代わりに `class_attribute` を使用すること
<sup>[[link](#no-class-vars)]</sup>

  ```Ruby
  class Parent
    @@class_var = 'parent'

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = 'child'
  end

  Parent.print_class_var # => will print "child"
  ```

  クラス階層内の全てのクラスを見ることができるように、
  実際にクラス変数を１つ共有してみましょう。
  クラスインスタンス変数はクラス変数より好まれます。

* <a name="visibility"></a>
  意図した使い方に沿って、可視性(`private`、`protected`)を設定しましょう。
  全てを`public`(デフォルトの設定)のままにしないようにしましょう。
  結局私達は今 *Ruby* を書いているのだから。 *Python* ではなく。
<sup>[[link](#visibility)]</sup>

* <a name="indent-public-private-protected"></a>
  `public`、`protected`、`private`は、適用するメソッド定義と同じインデントにしましょう。
  可視性を定義する識別子以降のメソッドに適用されることを強調するため、
  識別子の上下に空行を入れましょう。
<sup>[[link](#indent-public-private-protected)]</sup>

  ```Ruby
  class SomeClass
    def public_method
      # ...
    end

    private

    def private_method
      # ...
    end

    def another_private_method
      # ...
    end
  end
  ```

* <a name="blakline-public-private-protected"></a>メソッド定義の後で、そのメソッドの可視性を変更するために `private` や `protected` や `public` を引数付きで呼び出す場合は、メソッド定義とこれらのメソッド呼び出しの間に空行を入れてはならない。
<sup>[[link](#blankline-public-private-protected)]</sup>

    ```ruby
    class Foo
      # 良い例
      def foo
      end
      private :foo

      # 悪い例
      def foo
      end

      private :foo
    end
    ```

* <a name="space-public-private-protected"></a>
`private` や `protected` や `public` を引数なしで使用する場合、インデントレベルはメソッド定義と同じレベルとし、前後に1行ずつ空白を入れること。
<sup>[[link](#space-public-private-protected)]</sup>

    ```ruby
    # 良い例
    class Foo
      def foo
      end

      private

      def bar
      end
    end

    # 悪い例
    class Foo
      def foo
      end

    private

      def bar
      end
    end

    # 悪い例
    class Foo
      def foo
      end

      private

        def bar
        end
    end

    # 悪い例
    class Foo
      def foo
      end

      private
      def bar
      end
    end
    ```


* <a name="def-self-singletons"></a>
  シングルトンメソッドを定義するときは`def self.method`を用いましょう。
  クラス名を繰り返さないので、簡単にリファクタリングできるようになります。
<sup>[[link](#def-self-singletons)]</sup>

  ```Ruby
  class TestClass
    # 悪い例
    def TestClass.some_method
      # body omitted
    end

    # 良い例
    def self.some_other_method
      # body omitted
    end

    # たくさんのシングルトンメソッドを定義しなければならない時
    # この書き方も便利で、許容できます。
    class << self
      def first_method
        # body omitted
      end

      def second_method_etc
        # body omitted
      end
    end
  end
  ```

## 例外

* <a name="fail-method"></a>
  例外発生には`fail`を用いましょう。
  `raise`は例外をキャッチして、再度発生させるときにのみ使いましょう
  (何故なら、ここでは落ちるのではなく、明示的に目的を持って例外を発生させているからです)。
<sup>[[link](#fail-method)]</sup>

  ```Ruby
  begin
    fail 'Oops'
  rescue => error
    raise if error.message != 'Oops'
  end
  ```

* <a name="no-explicit-runtimeerror"></a>
  ２引数の`fail/raise`では、`RuntimeError`を明示しないようにしましょう。
<sup>[[link](#no-explicit-runtimeerror)]</sup>

  ```Ruby
  # 悪い例
  fail RuntimeError, 'message'

  # 良い例 - デフォルトでRuntimeErrorが発生します
  fail 'message'
  ```

* <a name="exception-class-messages"></a>
  例外インスタンスの代わりに、
  例外クラスとメッセージが分かれている`fail/raise`が好まれます。
<sup>[[link](#exception-class-messages)]</sup>

  ```Ruby
  # 悪い例
  fail SomeException.new('message')
  # `fail SomeException.new('message'), backtrace`とする書き方が存在しないことに注意しましょう。

  # 良い例
  fail SomeException, 'message'
  # `fail SomeException, 'message', backtrace`の用法と一貫性があります
  ```

* <a name="no-return-ensure"></a>
  `ensure`ブロックから`return`してはいけません。
  もし`ensure`から明示的に値を返したい場合は、
  `return`はどの例外発生よりも前に書いておき、
  例外など発生していなかったかのように値を返しましょう。
  事実上、例外は静かに捨てられます。
<sup>[[link](#no-return-ensure)]</sup>

  ```Ruby
  def foo
    begin
      fail
    ensure
      return 'very bad idea'
    end
  end
  ```

* <a name="begin-implicit"></a>
  可能な場所では、 *暗黙のbeginブロック* を用いましょう。
<sup>[[link](#begin-implicit)]</sup>

  ```Ruby
  # 悪い例
  def foo
    begin
      # main logic goes here
    rescue
      # failure handling goes here
    end
  end

  # 良い例
  def foo
    # main logic goes here
  rescue
    # failure handling goes here
  end
  ```

* <a name="contingency-methods"></a>
  *不確実性のあるメソッド*(Avdi Grimmによって作られた言葉です)
  を用いて`begin`の蔓延を和らげましょう。
<sup>[[link](#contingency-methods)]</sup>

  ```Ruby
  # 悪い例
  begin
    something_that_might_fail
  rescue IOError
    # handle IOError
  end

  begin
    something_else_that_might_fail
  rescue IOError
    # handle IOError
  end

  # 良い例
  def with_io_error_handling
    yield
  rescue IOError
    # handle IOError
  end

  with_io_error_handling { something_that_might_fail }

  with_io_error_handling { something_else_that_might_fail }
  ```

* <a name="dont-hide-exceptions"></a>
  例外をもみ消してはいけません。
<sup>[[link](#dont-hide-exceptions)]</sup>

  ```Ruby
  # 悪い例
  begin
    # an exception occurs here
  rescue SomeError
    # the rescue clause does absolutely nothing
  end

  # 悪い例
  do_something rescue nil
  ```

* <a name="no-rescue-modifiers"></a>
  `rescue`を修飾子として利用するのは避けましょう。
<sup>[[link](#no-rescue-modifiers)]</sup>

  ```Ruby
  # 悪い例 - StandardErrorとそれを継承した全てのクラスをキャッチしてしまいます
  read_file rescue handle_error($!)

  # 良い例 - Errno::ENOENTとそれを継承したクラスのみをキャッチします
  def foo
    read_file
  rescue Errno::ENOENT => ex
    handle_error(ex)
  end
  ```


* <a name="no-exceptional-flows"></a>
  制御フローに例外を使っては行けません。
<sup>[[link](#no-exceptional-flows)]</sup>

  ```Ruby
  # 悪い例
  begin
    n / d
  rescue ZeroDivisionError
    puts 'Cannot divide by 0!'
  end

  # 良い例
  if d.zero?
    puts 'Cannot divide by 0!'
  else
    n / d
  end
  ```

* <a name="no-blind-rescues"></a>
  `Exception`を`rescue`するのは避けましょう。
  これは`exit`のシグナルも捕捉するため、`kill -9`が必要になります。
<sup>[[link](#no-blind-rescues)]</sup>

  ```Ruby
  # 悪い例
  begin
    # calls to exit and kill signals will be caught (except kill -9)
    exit
  rescue Exception
    puts "you didn't really want to exit, right?"
    # exception handling
  end

  # 良い例
  begin
    # a blind rescue rescues from StandardError, not Exception as many
    # programmers assume.
  rescue => e
    # exception handling
  end

  # こちらも良い例
  begin
    # an exception occurs here

  rescue StandardError => e
    # exception handling
  end

  ```

* <a name="exception-ordering"></a>
  より詳細な例外を`rescue`チェーンの上に配置しましょう。
  そうでなければ、決して`rescue`されません。
<sup>[[link](#exception-ordering)]</sup>

  ```Ruby
  # 悪い例
  begin
    # 処理
  rescue Exception => e
    # エラー処理
  rescue StandardError => e
    # 決して到達しないエラー処理
  end

  # 良い例
  begin
    # 処理
  rescue StandardError => e
    # エラー処理
  rescue Exception => e
    # エラー処理
  end
  ```

* <a name="file-close"></a>
  外部リソースの含まれるプログラムでは、`ensure`で開放しましょう
<sup>[[link](#file-close)]</sup>

  ```Ruby
  f = File.open('testfile')
  begin
    # .. process
  rescue
    # .. handle error
  ensure
    f.close if f
  end
  ```

* <a name="standard-exceptions"></a>
  新しい例外クラスを導入するより、基本ライブラリの例外クラスを用いることが好まれます。
<sup>[[link](#standard-exceptions)]</sup>

## Collections

* <a name="literal-array-hash"></a>
  配列やハッシュのリテラルの方が好まれます。
  (コンストラクタに引数を渡す場合を除けば、ということですが)
 * 引数なしの `Array.new` `Hash.new` を使ってはならない。
 * 同じ要素を `n` 個持つ配列を初期化するときは `Array.new(n, obj)` を使用すること。`[obj] * n` と書いてはならない。

<sup>[[link](#literal-array-hash)]</sup>

  ```Ruby
  # 悪い例
  arr = Array.new
  hash = Hash.new

  # 良い例
  arr = []
  hash = {}
  ```

*  <a name="range-to-array"></a>
範囲リテラルを配列に変換するときは、`Range#to_a` ではなく `[*range]` を使うこと。
<sup>[[link](#range-to-array)]</sup>


    ```ruby
    # 良い例
    [*1..10]  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

    # bad
    (1..10).to_a  #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    ```

* <a name="percent-w"></a>
  (空文字列や、文字列内にスペースが入っていない)文字列の配列構文は、
  `%w`リテラルの方が好まれます。
  このルールは要素が２つ以上の配列に適用されます。
<sup>[[link](#percent-w)]</sup>

  ```Ruby
  # 悪い例
  STATES = ['draft', 'open', 'closed']

  # 良い例
  STATES = %w(draft open closed)
  ```

* <a name="percent-i"></a>
  シンボルの配列が必要なときは`%i`が好まれます
  (Ruby 1.9との互換性の維持が必要で無ければ)。
  このルールは要素が２つ以上の配列に適用されます。
<sup>[[link](#percent-i)]</sup>

  ```Ruby
  # 悪い例
  STATES = [:draft, :open, :closed]

  # 良い例
  STATES = %i(draft open closed)
  ```

* <a name="no-trailing-array-commas"></a>
  `Array`や`Hash`リテラルの最後の要素の後ろの`,`は避けましょう。
  複数行にわかれていない時は特に避けましょう。
<sup>[[link](#no-trailing-array-commas)]</sup>

  ```Ruby
  # 悪い例 - 簡単に要素を移動・追加・削除できますが、それでも好まれません。
  VALUES = [
             1001,
             2020,
             3333,
           ]

  # 悪い例
  VALUES = [1001, 2020, 3333, ]

  # 良い例
  VALUES = [1001, 2020, 3333]
  ```

* <a name="no-gappy-arrays"></a>
  配列に大きな隙間を作るのは避けましょう。
<sup>[[link](#no-gappy-arrays)]</sup>

  ```Ruby
  arr = []
  arr[100] = 1 # now you have an array with lots of nils
  ```

* <a name="first-and-last"></a>
  配列の最初や最後にアクセスしたいときは、
  `[0]`や`[-1]`より`first`や`last`が好まれます。
<sup>[[link](#first-and-last)]</sup>

* <a name="set-vs-array"></a>
  要素が一意のものを扱うときは、`Array`の代わりに`Set`を用いましょう。
  `Set`は重複がない要素が順序を持たないように実装されています。
  これは`Array`の直感的な内部操作と、`Hash`の要素発見の速さが合わさっています。
<sup>[[link](#set-vs-array)]</sup>






* <a name="list-literals-indent"></a>
配列リテラルを複数行で書く場合、`[` の後ろで改行し、要素行のインデントを1レベル下げ、`]` は独立した行に置いて `[` を書いた行の行頭にインデントを揃える。
<sup>[[link](#list-literals-indent)]</sup>

    ```ruby
    # 良い例
    [
      :foo,
      :bar,
      :baz
    ]

    # 悪い例
    [:foo,
      :bar,
      :baz
    ]
    ```

* <a name="list-literals-indent-assignment"></a>
代入の後ろに配列リテラルを複数行で書く場合は、`[` の後ろで改行し、要素行のインデントを1レベル下げ、`]` は独立した行に置いて `[` を書いた行の行頭にインデントを揃える。
<sup>[[link](#list-literals-indent-assignment)]</sup>

    ```ruby
    # 良い例
    array = [
      :foo,
      :bar,
      :baz
    ]

    # 悪い例
    array = [ :foo,
              :bar,
              :baz, ]

    # 悪い例
    array = [ :foo,
              :bar,
              :baz,
            ]

    # 悪い例
    array = [
      :foo,
      :bar,
      :baz, ]
    ```



* <a name="symbols-as-keys"></a>
  ハッシュのキーには文字列よりシンボルが好まれます。
<sup>[[link](#symbols-as-keys)]</sup>

  ```Ruby
  # 悪い例
  hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

  # 良い例
  hash = { one: 1, two: 2, three: 3 }
  ```

* <a name="no-mutable-keys"></a>
  変更のできるオブジェクトをハッシュのキーに使うのは避けましょう。
<sup>[[link](#no-mutable-keys)]</sup>

* <a name="hash-literals"></a>
  ハッシュのキーがシンボルの時は、Ruby 1.9のハッシュリテラル記法を用いましょう。
<sup>[[link](#hash-literals)]</sup>

  ```Ruby
  # 悪い例
  hash = { :one => 1, :two => 2, :three => 3 }

  # 良い例
  hash = { one: 1, two: 2, three: 3 }
  ```

* <a name="no-mixed-hash-syntaces"></a>
  Ruby 1.9のハッシュ記法とハッシュロケットを同じハッシュリテラル内で混在させてはいけません。
  シンボルでないキーがある場合は、ハッシュロケット記法を続けなければなりません。
 * ハッシュリテラルの key が symbol だが、`.`, `-`等を含む場合、HashRocket に統一するのが望ましい。
<sup>[[link](#no-mixed-hash-syntaces)]</sup>

  ```Ruby
  # 悪い例
  { a: 1, 'b' => 2 }

  # 良い例
  { :a => 1, 'b' => 2 }
  ```

    ```ruby
    # 良い例
    { :cookpad => 42,
      :'cookpad.com' => 'foo',
    }
    # bad
    { cookpad: 42,
      :'cookpad.com' => 'foo',
    }
    ```




* <a name="hash-literals-indent"></a>
ハッシュリテラルを単独で複数行で書く場合は、`{` の後で改行し、要素行のインデントを1レベル下げ、`}` は独立した行に置いて `{` を書いた行の行頭にインデントを揃える。
<sup>[[link](#hash-literals-indent)]</sup>
    ```ruby
    # 悪い例
    { first: 42,
      second: 'foo',
    }

    # 良い例
    {
      first: 42,
      second: 'foo',
    }

    # 悪い例
    { first: 42,
    second: 'foo',
    }
    ```

* <a name="hash-literals-indent-assignment"></a>
代入の後ろに配列リテラルを複数行で書く場合は、`{` の後で改行し、要素行のインデントを1レベル下げ、`}` は独立した行に置いて `{` を書いた行の行頭にインデントを揃える。
<sup>[[link](#hash-literals-indent-assignment)]</sup>

    ```ruby
    # 良い例
    hash = {
      first: 42,
      second: 'foo',
    }

    # 悪い例
    hash = { first: 42,
             second: 'foo',
           }

    # 悪い例
    hash = { first: 42,
             second: 'foo',
    }

    # 悪い例
    hash = { first: 42,
      second: 'foo',
    }

    # 悪い例
    hash = {
    first: 42,
    second: 'foo',
    }
    ```

* <a name="hash-key"></a>
  `Hash#has_key?`より`Hash#key?`を、
  `Hash#has_value?`より`Hash#value?`を用いましょう。
    [ここ](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/43765)
  でMatzが述べているように、長い記法は廃止が検討されています。
<sup>[[link](#hash-key)]</sup>

  ```Ruby
  # 悪い例
  hash.has_key?(:test)
  hash.has_value?(value)

  # 良い例
  hash.key?(:test)
  hash.value?(value)
  ```

* <a name="hash-fetch"></a>
  存在すべきキーを扱う時は、`Hash#fetch`を用いましょう。
<sup>[[link](#hash-fetch)]</sup>

  ```Ruby
  heroes = { batman: 'Bruce Wayne', superman: 'Clark Kent' }
  # 悪い例 - もし誤りがあってもすぐに気づくことができないかもしれません
  heroes[:batman] # => "Bruce Wayne"
  heroes[:supermann] # => nil

  # 良い例 - fetchはKeyErrorを投げるので、問題が明らかになります
  heroes.fetch(:supermann)
  ```

* <a name="hash-fetch-defaults"></a>
  独自のロジックを用いないようにするため、
  `Hash#fetch`経由でデフォルト値を導入しましょう。
<sup>[[link](#hash-fetch-defaults)]</sup>

  ```Ruby
  batman = { name: 'Bruce Wayne', is_evil: false }

  # 悪い例 - falseと判定される値が入っていた場合、望んだ通りに動かないかもしれません
  batman[:is_evil] || true # => true

  # 良い例 - falseと判定される値が入っていても正しく動きます
  batman.fetch(:is_evil, true) # => false
  ```

* <a name="use-hash-blocks"></a>
  `Hash#fetch`では、デフォルト値の代わりにブロックを用いることが好まれます。
<sup>[[link](#use-hash-blocks)]</sup>

  ```Ruby
  batman = { name: 'Bruce Wayne' }

  # 悪い例 - デフォルト値が使われると、先に評価してしまいます
  # だから、もし複数回呼ばれると、プログラムが遅くなります
  batman.fetch(:powers, get_batman_powers) # get_batman_powers は高価な呼び出しです

  # 良い例 - ブロックは後から評価されます。だから、KeyErrorが評価の引き金になります
  batman.fetch(:powers) { get_batman_powers }
  ```

* <a name="hash-values-at"></a>
  ハッシュから連続して複数の値が必要になる時は、`Hash#values_at`を用いましょう。
<sup>[[link](#hash-values-at)]</sup>

  ```Ruby
  # 悪い例
  email = data['email']
  nickname = data['nickname']

  # 良い例
  email, username = data.values_at('email', 'nickname')
  ```

* <a name="ordered-hashes"></a>
  Ruby 1.9現在ではハッシュは順序付けられるということを信頼しましょう。
<sup>[[link](#ordered-hashes)]</sup>

* <a name="no-modifying-collections"></a>
  コレクションを走査している時に変更を加えてわいけません。
<sup>[[link](#no-modifying-collections)]</sup>

## 変数
* <a name="multi-use-variable"></a>
 ひとつの変数を異なる役割のために使い回してはならない。そうしたくなる場合は、メソッドを複数のメソッドに分割できる。
<sup>[[link](#multi-use-variable)]</sup>

* <a name="local-variable-scope"></a>
 ローカル変数のスコープ (有効範囲) を可能な限り小さくすること。ローカル変数が存在しないメソッドは良いメソッドである。
<sup>[[link](#local-variable-scope)]</sup>

## 文字列
* 空文字列は '' と書くこと。
 *  理由が無い限り引数なしの String.new を使ってはならない。
* 文字列内部のエスケープシーケンスが最も少なくなるよう、適切な区切り記号を選択すること。

* <a name="string-interpolation"></a>
  文字列連結の代わりに文字列挿入や文字列整形を好みます。
<sup>[[link](#string-interpolation)]</sup>

  ```Ruby
  # 悪い例
  email_with_name = user.name + ' <' + user.email + '>'

  # 良い例
  email_with_name = "#{user.name} <#{user.email}>"

  # 良い例
  email_with_name = format('%s <%s>', user.name, user.email)
  ```
* 式展開の中で Object#to_s だけを呼び出してはならない。たとえばこういうの→"#{obj.to_s}"
* 式展開の中括弧を省略してはならない。
* <a name="pad-string-interpolation"></a>
  文字列挿入時にはスペースを入れることを検討しましょう。
  文字列から分かれたコードがより明確になります。
<sup>[[link](#pad-string-interpolation)]</sup>

  ```Ruby
  "#{ user.last_name }, #{ user.first_name }"
  ```

* <a name="consistent-string-literals"></a>
  一貫した文字列リテラルの引用記号のスタイルを採用しましょう。
  Rubyコミュニティには2つの有名なスタイル - デフォルトでシングルクォートを用いるもの (Option A)、
  ダブルクォートを用いるもの (Option B) - があり、
  どちらも良いと考えられています。
　　文字列内部のエスケープシーケンスが最も少なくなるよう、適切な区切り記号を選択すること。
<sup>[[link](#consistent-string-literals)]</sup>

  * **(Option A)** 文字列挿入の必要がないときや、`\t`や`\n`｀’｀等の特別な文字がない場合は、
    シングルクォーテーションが好まれます。

    ```Ruby
    # 悪い例
    name = "Bozhidar"

    # 良い例
    name = 'Bozhidar'
    ```

  * **(Option B)** 文字列中に`"`を含んでいたり、エスケープ文字を抑えたいときでない限り、
    ダブルクォーテーションが好まれます。

    ```Ruby
    # 悪い例
    name = 'Bozhidar'

    # 良い例
    name = "Bozhidar"
    ```

  Rubyコミュニティの中では、ほぼ間違いなく
  ２つ目のスタイルの方が有名です。
  しかしながら、このガイド内の文字列リテラル表記は、
  １つ目のスタイルを採用しています。

* <a name="persent-string-literals"></a>`%` 記法で文字列リテラルを書く場合は、括弧を区切り記号に使用すること。括弧の種類はどれでも良い。ただし、次のような特殊な場合は括弧以外を区切り記号として良い:
<sup>[[link](#persent-string-literals)]</sup>

    ```ruby
    OPEN_PARENTHESES = %!({[!
    ```

* <a name="no-character-literals"></a>
  文字リテラル構文`?x`を用いてはいけません。
<sup>[[link](#no-character-literals)]</sup>

  ```Ruby
  # 悪い例
  char = ?c

  # 良い例
  char = 'c'
  ```

* <a name="curlies-interpolate"></a>
  文字列の中の挿入されるインスタンス変数やグローバル変数の周りの
  `{}`は省略してはいけません。
<sup>[[link](#curlies-interpolate)]</sup>

  ```Ruby
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    # 悪い例 - 有効ですが不格好です
    def to_s
      "#@first_name #@last_name"
    end

    # 良い例
    def to_s
      "#{@first_name} #{@last_name}"
    end
  end

  $global = 0
  # 悪い例
  puts "$global = #$global"

  # 良い例
  puts "$global = #{$global}"
  ```

* <a name="no-to-s"></a>
  文字列に挿入するときに`Object#to_s`を使ってはいけません。
  自動的に呼び出されます。
<sup>[[link](#no-to-s)]</sup>

  ```Ruby
  # 悪い例
  message = "This is the #{result.to_s}."

  # 良い例
  message = "This is the #{result}."
  ```

* <a name="concat-strings"></a>
  大きなデータの塊を作る必要があるときは、`String#+`の使用は避けましょう。
  代わりに、`String#<<`を使いましょう。
  連結`String#<<`は、文字列インスタンスを直接書き換えるため、
  `String#+`よりも常に速いです、
  `String#+`はたくさんの新しいオブジェクトを作ってしまいます。
<sup>[[link](#concat-strings)]</sup>

  ```Ruby
  # 良く、そして速い例
  html = ''
  html << '<h1>Page title</h1>'

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
  ```

* <a name="heredocs"></a>
  複数行のヒアドキュメントを用いるときは、
  先頭のスペースも保持してしまうということを頭に入れておかなければなりません。
  過剰なスペースを取り除くためのマージンを採用するのを実用的です。
<sup>[[link](#heredocs)]</sup>

  ```Ruby
  code = <<-END.gsub(/^\s+\|/, '')
    |def test
    |  some_method
    |  other_method
    |end
  END
  # => "def test\n  some_method\n  other_method\nend\n"
  ```
* <a name="loop-include-strings"></a>
ループ内で文字列リテラルを書いてはならない。ここでループとは `while`、`until`、`for`、およびイテレータ (`each` などのブロックを何度も呼び出すブロック付きメソッド呼び出しのブロック内) である。
<sup>[[link](#loop-include-strings)]</sup>

## 正規表現

* <a name="no-regexp-for-plaintext"></a>
  単にプレーンテキストを文字列中から探すだけの時は、
  正規表現を使ってはいけません: `string['text']`を使いましょう。
<sup>[[link](#no-regexp-for-plaintext)]</sup>

* <a name="regexp-string-index"></a>
  単純化のため、文字列の添字に直接正規表現を渡しましょう。
<sup>[[link](#regexp-string-index)]</sup>

  ```Ruby
  match = string[/regexp/]             # get content of matched regexp
  first_group = string[/text(grp)/, 1] # get content of captured group
  string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
  ```

* <a name="non-capturing-regexp"></a>
  捕捉した結果を使う必要のないとき、捕捉しないグループを用いましょう。
 * 必要のない後方参照グループを作成してはならない。`(?: ... )` を使うこと。
<sup>[[link](#non-capturing-regexp)]</sup>

  ```Ruby
  /(first|second)/   # 悪い例
  /(?:first|second)/ # 良い例
  ```

* <a name="no-perl-regexp-last-matchers"></a>
  最後に正規表現にマッチした値を示すPerlレガシーの暗号的な変数を用いてはいけません
  (`$1`、`$2`など)。
  代わりに`Regexp.last_match[n]`を用いましょう。
<sup>[[link](#no-perl-regexp-last-matchers)]</sup>

  ```Ruby
  /(regexp)/ =~ string
  ...

  # 悪い例
  process $1

  # 良い例
  process Regexp.last_match[1]
  ```


* <a name="no-numbered-regexes"></a>
  どの値が入っているか追うのが困難になるので、
  順序付けられたグループを使うのは避けましょう。
  代わりに名付けられたグループを使いましょう。
<sup>[[link](#no-numbered-regexes)]</sup>

  ```Ruby
  # 悪い例
  /(regexp)/ =~ string
  ...
  process Regexp.last_match[1]

  # 良い例
  /(?<meaningful_var>regexp)/ =~ string
  ...
  process meaningful_var
  ```
* <a name="limit-escapes"></a>
  文字クラスの中では、特別な意味を持つ文字が少ないので注意が必要です:
  `^`、`-`、`\`、`]`のみが特別な意味を持つので、
  `.`を`[]`の中でエスケープしてはいけません。
<sup>[[link](#limit-escapes)]</sup>

* <a name="caret-and-dollar-regexp"></a>
  `^`や`$`は、文字列の先頭や末尾ではなく、
  行頭や行末にマッチするので注意が必要です。
  もし文字列全体の先頭末尾にマッチさせたいときは、
  `\A`、`\z`を使いましょう
  (`\n?\z`と等価である`\Z`と混同しないようにしましょう)。
<sup>[[link](#caret-and-dollar-regexp)]</sup>

  ```Ruby
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\z/] # don't match
  ```

* <a name="comment-regexes"></a>
  複雑な正規表現には`x`識別子を用いましょう。
  これを用いることで、より読みやすくなり、
  便利なコメントを使えるようになります。
  スペースが無視されることに注意しましょう。
 * 具体例は [uri/common.rb で定義されている正規表現](https://github.com/ruby/ruby/blob/trunk/lib/uri/common.rb#L457) が参考になる。
<sup>[[link](#comment-regexes)]</sup>

  ```Ruby
  regexp = /
    start         # some text
    \s            # white space char
    (group)       # first group
    (?:alt1|alt2) # some alternation
    end
  /x
  ```

* <a name="gsub-blocks"></a>
  `sub`/`gsub`での複雑な置換は、ブロックやハッシュを用いることで実現できます。
<sup>[[link](#gsub-blocks)]</sup>


## 代入式

* <a name="tuple"></a>
複合代入はリテラルまたは引数なしのメソッド呼び出しの結果を代入する場合、および2つの変数または属性の値を交換する場合のみ使ってよい。
<sup>[[link](#tuple)]</sup>

* <a name="assignment-space"></a>
代入記号の両側に空白を入れること。
<sup>[[link](#assignment-space)]</sup>

## パーセントリテラル

* <a name="percent-q-shorthand"></a>
  挿入と`"`双方が入る１行の文字列には、
  `%()`(`%Q()`の短縮形)を使いましょう。
  複数行の時はヒアドキュメントを好みます。
<sup>[[link](#percent-q-shorthand)]</sup>

  ```Ruby
  # 悪い例 (挿入の必要がありません)
  %(<div class="text">Some text</div>)
  # should be '<div class="text">Some text</div>'

  # 悪い例 (ダブルクォートがありません)
  %(This is #{quality} style)
  # should be "This is #{quality} style"

  # 悪い例 (複数行です)
  %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
  # should be a heredoc.

  # 良い例 (挿入が必要、ダブルクォートがある、そして１行です)
  %(<tr><td class="name">#{name}</td>)
  ```

* <a name="percent-q"></a>
  文字列に`'`と`"`双方が含まれない限り、
  `%q`の使用は避けましょう。
  たくさんの文字列をエスケープしなくてもよいときは、
  通常の文字列リテラルのほうがより読みやすく、
  推奨されるべきです。
<sup>[[link](#percent-q)]</sup>

  ```Ruby
  # 悪い例
  name = %q(Bruce Wayne)
  time = %q(8 o'clock)
  question = %q("What did you say?")

  # 良い例
  name = 'Bruce Wayne'
  time = "8 o'clock"
  question = '"What did you say?"'
  ```

* <a name="percent-r"></a>
  '/'が１つ *より多い* 正規表現に限り、`%r`を使いましょう。
<sup>[[link](#percent-r)]</sup>

  ```Ruby
  # 悪い例
  %r(\s+)

  # こちらも悪い例
  %r(^/(.*)$)
  # should be /^\/(.*)$/

  # 良い例
  %r(^/blog/2011/(.*)$)
  ```

* <a name="percent-x"></a>
  呼び出すコマンドにバッククォートが含まれる(かなり起こりえないが)ことがない限り、
  `%x`の使用は避けましょう。
<sup>[[link](#percent-x)]</sup>

  ```Ruby
  # 悪い例
  date = %x(date)

  # 良い例
  date = `date`
  echo = %x(echo `date`)
  ```

* <a name="percent-s"></a>
  `%s`の使用は避けましょう。
  Rubyコミュニティは、スペース含むシンボルを作る時は
  `:"文字列"`がよいと決めたようです。
<sup>[[link](#percent-s)]</sup>

* <a name="percent-literal-braces"></a>
  パーセントリテラルの区切り文字は、`%r`を除いて`()`が好まれます。
  正規表現の中では、`()`は色々なシーンで使われるので、
  正規表現の内容によっては、より使われる機会の少ない`{`のほうが
  良い選択となることがあるかもしれません。
<sup>[[link](#percent-literal-braces)]</sup>

  ```Ruby
  # 悪い例
  %w[one two three]
  %q{"Test's king!", John said.}

  # 良い例
  %w(one two three)
  %q("Test's king!", John said.)
  ```

## ブロック

* <a name="block-argument-call"></a>
ブロックの呼び出しは、`yield`を用い、`block.call`は使いません。
記述量だけでなく、実行速度においても優れている為です。
<sup>[[link](#block-argument-call)]</sup>

[参考](https://speakerdeck.com/sferik/writing-fast-ruby)

## メタプログラミング

* <a name="no-metaprogramming-masturbation"></a>
  不要なメタプログラミングは避けましょう。Avoid needless metaprogramming.
<sup>[[link](#no-metaprogramming-masturbation)]</sup>

* <a name="no-monkey-patching"></a>
  ライブラリに書かれているコアなクラスを汚すのはやめましょう
  (モンキーパッチを当ててはいけません)。
<sup>[[link](#no-monkey-patching)]</sup>

* <a name="block-class-eval"></a>
  ブロック渡しの`class_eval`のほうが、文字列挿入型よりも好ましいです。
  - 文字列挿入型を使う時は、バックトレースが働くように、常に`__FILE__`と`__LINE__`を渡しましょう:
<sup>[[link](#block-class-eval)]</sup>

  ```ruby
  class_eval 'def use_relative_model_naming?; true; end', __FILE__, __LINE__
  ```

  - `define_method`の方が、`class_eval{ def ... }`よりも好まれます。

* <a name="eval-comment-docs"></a>
  文字列挿入型の`class_eval`(または他の`eval`)を用いる時は、
  挿入されたときのコードをコメントに追加しましょう(Railsでは活用されています)。
<sup>[[link](#eval-comment-docs)]</sup>

  ```ruby
  # from activesupport/lib/active_support/core_ext/string/output_safety.rb
  UNSAFE_STRING_METHODS.each do |unsafe_method|
    if 'String'.respond_to?(unsafe_method)
      class_eval <<-EOT, __FILE__, __LINE__ + 1
        def #{unsafe_method}(*args, &block)       # def capitalize(*args, &block)
          to_str.#{unsafe_method}(*args, &block)  #   to_str.capitalize(*args, &block)
        end                                       # end

        def #{unsafe_method}!(*args)              # def capitalize!(*args)
          @dirty = true                           #   @dirty = true
          super                                   #   super
        end                                       # end
      EOT
    end
  end
  ```

* <a name="no-method-missing"></a>
  `method_missing`を用いたメタプログラミングは避けましょう。
  何故なら、バックトレースが面倒になり、
  `#methods`のリストの中に出てこず、
  ミススペルしたメソッド呼び出しも無言で動いてしまいます、
  例えば`nukes.launch_state = false`のようにです。
  代わりに、移譲、プロキシ、または`define_method`を使いましょう。
  もし`method_missing`を使わなければならない時は:
<sup>[[link](#no-method-missing)]</sup>

  - [`respond_to_missing?`](http://blog.marc-andre.ca/2010/11/methodmissing-politely.html)も実装されているか確かめましょう
  - 既知の接頭辞、`find_by_*`のようなものを捕捉しましょう -- 可能な限りアサートさせましょう
  - 最後に`super`を呼び出しましょう
  - アサートする、特別でないメソッドに移譲しましょう:

  ```ruby
  # 悪い例
  def method_missing?(meth, *args, &block)
    if /^find_by_(?<prop>.*)/ =~ meth
      # ... lots of code to do a find_by
    else
      super
    end
  end

  # 良い例
  def method_missing?(meth, *args, &block)
    if /^find_by_(?<prop>.*)/ =~ meth
      find_by(prop, *args, &block)
    else
      super
    end
  end

  # それでも最も良い選択は、発見できる全てのアトリビュートにdefine_methodすることです
  ```

## 雑則

* <a name="always-warn"></a>
  `ruby -w`で安全なコードを書きましょう。
<sup>[[link](#always-warn)]</sup>

* <a name="no-optional-hash-params"></a>
  オプショナルな変数としてのハッシュの使用を避けましょう。やりすぎてはいませんか？(オブジェクトの初期化はこのルールの例外です)
<sup>[[link](#no-optional-hash-params)]</sup>

* <a name="short-methods"></a>
  コードのある行が10行を超えるメソッドは避けましょう。
  理想を言えば、多くのメソッドは5行以内がよいです。
  空行は行数には含めません。
<sup>[[link](#short-methods)]</sup>

* <a name="too-many-params"></a>
  ３つや４つ以上引数を設定するのは避けましょう。
<sup>[[link](#too-many-params)]</sup>

* <a name="private-global-methods"></a>
  もし本当に"global"なメソッドが必要な場合は、
  Kernelに定義し、privateに設定しましょう。
<sup>[[link](#private-global-methods)]</sup>

* <a name="instance-vars"></a>
  グローバル変数の代わりに、モジュールのインスタンス変数を使用しましょう。
<sup>[[link](#instance-vars)]</sup>

  ```Ruby
  # 悪い例
  $foo_bar = 1

  # 良い例
  module Foo
    class << self
      attr_accessor :bar
    end
  end

  Foo.bar = 1
  ```

* <a name="alias-method"></a>
  `alias_method`が動く時は、`alias`は避けましょう。
<sup>[[link](#alias-method)]</sup>

* <a name="optionparser"></a>
  複雑なコマンドラインオプションをパースするために`OptionParser`を使いましょう。
  また、些細なオプションには`ruby -s`を使いましょう。
<sup>[[link](#optionparser)]</sup>

* <a name="time-now"></a>
  現在のシステム時間を読み出すには、`Time.new`よりも`Time.now`を使いましょう。
<sup>[[link](#time-now)]</sup>

* <a name="functional-code"></a>
  それで問題ない時は、破壊的処理を避け関数型の手法でコードを書きましょう。
<sup>[[link](#functional-code)]</sup>

* <a name="no-arg-mutations"></a>
  それがメソッドの目的でない限り、引数に破壊的変更をするのはやめましょう。
<sup>[[link](#no-arg-mutations)]</sup>

* <a name="destructive_method"></a>
破壊的メソッドを使用する際は、その副作用を最小限の範囲に留めること。
<sup>[[link](#destructive_method)]</sup>

* <a name="three-is-the-number-thou-shalt-count"></a>
  ３段階を超えるネストは避けましょう。
<sup>[[link](#three-is-the-number-thou-shalt-count)]</sup>

* <a name="be-consistent"></a>
  一貫性を保ちましょう。理想を言えば、このガイドラインに沿いましょう。
<sup>[[link](#be-consistent)]</sup>

* <a name="common-sense"></a>
  常識を用いましょう。
<sup>[[link](#common-sense)]</sup>

## ツール

ここでは、このガイドに反するコードを自動的にチェックするのを支援するツールをいくつか紹介します。

### RuboCop

[RuboCop](https://github.com/bbatsov/rubocop)は、このガイドに基づいた
Rubyコードスタイルチェッカーです。
Rubocopはすでにこのガイドの重要な部分をカバーしており、
MRI 1.9, MRI 2.0 双方をサポートし、Emacs向けのよいプラグインがあります。

### RubyMine

[RubyMine](http://www.jetbrains.com/ruby/) のコードインスペクションは、このガイドに
[部分的に基づいています](http://confluence.jetbrains.com/display/RUBYDEV/RubyMine+Inspections)。

## Be Consistent

&mdash;[Google C++ Style Guide][google-c++]

[airbnb-javascript]: https://github.com/airbnb/javascript
[bbatsov-ruby]: https://github.com/bbatsov/ruby-style-guide
[github-ruby]: https://github.com/styleguide/ruby
[google-c++]: http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml
[google-c++-comments]: http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments
[google-python-comments]: http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Comments
[ruby-naming-bang]: http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist
[cookpad-styleguide]: https://github.com/cookpad/styleguide/blob/master/ruby.ja.md
[moneyforward-corp]: http://corp.moneyforward.com/
[bojovs-com]: http://bojovs.com/2012/04/24/ruby-coding-style/
[bbatsov-rails]: https://github.com/bbatsov/rails-style-guide
[moneyforward-rails]: https://github.com/moneyforward/rails-style-guide

