<template>
  <div
    class="container"
    @mousedown="mousedown($event)"
    @mousemove.prevent="mousemove($event)"
    @mouseup="mouseup($event)"
  >
    <div v-if="succesful">Succesful</div>
    <div v-if="!succesful && isGameOver">Failed</div>
    <div class="line" v-for="row in grid" :key="row.index">
      <div
        v-for="item in row"
        :key="item.index"
        class="row"
        :style="{
          background: color[item],
          color: item == 0 ? '#f4c8ff' : '#FFF',
          transition: 'all 0.5s ease',
        }"
      >
        {{ item }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "2048",
  data() {
    return {
      color: {
        0: "#f4c8ff",
        2: "#bd4c85",
        4: "#e8c649",
        8: "#e29d1e",
        16: "#e2781e",
        32: "#e24b1e",
        64: "#a52d0a",
        128: "#e180cc",
        256: "#762364",
        512: "#515ce3",
        1024: "#51e3e3",
        2048: "#27c43a",
      },
      number: 4,
      succesful: false,
      isGameOver: false,
      grid: [
        [2, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
      ],
      direction: "",
      startPoint: {
        x: 0,
        y: 0,
      },
      endPoint: {
        x: 0,
        y: 0,
      },
    };
  },
  methods: {
    mousedown(event) {
      if (this.isGameOver) {
        return;
      }
      this.direction = "";
      this.startPoint.x = event.x;
      this.startPoint.y = event.y;
    },
    mousemove(event) {},
    mouseup(event) {
      if (this.isGameOver) {
        return;
      }
      this.endPoint.x = event.x;
      this.endPoint.y = event.y;
      this.direction = this.calcDirection();
      if (this.direction) {
        this.updateRows();
      }
    },
    calcDirection() {
      const xMinus = this.endPoint.x - this.startPoint.x;
      const yMinus = this.endPoint.y - this.startPoint.y;
      if (Math.abs(xMinus) >= Math.abs(yMinus)) {
        if (Math.sign(xMinus) > 0) {
          return "right";
        } else if (Math.sign(xMinus) < 0) {
          return "left";
        }
        return "";
      } else {
        if (Math.sign(yMinus) > 0) {
          return "bottom";
        } else if (Math.sign(yMinus) < 0) {
          return "top";
        }
        return "";
      }
    },
    updateRows() {
      switch (this.direction) {
        case "left":
          if (this.canMoveLeft(this.grid)) {
            this.moveLeft(this.grid);
            this.newNumberByRandom();
            this.$forceUpdate();
          }
          break;
        case "right":
          if (this.canMoveRight(this.grid)) {
            this.moveRight(this.grid);
            this.newNumberByRandom();
            this.$forceUpdate();
          }
          break;
        case "top":
          if (this.canMoveTop(this.grid)) {
            this.moveTop(this.grid);
            this.newNumberByRandom();
            this.$forceUpdate();
          }
          break;
        case "bottom":
          if (this.canMoveBottom(this.grid)) {
            this.moveBottom(this.grid);
            this.newNumberByRandom();
            this.$forceUpdate();
          }
          break;
        default:
          break;
      }
    },

    moveRight(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 3; j >= 0; j--) {
          if (data[i][j] !== 0) {
            for (let k = 3; k > j; k--) {
              if (data[i][k] === 0 && this.noBlockHorizontal(i, j, k, data)) {
                data[i][k] = data[i][j];
                data[i][j] = 0;
                continue;
              } else if (
                data[i][k] === data[i][j] &&
                this.noBlockHorizontal(i, j, k, data)
              ) {
                data[i][k] += data[i][k];
                data[i][j] = 0;
                continue;
              }
            }
          }
        }
      }
    },

    moveLeft(data) {
      for (let i = 3; i >= 0; i--) {
        for (let j = 0; j <= 3; j++) {
          if (data[i][j] !== 0) {
            for (let k = 0; k < j; k++) {
              if (data[i][k] === 0 && this.noBlockHorizontal(i, j, k, data)) {
                data[i][k] = data[i][j];
                data[i][j] = 0;
                continue;
              } else if (
                data[i][k] === data[i][j] &&
                this.noBlockHorizontal(i, j, k, data)
              ) {
                data[i][k] += data[i][k];
                data[i][j] = 0;
                continue;
              }
            }
          }
        }
      }
    },
    moveBottom(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 3; j >= 0; j--) {
          if (data[j][i] !== 0) {
            for (let k = 3; k > j; k--) {
              if (data[k][i] === 0 && this.noBlockVertival(i, j, k, data)) {
                data[k][i] = data[j][i];
                data[j][i] = 0;
                continue;
              } else if (
                data[k][i] === data[j][i] &&
                this.noBlockVertival(i, j, k, data)
              ) {
                data[k][i] += data[k][i];
                data[j][i] = 0;
                continue;
              }
            }
          }
        }
      }
    },

    moveTop(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 0; j <= 3; j++) {
          if (data[j][i] !== 0) {
            for (let k = 0; k < j; k++) {
              if (data[k][i] === 0 && this.noBlockVertival(i, j, k, data)) {
                data[k][i] = data[j][i];
                data[j][i] = 0;
                continue;
              } else if (
                data[k][i] === data[j][i] &&
                this.noBlockVertival(i, j, k, data)
              ) {
                data[k][i] += data[k][i];
                data[j][i] = 0;
                continue;
              }
            }
          }
        }
      }
    },

    noBlockVertival(row, col1, col2, data) {
      for (let i = col1 + 1; i < col2; i++) {
        if (data[i][row] !== 0) {
          return false;
        }
      }
      return true;
    },

    noBlockHorizontal(row, col1, col2, data) {
      for (let i = col1 + 1; i < col2; i++) {
        if (data[row][i] !== 0) {
          return false;
        }
      }
      return true;
    },

    canMoveRight(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 0; j <= 2; j++) {
          if (data[i][j] !== 0) {
            if (data[i][j + 1] === 0 || data[i][j + 1] === data[i][j]) {
              return true;
            }
          }
        }
      }
      return false;
    },

    canMoveLeft(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 1; j <= 3; j++) {
          if (data[i][j] !== 0) {
            if (data[i][j - 1] === 0 || data[i][j - 1] === data[i][j]) {
              return true;
            }
          }
        }
      }
      return false;
    },

    canMoveBottom(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 0; j <= 2; j++) {
          if (data[j][i] !== 0) {
            if (data[j + 1][i] === 0 || data[j + 1][j] === data[j][i]) {
              return true;
            }
          }
        }
      }
      return false;
    },

    canMoveTop(data) {
      for (let i = 0; i <= 3; i++) {
        for (let j = 1; j <= 3; j++) {
          if (data[j][i] !== 0) {
            if (data[j - 1][i] === 0 || data[j - 1][j] === data[j][i]) {
              return true;
            }
          }
        }
      }
      return false;
    },
    newNumberByRandom() {
      const arr = [];
      for (let i = 0; i <= 3; i++) {
        for (let j = 0; j <= 3; j++) {
          if (this.grid[i][j] === 2048) {
            this.isGameOver = true;
            this.succesful = true;
            return;
          }
          if (this.grid[i][j] === 0) {
            arr.push({ i, j });
          }
        }
      }
      if (arr.length === 0) {
        this.isGameOver = true;
      }
      const randomIndex = Math.floor(Math.random() * arr.length);
      const position = arr[randomIndex];
      this.grid[position.i][position.j] = 2;
    },
  },
};
</script>

<style scoped>
.container {
  width: 400px;
  height: 400px;
  display: flex;
  flex-direction: column;
}
.line {
  flex: 1;
  display: flex;
  flex-direction: row;
}
.row {
  width: 100px;
  height: 100px;
  margin: 2px 1px 2px 1px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 32px;
  /* animation: dynamicDiv 10s linear infinite; */
}

/* @keyframes dynamicDiv {
  form { background: #303fb2; }
  to { background: #2f70d4; }
} */
</style>
