# TicTacToe
React Tic-Tac-Toe sample로 react 공부

# React

<aside>
💡 최종 렌더링 되는 것은 public>index.html

</aside>

public>index.html ➡️ src>index.js ➡️ src>app.js

- index.html이 본래의 html
- index.js에서 JSX스타일인 <App>을 실제 DOM의 root에 넣어서 렌더링되도록 함
- app.js에서 JSX스타일로 화면 작성
- app.js에서 라우터 등 이동, 하위 페이지들 렌더링

기본 개념

<aside>
💡 React 구성 요소는 두 개의 버튼과 같은 여러 개의 인접한 JSX 요소가 아닌 단일 JSX 요소를 반환해야 합니다.

</aside>

## 보드 만들기

```jsx
export default function Square() {
  return (
    <>
      <div className="board-row">
        <button className="square">1</button>
        <button className="square">2</button>
        <button className="square">3</button>
      </div>
      <div className="board-row">
        <button className="square">4</button>
        <button className="square">5</button>
        <button className="square">6</button>
      </div>
      <div className="board-row">
        <button className="square">7</button>
        <button className="square">8</button>
        <button className="square">9</button>
      </div>
    </>
  );
}
```

```jsx
.board-row {
  content: "";
  display: table;
}
```

함수로 구분하기

```jsx
import "./App.css";

function Square({ value }) {
  return <button className="square">{value}</button>;
}

function App() {
  return (
    <>
      <div className="board-row">
        <Square value={1} />
        <Square value={2} />
        <Square value={3} />
      </div>
      <div className="board-row">
        <Square value={4} />
        <Square value={5} />
        <Square value={6} />
      </div>
      <div className="board-row">
        <Square value={7} />
        <Square value={8} />
        <Square value={9} />
      </div>
    </>
  );
}

export default App;
```

## Onclick

Interactive Component 만들기

```jsx
function Square({ value }) {
  function handleClick({ value }) {
    console.log(`clicked!${value}`);
  }

  return (
    <button 
			className="square" 
			onClick={() => handleClick({ value })}
		>
      {value}
    </button>
  );
}
```

## useState()

React는 컴포넌트에서 호출하여 항목을 "기억"할 수 있는 특수 함수를 제공합니다 . in 상태 의 현재 값을 저장하고 `Square`, `Square`클릭하면 값이 변경됩니다.

```jsx
function Square() {
  const [value, setValue] = useState(null);

  function handleClick() {
		// 바로 변경되어 적용
    setValue("X");
  }

  return (
    <button className="square" onClick={handleClick}>
      {value}
    </button>
  );
}
```

By calling this `set` function from an `onClick` handler, you’re telling React to re-render that `Square` whenever its `<button>` is clicked. After the update, the `Square`’s `value` will be `'X'`, so you’ll see the “X” on the game board. Click on any Square, and “X” should show up:

⇒

<aside>
💡 set()함수를 통해서 state가 바뀌게 되면 다시 렌더링 된다.

⇒ 각 value는 각자의 state를 가지고 있음 (독립적)

</aside>

## Arrow Function

**이렇게 바로 입력하는 게 안 되는 이유!**

`<Square value={squares[0]} onSquareClick={handleClick(0)} />`

<aside>
💡 handleClick(0) 으로 하면 함수가 바로 실행된다.

그에 따라 state가 변경되고, 화면에 리렌더링된다.

이것이 무한반복으로 일어나게 되고, 결국 Too many re-renders 오류가 발생한다

</aside>

Here is why this doesn’t work. The `handleClick(0)` call will be a part of rendering the board component. Because `handleClick(0)` alters the state of the board component by calling `setSquares`, your entire board component will be re-rendered again. But this runs `handleClick(0)` again, leading to an infinite loop:

Console

Too many re-renders. React limits the number of renders to prevent an infinite loop.

**화살표 함수**

Notice the new `() =>` syntax. Here, `() => handleClick(0)` is an *arrow function,* which is a shorter way to define functions. When the square is clicked, the code after the `=>` “arrow” will run, calling `handleClick(0)`.

<aside>
💡 () ⇒ Arrow function을 활용하면 함수를 prop으로 넘기고, 클릭했을 때 화살표 뒤의 함수를 실행하게 된다

</aside>

<aside>
💡 Now that your state handling is in the `Board` component, the parent `Board` component passes props to the child `Square` components so that they can be displayed correctly. When clicking on a `Square`, the child `Square` component now asks the parent `Board` component to update the state of the board. When the `Board`’s state changes, both the `Board` component and every child `Square` re-renders automatically

