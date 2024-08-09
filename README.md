<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Draughts Game</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="board"></div>
    <script src="script.js"></script>
</body>
</html>
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
    background-color: #f0f0f0;
}

#board {
    display: grid;
    grid-template-columns: repeat(8, 50px);
    grid-template-rows: repeat(8, 50px);
    gap: 0;
}

.square {
    width: 50px;
    height: 50px;
}

.dark {
    background-color: #769656;
}

.light {
    background-color: #eeeed2;
}

.piece {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    margin: auto;
    display: flex;
    align-items: center;
    justify-content: center;
}

.red {
    background-color: red;
}

.black {
    background-color: black;
    color: white;
}

.king {
    border: 2px solid gold;
}
const board = document.getElementById('board');
const squares = [];
const pieces = [];
const rows = 8;
const cols = 8;
let currentPlayer = 'red';

// Initialize board
for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
        const square = document.createElement('div');
        square.className = 'square ' + ((row + col) % 2 === 0 ? 'light' : 'dark');
        square.dataset.row = row;
        square.dataset.col = col;
        board.appendChild(square);
        squares.push(square);
    }
}

// Initialize pieces
for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
        if ((row + col) % 2 !== 0 && (row < 3 || row > 4)) {
            const piece = document.createElement('div');
            piece.className = 'piece ' + (row < 3 ? 'red' : 'black');
            piece.draggable = true;
            piece.dataset.row = row;
            piece.dataset.col = col;
            piece.addEventListener('dragstart', dragStart);
            piece.addEventListener('dragend', dragEnd);
            squares[row * cols + col].appendChild(piece);
            pieces.push(piece);
        }
    }
}

// Drag and drop functions
function dragStart(e) {
    e.dataTransfer.setData('text', `${this.dataset.row},${this.dataset.col}`);
}

function dragEnd(e) {
    const [fromRow, fromCol] = e.dataTransfer.getData('text').split(',').map(Number);
    const toSquare = document.elementFromPoint(e.clientX, e.clientY);
    const toRow = Number(toSquare.dataset.row);
    const toCol = Number(toSquare.dataset.col);

    if (isValidMove(fromRow, fromCol, toRow, toCol)) {
        movePiece(fromRow, fromCol, toRow, toCol);
        if (toRow === 0 && currentPlayer === 'red' || toRow === 7 && currentPlayer === 'black') {
            promoteToKing(toRow, toCol);
        }
        currentPlayer = currentPlayer === 'red' ? 'black' : 'red';
    }
}

function isValidMove(fromRow, fromCol, toRow, toCol) {
    // Add your move validation logic here
    return (Math.abs(fromRow - toRow) === 1 && Math.abs(fromCol - toCol) === 1) && (toSquareEmpty(toRow, toCol));
}

function toSquareEmpty(row, col) {
    return !squares[row * cols + col].hasChildNodes();
}

function movePiece(fromRow, fromCol, toRow, toCol) {
    const piece = squares[fromRow * cols + fromCol].removeChild(squares[fromRow * cols + fromCol].firstChild);
    squares[toRow * cols + toCol].appendChild(piece);
    piece.dataset.row = toRow;
    piece.dataset.col = toCol;
}

function promoteToKing(row, col) {
    const piece = squares[row * cols + col].firstChild;
    piece.classList.add('king');
}
