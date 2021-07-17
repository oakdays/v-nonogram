<template>
  <div class="mb-3 border">
    <div class="border-bottom d-flex">
      <div
        class="d-flex flex-column justify-content-center border-end cell"
        :style="{ width: maximumRowHintWidth }"
      />
      <div
        v-for="(hints, index) in columnHints"
        :key="index"
        class="d-flex flex-column justify-content-end cell"
        :class="{
          'border-end': index < columnHints.length - 1,
        }"
      >
        <template v-if="hints.length">
          <div v-for="(hint, hintIndex) in hints" :key="hintIndex">
            {{ hint }}
          </div>
        </template>
        <template v-else> 0 </template>
      </div>
    </div>

    <div
      v-for="(row, rowIndex) in matrix"
      :key="rowIndex"
      class="d-flex"
      :class="{
        'border-bottom': rowIndex < matrix.length - 1,
      }"
    >
      <div
        class="border-end cell pe-2 text-end"
        :style="{ width: maximumRowHintWidth }"
      >
        {{ getRowHint(rowIndex) }}
      </div>

      <div
        v-for="(column, columnIndex) in row"
        :key="columnIndex"
        :class="{
          selected: column == 1,
          marked: column == 2,
          'border-end': columnIndex < row.length - 1,
        }"
        class="cell cell--clickable"
        @click.exact="select(rowIndex, columnIndex)"
        @click.right.prevent="mark(rowIndex, columnIndex)"
      />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    solution: { type: Array, default: () => [] },
    solved: { type: Boolean, default: false },
    rows: { type: Number, default: 3 },
    columns: { type: Number, default: 3 },
  },

  data() {
    return {
      matrix: [
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0],
      ],
    }
  },

  computed: {
    maximumRowHintWidth() {
      let maxWidth = 0

      this.rowHints.forEach((hints) => {
        maxWidth = Math.max(maxWidth, hints.length)
      })

      return `${maxWidth * 20}px`
    },
    rowHints() {
      let hints = []

      for (let i in this.solution) {
        hints.push([])
        for (let j in this.solution[i]) {
          if (this.solution[i][j] == 1) {
            if (hints[i].length == 0) hints[i].push(1)
            else hints[i][hints[i].length - 1]++
          } else {
            if (hints[i].length > 0) hints[i].push(0)
          }
        }
      }

      return this.removeZeros(hints)
    },
    columnHints() {
      let hints = []

      for (let i in this.solution) {
        for (let j in this.solution[i]) {
          if (!hints[j]) hints[j] = [this.solution[i][j]]
          else {
            if (this.solution[i][j] == 1) hints[j][hints[j].length - 1]++
            else hints[j].push(this.solution[i][j])
          }
        }
      }

      return this.removeZeros(hints)
    },
  },

  watch: {
    matrix() {
      let isEqual = true

      for (let i in this.matrix) {
        for (let j in this.matrix[i]) {
          if (
            (this.matrix[i][j] === 1 &&
              this.matrix[i][j] !== this.solution[i][j]) ||
            (this.matrix[i][j] !== 1 && this.solution[i][j] === 1)
          ) {
            isEqual = false
            break
          }
        }
        if (!isEqual) break
      }

      if (isEqual) this.$emit('victory')
    },
    solution() {
      let matrix = []
      for (let i = 0; i < this.rows; i++) {
        matrix.push([])
        for (let j = 0; j < this.columns; j++) {
          matrix[i].push(0)
        }
      }
      this.matrix = matrix
    },
  },

  methods: {
    select(row, column) {
      if (!this.solved) {
        this.$set(
          this.matrix[row],
          column,
          this.matrix[row][column] == 1 ? 0 : 1
        )
      }
    },
    mark(row, column) {
      if (!this.solved) {
        this.$set(
          this.matrix[row],
          column,
          this.matrix[row][column] == 2 ? 0 : 2
        )
      }
    },
    removeZeros(hints) {
      hints.forEach((hintGroup, index) => {
        hints[index] = hintGroup.filter((hint) => hint > 0)
      })
      return hints
    },
    getRowHint(index) {
      return this.rowHints[index] && this.rowHints[index].length
        ? this.rowHints[index].join(' ')
        : 0
    },
  },
}
</script>

<style lang="scss" scoped>
.cell {
  width: 30px;
  text-align: center;
  letter-spacing: 1px;

  &--clickable {
    cursor: pointer;
    padding: 1px;

    &:hover {
      background-color: whitesmoke;
    }

    &:active {
      background-color: lightgrey;
    }

    &.selected {
      background-color: black;
    }

    &.marked {
      background-color: lightcoral;
    }
  }
}
</style>
