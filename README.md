# JestとVue Test Utilsを利用したコンポーネントの単体テストに関して

今回、以下のライブラリ(フレームワーク)を利用する。

- Jest: テストフレームワーク
- Vue Test Utils: Vueコンポーネントをテストするためのライブラリ

## なぜJestを利用するのか

一言で言えば「全部入り」だから。

従来、テストをするためには複数のライブラリを組み合わせて利用する必要があった。

- テストランナー（テストフレームワーク）: テストの構造を作る機能や、テストの実行環境、検証結果のレポート機能などを提供
  - Karma
  - Mocha
  - Jasmine
  - AVA
  - tape
- アサーション: テスト結果が期待通りであるか判定する機能を提供
  - should.js
  - Expect
  - chai
  - power-assert
- テストモック・テストダブル: モック、スタブなどの機能を提供
  - Sinon.js
  - testdouble.js

_※テストランナーとテストフレームワークの定義は文献によって異なったので、今回を一緒のものとする_

自由に組み合わせて利用できる反面、テストの知見がない人にとってはどれを組み合わせれば良いのか判断ができない。また、判断をするためには、それぞれのライブラリをある程度学習する必要があり、そのような手間がテストに対してのモチベーションを低下を招いている。

Jest はそのような悩みを解消したライブラリであり、上記の機能が全て入っている。

そのため、Jest をインストールすれば、すぐにテストコードを書き始められる。

今回に限らず、「テストの興味はあるが、選択肢が多すぎてどれを利用すれば良いのかわからない。」と思っている人々はとりあえず Jest を使ってみれば良いと思う。

## Jestの基本的な使いかた

### インストール

```shell
npm i jest -D
# もしくは
# yarn add jest -D
```

### テスト対象とテストコードの準備

今回テストをするのは、引数を合計する`sum.js`

```js
const sum = (number, number2) => number + number2;

export default sum;
```

これをテストする`sum.spec.js`は以下のようになる。

```js
import sum from '@/utils/sum';

// sumという名前のdescribeブロックを作成する
// 要は「このブロックの中ではsumのテストをするぜ」みたいな宣言のようなもの
describe('sum', () => {
  // テスト内容（期待する動作とそれを確認するためのアサーション）を定義する
  it('adds 1 + 2 to equal 3', () => {
    // 実行するアサーション（テスト結果が期待通りであるか判定する関数）
    expect(sum(1, 2)).toBe(3);
  });
});
```

### テストを実行

オプションや設定ファイルがない状態で`jest`コマンドを実行すれば、デフォルトで以下を検出してテストを実行する。

- `__tests__`ディレクトリ内の`.js`、`.jsx`、`.ts`および`.tsx`ファイルd
- 拡張子が`.test`または`.spec`のファイル（`Component.test.js`や`Component.spec.js`など）
- `test.js`、`spec.js`

そのため、今回は`jest`コマンドを実行するだけで`sum.spec.js`のテストが実行され、以下のような出力になる。

```shell
$ jest
PASS  test/utils/sum.spec.js
  sum
    ✓ adds 1 + 2 to equal 3 (1ms)
```

### モック関数

## 何をテストするのか

[一般的なヒント | Vue Test Utils](https://vue-test-utils.vuejs.org/ja/guides/common-tips.html)では以下のように記載されている。

>UI コンポーネントでは、コンポーネントの内部実装の詳細に集中しすぎて脆弱なテストが発生する可能性があるため、完全なラインベースのカバレッジを目指すことはお勧めしません。

>コンポーネントのパブリックインターフェイスを検証するテストを作成し、内部をブラックボックスとして扱うことをお勧めします。単一のテストケースでは、コンポーネントに提供された入力（ユーザーのやり取りやプロパティの変更）によって、期待される出力（結果の描画またはカスタムイベントの出力）が行われることが示されます。

つまり、コンポーネントのテストの考え方は通常の関数のテストと同じで、入力に対して出力（描画、イベントの発火、メソッドが動作したかなど）が正しいかどうかをテストすれば良い。

コンポーネントに無茶苦茶複雑な内部実装があったとしてもそれはテストする必要はない。

イメージとして以下のような感じ。

<img src="./media/image-of-testing-component.jpg" />

### テスト項目

コンポーネントにおいてのテスト項目（入力に対しての出力）は主に以下の通り。

- コンポーネントの描画内容が正しいか
- コンポーネントの属性やクラスが正しいか
- propsが正しく受け取れるか
- methodsが正しく動作するか
- 特定のイベントが発火した際に、正常に動作しているか
- $emitでイベントが正しく発⽕するか
- slotが正しく動作するか

## Note

babel7でvue-jestを利用するためには`babel-core@7.0.0-bridge.0`が必要。

- https://github.com/vuejs/vue-jest/issues/160
- https://github.com/vuejs/vue-jest/pull/173/files/21527df0d950b4cfe11e75b16003ca591d270332
