import React, { useState, useEffect } from 'react';
import './App.css';

const WINNING_COMBINATIONS = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
  [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
  [0, 4, 8], [2, 4, 6]             // diagonals
];

const App = () => {
  const [board, setBoard] = useState(Array(9).fill(null));
  const [isXNext, setIsXNext] = useState(true);
  const [winner, setWinner] = useState(null);
  const [gameOver, setGameOver] = useState(false);
  const [isMultiplayer, setIsMultiplayer] = useState(false);
  const [playerXWins, setPlayerXWins] = useState(0);
  const [playerOWins, setPlayerOWins] = useState(0);
  const [draws, setDraws] = useState(0);

  useEffect(() => {
    if (!isXNext && !gameOver && !isMultiplayer) {
      setTimeout(() => botMove(), 1000);
    }
  }, [isXNext, board, gameOver, isMultiplayer]);

  const handleClick = (index) => {
    if (board[index] || gameOver) return;

    const newBoard = [...board];
    newBoard[index] = isXNext ? 'X' : 'O';
    setBoard(newBoard);
    setIsXNext(!isXNext);

    const winner = calculateWinner(newBoard);
    if (winner) {
      setWinner(winner);
      setGameOver(true);
      if (winner === 'X') setPlayerXWins(playerXWins + 1);
      if (winner === 'O') setPlayerOWins(playerOWins + 1);
    } else if (newBoard.every(cell => cell !== null)) {
      setGameOver(true);
      setDraws(draws + 1);
    }
  };

  const botMove = () => {
    const bestMove = getBestMove(board);
    const newBoard = [...board];
    newBoard[bestMove] = 'O';
    setBoard(newBoard);
    setIsXNext(true);

    const winner = calculateWinner(newBoard);
    if (winner) {
      setWinner(winner);
      setGameOver(true);
      if (winner === 'X') setPlayerXWins(playerXWins + 1);
      if (winner === 'O') setPlayerOWins(playerOWins + 1);
    }
  };

  const getBestMove = (board) => {
    const availableMoves = board.map((val, index) => (val === null ? index : null)).filter((index) => index !== null);
    
    for (let i = 0; i < availableMoves.length; i++) {
      const move = availableMoves[i];
      const testBoard = [...board];
      testBoard[move] = 'O';
      if (calculateWinner(testBoard) === 'O') return move;
    }
    return availableMoves[Math.floor(Math.random() * availableMoves.length)];
  };

  const calculateWinner = (board) => {
    for (let [a, b, c] of WINNING_COMBINATIONS) {
      if (board[a] && board[a] === board[b] && board[a] === board[c]) {
        return board[a];
      }
    }
    return null;
  };

  const resetGame = () => {
    setBoard(Array(9).fill(null));
    setIsXNext(true);
    setWinner(null);
    setGameOver(false);
  };

  const toggleMultiplayer = () => {
    setIsMultiplayer(!isMultiplayer);
    resetGame();
  };

  return (
    <div className="game-container">
      <h1>Tic-Tac-Toe</h1>
      <div className="scoreboard">
        <div className="score">
          <span>Player X: {playerXWins}</span>
        </div>
        <div className="score">
          <span>Bot O: {playerOWins}</span>
        </div>
        <div className="score">
          <span>Draws: {draws}</span>
        </div>
      </div>
      <div className="status">
        {winner
          ? `🎉 ${winner} wins!`
          : gameOver
          ? "It's a draw!"
          : `Next: ${isXNext ? 'Player 1 (X)' : isMultiplayer ? 'Player 2 (O)' : 'Bot (O)'}`}
      </div>
      <div className="board">
        {board.map((cell, index) => (
          <div
            key={index}
            className={`square ${cell}`}
            onClick={() => handleClick(index)}
          >
            {cell}
          </div>
        ))}
      </div>
      <button className="reset-btn" onClick={resetGame}>Start New Game</button>
      <button className="multiplayer-btn" onClick={toggleMultiplayer}>
        {isMultiplayer ? 'Switch to Single Player' : 'Switch to Multiplayer'}
      </button>
    </div>
  );
};

export default App;
