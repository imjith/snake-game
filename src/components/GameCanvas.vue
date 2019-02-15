<template>
  <div
    tabindex="0"
    @keyup.right="move(1)"
    @keyup.up="move(2)"
    @keyup.down="move(-2)"
    @keyup.left="move(-1)"
    :style="{'max-width': (w*size)+'px'}"
  >
    <div class="score">
      <h2>Score : {{score}}</h2>
      <h5>Best : {{best}}</h5>
    </div>

    <div class="game-canvas" :style="{'max-width': (w*size)+'px'}">
      <div
        v-for="n in size*size"
        :key="n"
        :class="['pixel', getTheme(n)]"
        :style="{width:w+'px',height:w+'px'}"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "GameCanvas",
  props: {},

  data() {
    return {
      size: 40,
      w: 10,
      snake: Array(1601).fill(0),
      defHead: 44,
      defTail: 42,
      head: 44,
      tail: 42,
      curMovement: 1,
      isMoving: false,
      task: null,
      tCount: 0,
      treasurePos: 0,
      score: 0,
      best: 0
    };
  },
  mounted: function() {
    for (let i = this.defTail; i <= this.defHead; i++) {
      this.snake.splice(i, 1, 1);
    }
  },
  methods: {
    getHeadRow() {
      return Math.ceil(this.head / this.size);
    },
    getTailRow() {
      return Math.ceil(this.tail / this.size);
    },
    getHeadCol() {
      return this.head % this.size;
    },
    getTailCol() {
      return this.tail % this.size;
    },
    getTheme(n) {
      let v = this.snake[n];
      if (v == 0) {
        return "blank";
      }
      if (v == 1) {
        return "snake";
      }

      if (v == 2) {
        return "treasure";
      }
      return "";
    },
    gameOver() {
      alert("Game over");
      this.snake = Array(1601).fill(0);
      this.head = this.defHead;
      this.tail = this.defTail;
      this.isMoving = false;
      this.curMovement = 1;
      clearInterval(this.task);
      this.task = null;
      this.tCount = 0;
      this.best = this.score;
      this.score = 0;
      for (let i = this.defTail; i <= this.defHead; i++) {
        this.snake.splice(i, 1, 1);
      }
    },
    isGameOver(e) {
      let row = Math.ceil(this.head / this.size);
      let col = this.head % this.size;
      return (
        (e == 1 && col == 0) ||
        (e == -1 && col == 1) ||
        (e == 2 && row == 1) ||
        (e == -2 && row == this.size)
      );
    },
    move(e) {
      if (e != -this.curMovement) {
        if (null != this.task) {
          clearInterval(this.task);
        }
        this.isMoving = true;
        this.startRunning(e);
        this.curMovement = e;
      }
    },
    startRunning(e) {
      this.task = setInterval(() => {
        if (this.isGameOver(e)) {
          this.gameOver();
        } else {
          this.tCount += 1;
          if (this.tCount == 6) {
            this.generateTresure();
          }
          this.moveSnake(e);
        }
      }, 100);
    },
    moveSnake(e) {
      this.moveHead(e);
      if (this.head == this.treasurePos) {
        this.tCount = 0;
        this.treasurePos = 0;
        this.score += 1;
      } else {
        this.moveTail(e);
      }
    },
    moveHead(e) {
      if (e == 1) {
        this.head += 1;
      } else if (e == -1) {
        this.head -= 1;
      } else if (e == 2) {
        this.head -= this.size;
      } else {
        this.head += this.size;
      }
      this.snake.splice(this.head, 1, 1);
    },
    moveTail(e) {
      this.snake.splice(this.tail, 1, 0);
      if (!this.isUpperEmpty(this.tail)) {
        this.tail -= this.size;
      } else if (!this.isRightEmpty(this.tail)) {
        this.tail += 1;
      } else if (!this.isLeftEmpty(this.tail)) {
        this.tail -= 1;
      } else {
        this.tail += this.size;
      }
      this.snake.splice(this.tail, 1, 1);
    },
    generateTresure() {
      let t = null;
      while (t == null) {
        let rn = Math.floor(Math.random() * 1601) + 1;
        if (this.snake[rn] == 0) {
          t = rn;
        }
      }
      this.treasurePos = t;
      this.snake.splice(this.treasurePos, 1, 2);
    },
    isUpperEmpty(pos) {
      return this.snake[pos - this.size] == 0;
    },
    isBottomEmpty(pos) {
      return this.snake[pos + this.size] == 0;
    },
    isLeftEmpty(pos) {
      return this.snake[pos - 1] == 0;
    },
    isRightEmpty(pos) {
      return this.snake[pos + 1] == 0;
    }
  }
};
</script>


<style scoped>
.pixel {
  border: 1px solid rgb(241, 240, 240);
}
.game-canvas {
  display: flex;
  flex-wrap: wrap;
}

.blank {
  background-color: rgb(241, 240, 240);
}

.snake {
  background-color: black;
}

.treasure {
  background-color: rgb(171, 123, 1);
  box-shadow: 0 0 5px rgb(217, 208, 26);
  border-width: 2px;
  border-color: rgba(217, 208, 26, 1);
}
</style>
