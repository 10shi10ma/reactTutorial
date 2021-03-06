# Reactチュートリアル
Reactの公式チュートリアルの解説です。

# Step1 環境構築
ローカルの環境に、yarnまたはnpmが既にインストールされている状態からはじめます。  
コマンドはyarnの場合を記載しますので、npmを利用したい人は適宜変更してください。

***

package.jsonを作成
```
> yarn init --yes
```

package.jsonの内容を以下を参考に記入してください。
```json
{
  "name": "reactTutorial",
  "version": "1.0.0",
  "description": "React Tutorial",
  "author": "10shi10ma (https://github.com/10shi10ma)",
  "license": "MIT",
  "dependencies": {
    "react": "^16.2.0",
    "react-dom": "^16.2.0",
    "react-scripts": "^1.1.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}
```

***

必要に応じてgitignoreファイルを追加してください。[参考](https://github.com/10shi10ma/reactTutorial/commit/eae264af199130d13717e052a5aaceda1f8acb05)

***
package.jsonに書かれているパッケージをインストール
```
> yarn
```

これで環境構築は完了です。

# step2 チュートリアルの準備
Reactの公式チュートリアルでは、最低限のHTML、JS、CSSが提供された状態からスタートします。  
この準備では、チュートリアルを進めるのに最低限必要なReactのコンポーネントの表示までを行います。

***

プロジェクト直下にsrcディレクトリと、publicディレクトリを作成してください。
```
> mkdir src public
```

***

まず、必要最低限のHTMLファイルをpublicディレクトリに追加します。  
ファイル名はindex.htmlとしてください。  
ファイルの内容は[ここ](https://github.com/10shi10ma/reactTutorial/commit/ce666e4e8f33d8f25aa3db5fdb18ea49106792e5)を参照

***

次は、チュートリアルで利用する必要最低限のコンポーネントを定義したindex.jsファイルと、CSSファイルを追加します。  
ファイルの内容はindex.jsとindex.cssとしてください。  
ファイルの内容は[ここ](https://github.com/10shi10ma/reactTutorial/commit/78d9ac8d9b2fd018f720809b29d5bdf3d8bfb88e)を参照

***

これで最低限チュートリアルをスタートできる画面が作成できました。  
ブラウザで確認してみましょう。

```
> yarn start
```
[http://localhost:3000/](http://localhost:3000/)がブラウザで開かれて、3 * 3のテーブルが表示されていると思います。

# step3 BoardコンポーネントからSquareコンポーネントにデータを渡す
step2が完了した時点で、3つのReactのコンポーネントが定義された状態になっています。
- Square Component: 単一の```<button>```をレンダリングするコンポーネント
- Board Component: 9つの四角形をレンダリングするコンポーネント
- Game Component: 後で記入するプレースホルダをボードにレンダリングするコンポーネント

***

BoardコンポーネントからSquareコンポーネントへデータを渡します。  
renderSquareメソッド内を```<Square />;```から```<Square value={i}/>;```に書き換えてください。
```js
class Board extends React.Component {
  renderSquare(i) {
    return <Square value={i}/>;
  }
```

***

Squareコンポーネントのrenderメソッドで```{/* TODO */}```となっている箇所を```{this.props.value}```に書き換えてください。  
コンポーネント内では、```props```というオブジェクトを通して受け取ったデータへアクセスします。
```js
class Square extends React.Component {
  render() {
    return (
      <button className="square">
        {this.props.value}
      </button>
    );
  }
}
```

***

これで、各四角形の中に1~9の数字が表示されるようになりました。  

[step3での変更点](https://github.com/10shi10ma/reactTutorial/commit/e57c66009cf0e7ae4b5ebc7525489c233c242f74)

# Step4 Squareコンポーネントに状態を持たせる
Squareコンポーネントをクリックすると "X"が表示されるようにしましょう。  

***

Reactコンポーネントはコンストラクタ内でthis.stateを設定することでコンポーネントが状態(state)を持つことができます。正方形の現在の状態(state)を保存し、正方形がクリックされたときにそれを変更しましょう。

***

まず、Squareコンポーネントクラスにコンストラクタを追加してstateを初期化します。  
constructorの内容は以下のようにしてください。
```js
class Square extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: null,
    };
  }
```
これでSquareコンポーネントが状態(state)を持つことができるようになりました。

***

次に、Squareコンポーネントのrenderメソッドを書き換えます。  
これまでpropsを参照していたのを、stateを参照するようにします。  
```<button>```タグ内の```this.props.value```を```this.state.value```に変更してください。

***

あとは、Squareコンポーネントをクリックした時の動作を記述します。
renderメソッド内のbuttonタグを以下の内容に変更してください。
```js
<button className="square" onClick={() => this.setState({value: 'X'})}>
```
this.setStateが呼び出されると、渡されたstateでコンポーネントの状態が更新されて再レンダリングされます。

***

任意の正方形をクリックすると、その中にXが表示されるようになりました。

[step4での変更点](https://github.com/10shi10ma/reactTutorial/commit/971fd55d6c7c8c5315a89a2c64288c4336ac6f86)

# step5 親コンポーネントで状態を管理する
各Squareの状態を管理するために、Boardで各Square Componentのstateを持つようにします。
このようにReactでは、複数の子Componentからデータを集計したり子コンポーネントが相互にやりとりする場合は、親コンポーネントでstateを持つようにします。

***

Board Componentにコンストラクタを追加し、9つの正方形に対応する配列を持つ初期状態をstateに設定するように変更してください。
```js
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }
```

***

あとは、Square Componentに渡すデータをpropsから自身が持っているstateに変更します。
Board ComponentのrenderSquareメソッドの中身を以下のように変更してください。
```js
renderSquare(i) {
  return <Square value={this.state.squares[i]} />;
}
```

***

[step5での変更点](https://github.com/10shi10ma/reactTutorial/commit/affcaa8efbc99d94345843b2b1aa6642bc463a2c)

# step6 親コンポーネントの状態を更新する準備
Boardコンポーネントで、どの正方形が塗りつぶされているかを保存したいです。  
なのでSquare Componentが塗りつぶされた際、Board Componentのstateを更新する必要があります。
しかしコンポーネントのstateはプライベートなデータなので、Board ComponentのstateをSquare Componentから直接更新することはできません。

***

Reactで一般的なパターンは、正方形をクリックすると呼び出される関数をBoard ComponentからSquare Componentに渡します。  
Board ComponentのrenderSquareメソッドを以下の内容に書き換えてください。

```js
renderSquare(i) {
  return (
    <Square
      value={this.state.squares[i]}
      onClick={() => this.handleClick(i)}
    />
  );
}
```

***

Board Componentから受け取った関数を利用するようにSquare Componentのrenderメソッドを書き換えます。また、Boardでstateを保存するのでSquare Componentはstateを持たなくなります。よってSquare Componentからコンストラクタ定義を削除してください。

***

Square Componentの内容を以下の通りに変更してください。
```js
class Square extends React.Component {
  render() {
    return (
      <button className="square" onClick={() => this.props.onClick()}>
        {this.props.value}
      </button>
    );
  }
}
```

***

これで、正方形をクリックした際に、```<button>```のonClickに指定された```() => this.props.onClick()```関数が実行されます。  
```this.props.onClick()```はBoard Componentから渡された関数なので、実際は、Board Componentの```() => this.handleClick(i)```関数が実行されます。


[step6での変更点](https://github.com/10shi10ma/reactTutorial/commit/f55d9222978bf34f6f9c7364ae6c535572b3f341)

# step7 親のstateを更新するメソッドを追加
Square Componentがクリックされた時に呼ばれるhandleClickメソッドをBoard Componentに実装します。
以下の内容でhandleClickメソッドを追加してください。
```js
handleClick(i) {
  const squares = this.state.squares.slice();
  squares[i] = 'X';
  this.setState({squares: squares});
}
```

***

これでSquare Componentはstateを保持しません。  
Square Componentは、親コンポーネントのBoard Componentから値を受け取り、クリックされたときに親に通知するようになりました。

***

[step7での変更点](https://github.com/10shi10ma/reactTutorial/commit/6f96c21486bd6d5f904e2f46c05abc1b64627da7)

# step7.5 不変性(Immutability)の重要性
step7で紹介したように、.slice（）演算子を使用して変更を加える前に正方形の配列をコピーし、既存の配列の変更を行いませんでした。  
Reactのチュートリアルでは、「不変性(Immutability)が重要な理由」を説明しています。  
興味がある人は是非読んでみてください。  

***

[React Tutorial|Why Immutability Is Important](https://reactjs.org/tutorial/tutorial.html#why-immutability-is-important)

# step8 functional componentsにする
Square Componentはrenderメソッドのみ持っているので、```React.Component```を継承したクラスをやめて、functional componentsにします。  
functional componentsは単にpropsをとり、レンダリングすべきものを返す関数を書くだけです。

***

Squareコンポーネントの内容を以下のように変更してください。
```js
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```

***

[step8での変更点](https://github.com/10shi10ma/reactTutorial/commit/81975726ac719fcc276442328dad6a8303dc4cfc)

# step9 stateを追加する
今は正方形をクリックすると、必ずXの文字が正方形に表示されます。このチュートリアルで作っているゲームでは、XとOをの2つのユーザーが交代交代で正方形をクリックします。  
なので、Board Componentにターンの概念を追加します。  

***

Board Componentのconstructorのstateの初期状態を以下のように変更してください。
```js
constructor(props) {
  super(props);
  this.state = {
    squares: Array(9).fill(null),
    xIsNext: true,
  };
}
```

***

正方形がクリックされた際に表示する文字をxIsNextの値に応じて変化させます。  
またクリックされる度に、xIsNextの値を反転させて、stateを保存することによってxIsNextをトグルします。   
handleClickの処理を以下のように変更してください。
```js
handleClick(i) {
  const squares = this.state.squares.slice();
  squares[i] = this.state.xIsNext ? 'X' : 'O';
  this.setState({
    squares: squares,
    xIsNext: !this.state.xIsNext,
  });
}
```

***

Board Componentのrenderメソッドで指定したステータステキストを変更して、次のターンが誰かを表示します。
```js
render() {
    const status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    //..
```

***

これでBoard Componentにターンの概念が追加されました。

***

[step9での変更点](https://github.com/10shi10ma/reactTutorial/commit/ea0b27dfbe04c1ef118a99b238f67e1c7884b0f7)

# step10 勝敗を判定する
まず始めに、以下のヘルパー関数をindex.js内に追加してください。
```js
function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}
```

この関数は、配列のsquaresを渡すと、ゲームの勝敗がついている場合は、そのユーザー文字。ついていない場合はnullを返します。  

***

Square Componentのrenderメソッドで、勝敗が決まった場合はステータス文字に勝者を。決まっていない場合はステータス文字に、次のユーザーを表示できます。  
renderメソッドの内容を以下を参考に変更してください。
```js
render() {
    const winner = calculateWinner(this.state.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }
    // ..
```

***

最後に、勝敗が決まっている場合は正方形をクリックしても何も起こらないようにします。  
handleClickメソッドの処理を以下のように変更します。
```js
handleClick(i) {
  const squares = this.state.squares.slice();
  if (calculateWinner(squares) || squares[i]) {
    return;
  }
  squares[i] = this.state.xIsNext ? 'X' : 'O';
  this.setState({
    squares: squares,
    xIsNext: !this.state.xIsNext,
  });
}
```

***

これでゲームは完成しました！

***


[step10での変更点](https://github.com/10shi10ma/reactTutorial/commit/21401723b1df58c97daddb1287630876dfde5784)

# step11 トップレベルのコンポーネントにstateを引き上げる
ゲームが完成したので、これから、ボードの古い状態に戻って以前の動き(history)を見ることができるようにしましょう。

***

以下のようなオブジェクトをstateとして保持しましょう。
```js
history = [
  {
    squares: [
      null, null, null,
      null, null, null,
      null, null, null,
    ]
  },
  {
    squares: [
      null, null, null,
      null, 'X', null,
      null, null, null,
    ]
  },
  // ...
]
```

***

トップレベルのGame Componentがhistoryの表示を担当するようにします。
step5以降で、Square ComponentからBoard Componentに管理する情報(state)を引き上げたように、今回はBoard ComponentからGame Componentにstateを引き上げます。これでトップレベルのGame Componentで必要なすべての情報を得ることができます。

***

まず最初に、Game Componentにコンストラクタを追加してGameの初期状態を設定します。
以下を参考にコンストラクタを追加してください。
```js
class Game extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      history: [{
        squares: Array(9).fill(null),
      }],
      xIsNext: true,
    };
  }
  // ..
```

***

不要になったBoard Componentのstateを削除します。
まず、Board Componentのコンストラクタを削除してください。
次に、renderSquareメソッドではstateではなく、propsからデータにアクセスするようにします。
なので```this.state.squares[i]```を```this.props.squares[i]```に、```this.handleClick(i)```を```this.props.onClick(i)```に変更してください。


[step11での変更点](https://github.com/10shi10ma/reactTutorial/commit/ea4e0cf42ac8cb78bf79f90a36d230951b679dfa)

# step12 トップレベルのコンポーネントにクリックハンドラや計算処理を引き上げる
現在Board Componentにあるstatusの計算と正方形をクリックされた時の処理をGame Componentに移しましょう。

***

step11でGame Componentにボードの状態やターンを持ってきたので、statusの計算や現在のボードの状態をBoard Componentに渡すようにします。  
Game Componentのrenderメソッドを以下を参考に変更してください。
```js
render() {
  const history = this.state.history;
  const current = history[history.length - 1];
  const winner = calculateWinner(current.squares);

  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board
          squares={current.squares}
          onClick={(i) => this.handleClick(i)}
        />

      </div>
      <div className="game-info">
        <div>{status}</div>
        <ol>{/* TODO */}</ol>
      </div>
    </div>
  );
}
```

***

これでBoard Componentから```<div className = "status"> {status} </ div>```とstatusを計算するコードを削除できます。  
Board Componentのrenderメソッドは以下のようになります。
```js
render() {
  return (
    <div>
      <div className="board-row">
        {this.renderSquare(0)}
        {this.renderSquare(1)}
        {this.renderSquare(2)}
      </div>
      <div className="board-row">
        {this.renderSquare(3)}
        {this.renderSquare(4)}
        {this.renderSquare(5)}
      </div>
      <div className="board-row">
        {this.renderSquare(6)}
        {this.renderSquare(7)}
        {this.renderSquare(8)}
      </div>
    </div>
  );
}
```

***

次にhandleClickメソッドをBoard ComponentからGame Componentに移す必要があります。  
この際、最新のsquaresをhistoryオブジェクトに追加する処理を加えています。  
Game ComponentのhandleClickメソッドを以下を参考に変更してください。  
```js
handleClick(i) {
  const history = this.state.history;
  const current = history[history.length - 1];
  const squares = current.squares.slice();
  if (calculateWinner(squares) || squares[i]) {
    return;
  }
  squares[i] = this.state.xIsNext ? 'X' : 'O';
  this.setState({
    history: history.concat([{
      squares: squares,
    }]),
    xIsNext: !this.state.xIsNext,
  });
}
```

***

これでBoard ComponentはrenderSquareメソッドとrenderメソッドだけになりました。stateの初期化とクリックハンドラは両方ともGame Componentに存在することになります。

***

[step12での変更点](https://github.com/10shi10ma/reactTutorial/commit/ec80b7efac6788b314baac827434ff00e702c03c)

# step13 複数の要素を表示する
次は、操作履歴を表示します。  

***

正方形をクリックした操作履歴の各ステップを、ボタン```<button>```を含むリストアイテム```<li>```として作成します。  
Game Componentのrenderメソッドを以下を参考に変更してください。
```js
render() {
  const history = this.state.history;
  const current = history[history.length - 1];
  const winner = calculateWinner(current.squares);

  const moves = history.map((step, move) => {
    const desc = move ?
      'Go to move #' + move :
      'Go to game start';
    return (
      <li>
        <button onClick={() => this.jumpTo(move)}>{desc}</button>
      </li>
    );
  });

  let status;
  if (winner) {
    status = 'Winner: ' + winner;
  } else {
    status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
  }

  return (
    <div className="game">
      <div className="game-board">
        <Board
          squares={current.squares}
          onClick={(i) => this.handleClick(i)}
        />
      </div>
      <div className="game-info">
        <div>{status}</div>
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
```

***

Reactでは、複数の同じ要素を表示する場合に、配列のmapを活用します。

***

[step13での変更点](https://github.com/10shi10ma/reactTutorial/commit/ba5293bd2b4cb583d5bafb5be4043e8b4e679cca)

# step14 keyを指定する
現在、以下の警告が表示されます。

```
Warning: Each child in an array or iterator should have a unique “key” prop. Check the render method of “Game”.
```

***

これを解消するために、```<li>```にkeyを指定します。  
以下を参考にkeyを指定するように変更してください。
```js
const moves = history.map((step, move) => {
  const desc = move ?
    'Go to move #' + move :
    'Go to game start';
  return (
    <li key={move}>
      <button onClick={() => this.jumpTo(move)}>{desc}</button>
    </li>
  );
});
```

***

[step14での変更点](https://github.com/10shi10ma/reactTutorial/commit/51b2a555477bac26f7e016789928676bb604399e)

# step15 イベントハンドラですべてのstateを更新する
step13で作成した、ステップボタンのクリックハンドラとして定義したjumpToメソッドを作成します。

***

まずは、Game Componentのstateに現在表示しているステップを追加します。  
以下を参考にGame Componentのコンストラクタを変更してください。
```js
class Game extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      history: [{
        squares: Array(9).fill(null),
      }],
      stepNumber: 0,
      xIsNext: true,
    };
  }
```

***

次に、ゲームでstepNumberを更新するjumpToメソッドを定義します。  
また、その際はxIsNextを更新します。移動番号のインデックスが偶数の場合、xIsNextをtrueに設定します。
以下を参考に、Game ComponentにjumpToメソッドを追加してください。
```js
handleClick(i) {
  // this method has not changed
}

jumpTo(step) {
  this.setState({
    stepNumber: step,
    xIsNext: (step % 2) === 0,
  });
}

render() {
  // this method has not changed
}
```

***

正方形をクリックした際に、各stateを更新するようにします。  
以下を参考に、handleClickメソッドを変更してください。
```js
handleClick(i) {
  const history = this.state.history.slice(0, this.state.stepNumber + 1);
  const current = history[history.length - 1];
  const squares = current.squares.slice();
  if (calculateWinner(squares) || squares[i]) {
    return;
  }
  squares[i] = this.state.xIsNext ? 'X' : 'O';
  this.setState({
    history: history.concat([{
      squares: squares
    }]),
    stepNumber: history.length,
    xIsNext: !this.state.xIsNext,
  });
}
```

***

これで、stepNumberを参照することで、Gameの状態を表示することができます。  
Game Componentのrenderメソッドを以下を参考に変更してください。
```js
render() {
  const history = this.state.history;
  const current = history[this.state.stepNumber];
  const winner = calculateWinner(current.squares);
```

***

「 Go to」ボタンをクリックすると、Board Componentはすぐに更新され、その時のゲームの様子が表示されます。

***

[step15での変更点](https://github.com/10shi10ma/reactTutorial/commit/1e7c7c657eebbb53e3a23f4ad25ddd69c954eae9)

# 最後に
これで、React Tutorialは終了です。  
このTutorialでは、Reactのコンセプトである、 componentやprops、stateについて触れました。  
それぞれの詳細についてはdocumentを参照してください。
[React document](https://reactjs.org/docs/hello-world.html)