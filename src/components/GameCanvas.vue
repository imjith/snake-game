<template>
  <div
    @keyup.right="move(1)"
    @keyup.up="move(2)"
    @keyup.down="move(-2)"
    @keyup.left="move(-1)"
    class="game-canvas"
    :style="{'max-width': (w*size)+'px'}"
  >
    <div
      v-for="n in size*size"
      :key="n"
      :class="['pixel', snake[n]==0?'white':'snake']"
      :style="{width:w+'px',height:w+'px'}"
    ></div>

    <pre>{{tail+" , "+head}}</pre>
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
      task: null
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
    gameOver() {
      alert("Game over");
      this.snake = Array(1601).fill(0);
      this.head = this.defHead;
      this.tail = this.defTail;
      this.isMoving = false;
      this.curMovement = 1;
      clearInterval(this.task);
      this.task = null;
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
      console.log(`event :${e}`);
      if (null != this.task) {
        clearInterval(this.task);
      }
      if (e != -this.curMovement) {
        console.log(`Moving : ${e}`);
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
          this.moveSnake(e);
        }
      }, 200);
    },
    moveSnake(e) {
      this.moveTail(e);
      this.moveHead(e);
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
  border: 1px solid rgb(207, 229, 229);
}
.game-canvas {
  display: flex;
  flex-wrap: wrap;
}

.white {
  background-color: rgb(207, 229, 229);
}

.snake {
  background-color: black;
}
</style>
