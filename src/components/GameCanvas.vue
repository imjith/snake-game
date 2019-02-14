<template>
  <div
    @keyup.right="moveRight"
    @keyup.up="moveUp"
    @keyup.down="moveDown"
    @keyup.left="moveLeft"
    class="game-canvas"
    :style="{'max-width': (w*size)+'px'}"
  >
    <div
      v-for="n in size*size"
      :key="n"
      :class="['pixel', snake[n]]"
      :style="{width:w+'px',height:w+'px'}"
    ></div>

    <pre>{{tail+" , "+head}}</pre>
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
      size: 10,
      w: 20,
      snake: Array(101).fill("white"),
      defHead: 44,
      defTail: 42,
      head: 44,
      tail: 42,
      movingLef: false,
      movingRight: false,
      movingUp: false,
      movingDown: false
    };
  },
  mounted: function() {
    for (let i = this.defTail; i <= this.defHead; i++) {
      this.snake.splice(i, 1, "snake");
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
      this.snake = Array(101).fill("white");
      this.head = this.defHead;
      this.tail = this.defTail;
      for (let i = this.defTail; i <= this.defHead; i++) {
        this.snake.splice(i, 1, "snake");
      }
    },
    moveRight() {
      let tailCol = this.getTailCol();
      let headCol = this.getHeadCol();
      if (headCol != 0) {
        let headRow = this.getHeadRow();
        let tailRow = this.getTailRow();
        if (this.head > this.tail || tailRow != headRow) {
          this.snake.splice(this.tail, 1, "white");

          if (tailCol == headCol) {
            if (this.head > this.tail) {
              this.tail += this.size;
            } else {
              this.tail -= this.size;
            }
          } else {
            if (headRow > tailRow) {
              if (this.snake[this.tail + this.size] != "white") {
                this.tail += this.size;
              } else {
                this.tail += 1;
              }
            } else {
              if (headCol > tailCol) {
                this.tail += 1;
              } else {
                this.tail -= 1;
              }
            }
          }
          this.head += 1;
          this.snake.splice(this.head, 1, "snake");
          //this.snake.splice(this.tail, 1, "snake");
        }
      } else {
        this.gameOver();
      }
    },
    moveLeft() {
      let headRow = this.getHeadRow();
      let headCol = this.getHeadCol();
      if (headCol != 1) {
        let tailRow = this.getTailRow();
        if (this.head < this.tail || tailRow != headRow) {
          let tailCol = this.getTailCol();

          this.snake.splice(this.tail, 1, "white");
          this.head -= 1;
          if (tailCol == headCol) {
            if (this.head > this.tail) {
              this.tail += this.size;
            } else {
              this.tail -= this.size;
            }
          } else {
            if (tailRow == headRow) {
              this.tail -= 1;
            } else if (headRow < tailRow && headCol > tailCol) {
              this.tail += 1;
            } else if (headRow < tailRow && headCol < tailCol) {
              this.tail -= 1;
            } else {
              this.tail += this.size;
            }
          }

          this.snake.splice(this.head, 1, "snake");
          // this.snake.splice(this.tail, 1, "snake");
        }
      } else {
        this.gameOver();
      }
    },
    moveUp() {
      let headRow = this.getHeadRow();
      let tailRow = this.getTailRow();
      if (headRow > 1) {
        /*
        invalid conditions
        1. when head is large and both in same col
        2. 
        */
        let tailCol = this.getTailCol();
        let headCol = this.getHeadCol();
        if (this.head < this.tail || tailCol != headCol) {
          this.snake.splice(this.tail, 1, "white");

          if (tailCol == headCol) {
            this.tail -= this.size;
          } else {
            if (headRow == tailRow) {
              if (headCol > tailCol) {
                this.tail += 1;
              } else {
                this.tail -= 1;
              }
            } else {
              if (headRow > tailRow && headCol > tailCol) {
                this.tail += this.size;
              } else if (headRow < tailRow && headCol > tailCol) {
                if (this.snake[this.head + this.size] != "white") {
                  this.tail += 1;
                } else {
                  this.tail -= this.size;
                }
              } else if (headRow < tailRow && headCol < tailCol) {
                if (this.snake[this.head + this.size] != "white") {
                  this.tail -= 1;
                } else {
                  this.tail -= this.size;
                }
              } else {
                this.tail += 1;
              }
            }
          }
          this.head -= this.size;

          //this.snake.splice(this.tail, 1, "snake");
          this.snake.splice(this.head, 1, "snake");
        }
      } else {
        this.gameOver();
      }
    },
    moveDown() {
      let headRow = this.getHeadRow();
      let tailRow = this.getTailRow();
      if (headRow < this.size) {
        /*
        invalid conditions
        1. when head is small and both in same col
        2. 
        */
        let tailCol = this.getTailCol();
        let headCol = this.getHeadCol();
        if (
          this.head > this.tail ||
          (tailCol != headCol && this.snake[this.head + this.size] == "white")
        ) {
          this.snake.splice(this.tail, 1, "white");
          if (tailCol == headCol) {
            this.tail += this.size;
          } else {
            if (headRow == tailRow) {
              this.tail += 1;
            } else if (headCol > tailCol) {
              if (headRow > tailRow) {
                this.tail += this.size;
              } else {
                this.tail -= this.size;
              }
            } else {
              if (headRow > tailRow) {
                this.tail -= 1;
              } else {
                this.tail -= this.size;
              }
            }
          }

          this.head += this.size;
          //this.snake.splice(this.tail, 1, "snake");
          this.snake.splice(this.head, 1, "snake");
        }
      } else {
        this.gameOver();
      }
    }
  }
};
</script>


<style scoped>
.pixel {
  border: 1px solid lightblue;
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
