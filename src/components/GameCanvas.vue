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
      size: 30,
      w: 18,
      snake: Array(901).fill(0),
      defHead: 44,
      defTail: 42,
      head: 44,
      tail: 42,
      curMovement: 1,
      isMoving: false,
      task: null,
      tCount: 0,
      foodPos: 0,
      score: 0,
      best: 0,
      snake_body: null
    };
  },
  mounted: function() {
    this.initSnake();
  },
  methods: {
    initSnake() {
      this.snake_body = [];
      for (let i = this.defHead; i >= this.defTail; i--) {
        this.snake.splice(i, 1, 1);
        this.snake_body.push(i);
      }
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
        return "food";
      }
      return "";
    },
    gameOver() {
      alert("Game over");
      this.snake = Array(901).fill(0);
      this.head = this.defHead;
      this.tail = this.defTail;
      this.isMoving = false;
      this.curMovement = 1;
      clearInterval(this.task);
      this.task = null;
      this.tCount = 0;
      if (this.score > this.best) {
        this.best = this.score;
      }
      this.score = 0;
      this.initSnake();
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
            this.createFood();
          }
          this.moveSnake(e);
        }
      }, 100);
    },
    moveSnake(e) {
      this.moveHead(e);
      if (this.head == this.foodPos) {
        this.tCount = 0;
        this.foodPos = 0;
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
      if (this.snake[this.head] === 1) {
        this.gameOver();
        return true;
      }

      this.snake_body.splice(0, 0, this.head);
      this.snake.splice(this.head, 1, 1);
      return false;
    },
    moveTail(e) {
      this.snake_body.splice(-1, 1);
      this.snake.splice(this.tail, 1, 0);
      this.tail = this.snake_body[this.snake_body.length - 1];
      this.snake.splice(this.tail, 1, 1);
    },
    createFood() {
      let t = null;
      while (t == null) {
        let rn = Math.floor(Math.random() * 1601) + 1;
        if (this.snake[rn] == 0) {
          t = rn;
        }
      }
      this.foodPos = t;
      this.snake.splice(this.foodPos, 1, 2);
    }
  }
};
</script>


<style scoped>
.pixel {
  border: 0px solid rgb(241, 240, 240);
}
.game-canvas {
  display: flex;
  flex-wrap: wrap;
}

.blank {
  background-color: rgb(241, 240, 240);
}

.snake {
  background-color: rgb(49, 168, 19);
  border: 0px solid;
}

.food {
  background-image: url("../assets/image/apple.png");
  background-color: rgb(241, 240, 240);
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center center fixed;
}
</style>
