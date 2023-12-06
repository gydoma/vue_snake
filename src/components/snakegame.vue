<template>
    <div id="app">
        <div class="game-container">
            <div class="game-board">
                <div
                    v-for="(row, rowIndex) in board"
                    :key="rowIndex"
                    class="game-row"
                >
                    <div
                        v-for="(cell, colIndex) in row"
                        :key="colIndex"
                        :class="{
                            'game-cell': true,
                            'snake-cell': isSnakeCell(rowIndex, colIndex),
                            'food-cell': isFoodCell(rowIndex, colIndex),
                            'snake-head': isSnakeHeadCell(rowIndex, colIndex),
                        }"
                        :style="isSnakeHeadCell(rowIndex, colIndex) ? { transform: `rotate(${rotation}deg)` } : {}"
                    ></div>
                </div>
            </div>
            <div class="game-info">
                <div class="score">Score: {{ score }} - Session Record: {{ record }} - T/m {{ 500 - score * 7.5 }}ms</div>
                <button @click="startGame" v-if="!gameOver && snake.length === 0">Start Game</button>
                <div v-else-if="gameOver">
                    <div class="game-over">Game Over!</div>
                    <button @click="restartGame">Restart</button>
                    <h3 id="test"></h3>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            boardSize: 10,
            snake: [],
            food: {},
            head: {},
            direction: "right",
            gameOver: false,
            score: 0,
            record: 0,
        };
    },
    computed: {
        board() {
            const board = [];
            for (let row = 0; row < this.boardSize; row++) {
                const rowData = [];
                for (let col = 0; col < this.boardSize; col++) {
                    rowData.push({});
                }
                board.push(rowData);
            }
            return board;
        },
        rotation() {
        switch (this.direction) {
            case 'up':
                return 270;
            case 'down':
                return 90;
            case 'left':
                return 180;
            case 'right':
                return 0;
            default:
                return 0;
        }
        },
    },
    methods: {
        startGame() {
            this.snake = [
                { row: Math.floor(this.boardSize / 2), col: Math.floor(this.boardSize / 2) },
            ];
            this.generateFood();
            this.direction = "right";
            this.gameOver = false;
            this.score = 1;
            this.moveSnake();
        },
        moveSnake() {
            if (this.gameOver) return;

            const head = { ...this.snake[0] };
            switch (this.direction) {
                case "up":
                    head.col--;
                    break;
                case "down":
                    head.col++;
                    break;
                case "left":
                    head.row--;
                    break;
                case "right":
                    head.row++;
                    break;
            }

            if (this.isCollision(head)) {
                this.gameOver = true;
                return;
            }

            this.snake.unshift(head);

            if (this.isEatingFood(head)) {
                this.score++;
                this.generateFood();
            } else {
                this.snake.pop();
            }
            
            if (this.score > this.record){
                this.record = this.score
            }

            setTimeout(() => {
                this.moveSnake();
            }, 500 - (this.score * 7.5));
        },
        changeDirection(event) {
            const keyPressed = event.keyCode;
            switch (keyPressed) {
                case 37:
                    if (this.direction !== "right") {
                        this.direction = "left";
                    }
                    break;
                case 38:
                    if (this.direction !== "down") {
                        this.direction = "up";
                    }
                    break;
                case 39:
                    if (this.direction !== "left") {
                        this.direction = "right";
                    }
                    break;
                case 40:
                    if (this.direction !== "up") {
                        this.direction = "down";
                    }
                    break;
            }
        },
        isCollision(head) {
            return (
                head.row < 0 ||
                head.row >= this.boardSize ||
                head.col < 0 ||
                head.col >= this.boardSize ||
                this.snake.some((segment) => segment.row === head.row && segment.col === head.col)
            );
        },
        isEatingFood(head) {
            return head.row === this.food.row && head.col === this.food.col;
        },
        generateFood() {
            let food;
            do {
                food = {
                    row: Math.floor(Math.random() * this.boardSize),
                    col: Math.floor(Math.random() * this.boardSize),
                };
            } while (this.snake.some((segment) => segment.row === food.row && segment.col === food.col));

            this.food = food; 
        },
        isSnakeCell(row, col) {
            return this.snake.some((segment) => segment.row === row && segment.col === col);
        },
        // kurva anyád te geciláda hogy basznád meg te semmirekellő silány szar anyád büszkesége te hitler faszára való senkiházi undorító fostaliga, egyetlen egy szaros értelmes mondatot nem tudsz összehozni, egy senki vagy te mocskos utolsó faszkalap 
        //isSnakeHeadCell(row, col) {
        //    return this.snake[0].row === row && this.snake[0].col === col;
        //},
        isSnakeHeadCell(row, col) {
            if (this.snake && this.snake.length > 0) {
                return this.snake[0].row === row && this.snake[0].col === col;
            }
                return false;
            },
        isFoodCell(row, col) {
            return this.food.row === row && this.food.col === col;
        },
        restartGame() {
            this.startGame();
        },
    },
    mounted() {
        window.addEventListener("keydown", this.changeDirection);
    },
};
</script>

<style>

html, body {margin: 0; height: 100%; overflow: hidden; color:#dfafff}
#div.app {background-color: red;}
* {
  image-rendering: pixelated;
  image-rendering: -moz-crisp-edges;
  image-rendering: crisp-edges;
}
body {background-color: #121112;}
.game-container {
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.game-board {
    display: flex;
    justify-content: center;
    background-color: #242224;
    border-radius: 60px;
    padding: 5%;
}

.game-row {
    display: flex;
    height:500px;
    width: 50px;
    flex-direction: column;;
}

.game-cell {
    flex: 1;
    background-color: #fff;
    margin: -1px;
    background-image: url(../assets/snakebg.png);
    background-repeat: no-repeat;
    background-position: center;
    background-size: 100%;
}   

.snake-cell {
    background-image: url(../assets/snake.gif);
    background-repeat: no-repeat;
    background-position: center;
    background-size: 100%;
}
.snake-head {
    background-image: url(../assets/snakehead.gif);
    background-repeat: no-repeat;
    background-position: center;
    background-size: 100%;
}

.food-cell {
    background-image: url(../assets/cake.gif);
    background-repeat: no-repeat;
    background-position: center;
    background-size: 100%;
}

.game-info {
    text-align: center;
}

.score {
    font-size: 36px;
    margin-bottom: 10px;
}

.game-over {
    font-size: 48px;
    margin-bottom: 10px;
}

button {
    padding: 10px 20px;
    font-size: 24px;
    background-color: #74359f;
    color: #dfafff;
    border: 0;
    border-radius: 25px;
}
</style>