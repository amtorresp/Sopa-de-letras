<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sopa de Letras - Antídotos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: #f0f0f0;
        }
        .game-container {
            margin: 20px;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .grid {
            display: grid;
            grid-template-columns: repeat(10, 40px);
            gap: 2px;
            margin-bottom: 20px;
        }
        .cell {
            width: 40px;
            height: 40px;
            border: 1px solid #ccc;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            cursor: pointer;
            user-select: none;
        }
        .cell.selected {
            background-color: #e6f3ff;
        }
        .cell.found {
            background-color: #90EE90;
        }
        .word-list {
            margin-top: 20px;
        }
        .word-list div {
            margin: 5px 0;
        }
        .found-word {
            text-decoration: line-through;
            color: #28a745;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h2>Sopa de Letras - Antídotos</h2>
        <div id="grid" class="grid"></div>
        <div class="word-list" id="wordList"></div>
    </div>

    <script>
        const puzzle = [
            ['N','A','L','O','X','O','N','A','P','R'],
            ['F','L','U','M','A','Z','E','N','I','L'],
            ['W','K','C','T','Y','H','J','M','B','O'],
            ['O','X','I','G','E','N','O','V','W','X'],
            ['N','D','P','Q','S','T','U','Y','Z','A'],
            ['C','A','B','M','N','O','P','Q','R','C'],
            ['D','E','F','G','H','I','J','K','L','M'],
            ['T','U','V','W','X','Y','Z','A','B','N'],
            ['C','D','E','F','G','H','I','J','K','L'],
            ['M','N','O','P','Q','R','S','T','U','V']
        ];

        const words = [
            {word: 'NALOXONA', found: false},
            {word: 'FLUMAZENIL', found: false},
            {word: 'NAC', found: false},
            {word: 'OXIGENO', found: false}
        ];

        let selectedCells = [];
        let startCell = null;

        function createGrid() {
            const gridElement = document.getElementById('grid');
            for (let i = 0; i < puzzle.length; i++) {
                for (let j = 0; j < puzzle[i].length; j++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.textContent = puzzle[i][j];
                    cell.dataset.row = i;
                    cell.dataset.col = j;
                    cell.addEventListener('mousedown', startSelection);
                    cell.addEventListener('mouseover', updateSelection);
                    cell.addEventListener('mouseup', endSelection);
                    gridElement.appendChild(cell);
                }
            }
        }

        function createWordList() {
            const wordListElement = document.getElementById('wordList');
            words.forEach(wordObj => {
                const div = document.createElement('div');
                div.textContent = wordObj.word;
                div.id = `word-${wordObj.word}`;
                wordListElement.appendChild(div);
            });
        }

        function startSelection(e) {
            startCell = e.target;
            selectedCells = [startCell];
            updateCellStyles();
        }

        function updateSelection(e) {
            if (!startCell) return;
            
            const currentCell = e.target;
            if (isValidSelection(startCell, currentCell)) {
                selectedCells = getCellsBetween(startCell, currentCell);
                updateCellStyles();
            }
        }

        function endSelection() {
            if (selectedCells.length > 0) {
                checkWord();
            }
            startCell = null;
            selectedCells = [];
            updateCellStyles();
        }

        function isValidSelection(start, end) {
            const startRow = parseInt(start.dataset.row);
            const startCol = parseInt(start.dataset.col);
            const endRow = parseInt(end.dataset.row);
            const endCol = parseInt(end.dataset.col);
            
            return startRow === endRow || startCol === endCol || 
                   Math.abs(endRow - startRow) === Math.abs(endCol - startCol);
        }

        function getCellsBetween(start, end) {
            const cells = [];
            const startRow = parseInt(start.dataset.row);
            const startCol = parseInt(start.dataset.col);
            const endRow = parseInt(end.dataset.row);
            const endCol = parseInt(end.dataset.col);
            
            const rowDir = Math.sign(endRow - startRow) || 0;
            const colDir = Math.sign(endCol - startCol) || 0;
            
            let currentRow = startRow;
            let currentCol = startCol;
            
            while (true) {
                const cell = document.querySelector(
                    `.cell[data-row="${currentRow}"][data-col="${currentCol}"]`
                );
                cells.push(cell);
                
                if (currentRow === endRow && currentCol === endCol) break;
                
                currentRow += rowDir;
                currentCol += colDir;
            }
            
            return cells;
        }

        function updateCellStyles() {
            document.querySelectorAll('.cell').forEach(cell => {
                cell.classList.remove('selected');
            });
            
            selectedCells.forEach(cell => {
                cell.classList.add('selected');
            });
        }

        function checkWord() {
            const selectedWord = selectedCells
                .map(cell => cell.textContent)
                .join('');
            
            const wordObj = words.find(w => 
                w.word === selectedWord || w.word === selectedWord.split('').reverse().join('')
            );
            
            if (wordObj && !wordObj.found) {
                wordObj.found = true;
                selectedCells.forEach(cell => cell.classList.add('found'));
                document.getElementById(`word-${wordObj.word}`).classList.add('found-word');
                checkWin();
            }
        }

        function checkWin() {
            if (words.every(w => w.found)) {
                setTimeout(() => {
                    alert('¡Felicitaciones! Has encontrado todas las palabras.');
                }, 300);
            }
        }

        // Inicializar el juego
        createGrid();
        createWordList();
    </script>
</body>
</html>
