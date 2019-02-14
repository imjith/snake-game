<template>
  <div
    @keyup.right="moveRight"
    @keyup.up="moveUp"
    @keyup.down="moveDown"
    @keyup.left="moveLeft"
    class="game-canvas"
    :style="{'max-width': (w*size+(size*2))+'px'}"
  >
    <div
      v-for="n in size*size"
      :key="n"
      :class="['pixel', snake[n]]"
      :style="{width:w+'px',height:w+'px'}"
    ></div>
  </div>
</template>

<script>
export default {
  name: "GameCanvas",
  props: {
    msg: String
  },

  data() {
    return {
      size: 5,
      w: 20,
      snake: Array(26).fill("white"),
      head: 13,
      tail: 12,
      movingLef: false,
      movingRight: false,
      movingUp: false,
      movingDown: false
    };
  },
  mounted: function() {
    for (let i = this.tail; i <= this.head; i++) {
      this.snake.splice(i, 1, "snake");
    }
  },
  methods: {
    moveRight() {
      let tailCol = this.tail % 5;
      let headCol = this.head % 5;
      if (headCol != 0) {
        let headRow = Math.ceil(this.head / 5);
        let tailRow = Math.ceil(this.tail / 5);
        if (this.head > this.tail || tailRow != headRow) {
          this.snake.splice(this.tail, 1, "white");
          this.head += 1;
          if (tailCol == headCol) {
            if (this.head > this.tail) {
              this.tail += 5;
            } else {
              this.tail -= 5;
            }
          } else {
            this.tail += 1;
          }

          this.snake.splice(this.head, 1, "snake");
          this.snake.splice(this.tail, 1, "snake");
        }
      } else {
        alert("Game over");
      }
    },
    moveLeft() {
      let headRow = Math.ceil(this.head / 5);
      let headCol = this.head % 5;
      if (headCol != 1) {
        let tailRow = Math.ceil(this.tail / 5);
        if (this.head < this.tail || tailRow != headRow) {
          let tailCol = this.tail % 5;

          this.snake.splice(this.tail, 1, "white");
          this.head -= 1;
          if (tailCol == headCol) {
            if (this.head > this.tail) {
              this.tail += 5;
            } else {
              this.tail -= 5;
            }
          } else {
            this.tail -= 1;
          }

          this.snake.splice(this.head, 1, "snake");
          this.snake.splice(this.tail, 1, "snake");
        }
      } else {
        alert("Game over");
      }
    },
    moveUp() {
      let headRow = Math.ceil(this.head / 5);
      if (headRow > 1) {
        /*
        invalid conditions
        1. when head is large and both in same col
        2. 
        */
        let tailCol = this.tail % 5;
        let headCol = this.head % 5;
        if (this.head < this.tail || tailCol != headCol) {
          this.snake.splice(this.tail, 1, "white");

          if (tailCol == headCol) {
            this.tail -= 5;
          } else {
            if (this.head > this.tail) {
              this.tail += 1;
            } else {
              this.tail -= 1;
            }
          }
          this.head -= 5;

          this.snake.splice(this.tail, 1, "snake");
          this.snake.splice(this.head, 1, "snake");
        }
      } else {
        alert("game over");
      }
    },
    moveDown() {
      let headRow = Math.ceil(this.head / 5);
      if (headRow < 5) {
        /*
        invalid conditions
        1. when head is small and both in same col
        2. 
        */
        let tailCol = this.tail % 5;
        let headCol = this.head % 5;
        if (this.head > this.tail || tailCol != headCol) {
          this.snake.splice(this.tail, 1, "white");
          if (tailCol == headCol) {
            this.tail += 5;
          } else {
            if (this.head > this.tail) {
              this.tail += 1;
            } else {
              this.tail -= 1;
            }
          }
          this.head += 5;

          this.snake.splice(this.tail, 1, "snake");
          this.snake.splice(this.head, 1, "snake");
        }
      } else {
        alert("game over");
      }
    }
  }
};
</script>


<style scoped>
.pixel {
  border: 1px solid lightgrey;
}
.game-canvas {
  display: flex;
  flex-wrap: wrap;
}

.white {
  background-color: white;
}

.snake {
  background-color: black;
}
</style>