➡️ Board 컴포넌트에서 state가 모두 handling되고 있고, 

부모인  Board 컴포넌트가 child인 Square 컴포넌트로 props를 전달하고, 제대로 보여지도록 한다.

Square를 클릭하면, 자식인 Square 컴포넌트가 부모 컴포넌트에 state를 update하도록 요청한다.

Board의 state가 변경되고 나면, Board와 Square 컴포넌트 모두 자동으로 re-render 된다!

</aside>

<aside>
💡 handleSomething > onSomething

➡️ handleSomething으로 onSomething 관리!

</aside>

## ****immutability (불변성)****

배열을 변경할 때 기존 배열을 복사해서 새로운 배열을 생성한 후, state를 변경하면 불변성을 지킬 수 있다

**<불변성 유지 장점>**

1. History 남길 수 있음
2. re-render할 필요 없을 때 값이 변했는지 비교하기 용이함

## 재클릭 방지

조건 비교 없이 간단하게 return 시키는 방법 있음!

```jsx
// 이미 클릭한 곳 재클릭 방지
if (squares[i]) {
  return;
}
```

## History

```jsx
function Game() {
  // state를 lift up 해서 square>board>game 가져오기
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  // map의 first:값, second:index
  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = "Go to Move #" + move;
    } else {
      description = "Go to game start";
    }

    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>

      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
```

# Tic-Tac-Toe 코드

전체 코드

```jsx
import "./App.css";
import { useState } from "react";

function Square({ value, onSquareClick }) {
  return (
    <button className="square" onClick={onSquareClick}>
      {value}
    </button>
  );
}

function Board({ xIsNext, squares, onPlay }) {
  function handleClick(i) {
    // 이미 클릭한 곳 재클릭 방지
    if (squares[i] || calculateWinner(squares)) {
      return;
    }

    const nextSquares = squares.slice(); // 배열 복사하기

    if (xIsNext) {
      nextSquares[i] = "X";
    } else {
      nextSquares[i] = "O";
    }

    onPlay(nextSquares);
  }

  // winner 없으면 null
  const winner = calculateWinner(squares);
  let status;
  if (winner) {
    status = "Winner: " + winner;
  } else {
    status = "Next player: " + (xIsNext ? "X" : "O");
  }

  return (
    <>
      <div className="status">{status}</div>
      <div className="board-row">
        <Square value={squares[0]} onSquareClick={() => handleClick(0)} />
        <Square value={squares[1]} onSquareClick={() => handleClick(1)} />
        <Square value={squares[2]} onSquareClick={() => handleClick(2)} />
      </div>
      <div className="board-row">
        <Square value={squares[3]} onSquareClick={() => handleClick(3)} />
        <Square value={squares[4]} onSquareClick={() => handleClick(4)} />
        <Square value={squares[5]} onSquareClick={() => handleClick(5)} />
      </div>
      <div className="board-row">
        <Square value={squares[6]} onSquareClick={() => handleClick(6)} />
        <Square value={squares[7]} onSquareClick={() => handleClick(7)} />
        <Square value={squares[8]} onSquareClick={() => handleClick(8)} />
      </div>
    </>
  );
}

// Winner 판별 함수
function calculateWinner(squares) {
  // 이기는 경우의 수
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

  // 해당 경우의 수가 모두 O 또는 모두 X인지 확인 (a,b,c 전부 같으면 됨)
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }

  return null;
}

function Game() {
  // state를 lift up 해서 square>board>game 가져오기
  const [history, setHistory] = useState([Array(9).fill(null)]);
  const [currentMove, setCurrentMove] = useState(0);
  const xIsNext = currentMove % 2 === 0;
  const currentSquares = history[currentMove];

  function handlePlay(nextSquares) {
    const nextHistory = [...history.slice(0, currentMove + 1), nextSquares];
    setHistory(nextHistory);
    setCurrentMove(nextHistory.length - 1);
  }

  function jumpTo(nextMove) {
    setCurrentMove(nextMove);
  }

  // map의 first:값, second:index
  const moves = history.map((squares, move) => {
    let description;
    if (move > 0) {
      description = "Go to Move #" + move;
    } else {
      description = "Go to game start";
    }

    return (
      <li key={move}>
        <button onClick={() => jumpTo(move)}>{description}</button>
      </li>
    );
  });

  return (
    <div className="game">
      <div className="game-board">
        <Board xIsNext={xIsNext} squares={currentSquares} onPlay={handlePlay} />
      </div>

      <div className="game-info">
        <ol>{moves}</ol>
      </div>
    </div>
  );
}
export default Game;
```

