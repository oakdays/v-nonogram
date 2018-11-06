<template>
  <table
    class="table table-bordered table-sm text-center noselect"
    style="table-layout: fixed;">
    <thead>
      <tr>
        <th />
        <th
          v-for="(hint, index) in columnHints"
          :key="index">
          {{ hint || 0 }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(row, rowIndex) in matrix"
        :key="rowIndex">
        <th>{{ rowHints[rowIndex] || 0 }}</th>
        <td
          v-for="(column, columnIndex) in row"
          :key="columnIndex"
          :class="{'selected': column == 1, 'marked': column == 2}"
          @click="select(rowIndex, columnIndex)"
          @click.right.prevent="mark(rowIndex, columnIndex)" />
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  props: {
    solution: { type: Array, default: () => [] },
    solved: { type: Boolean, default: false },
    rows: { type: Number, default: 3 },
    columns: { type: Number, default: 3 }
  },
  data () {
    return {
      matrix: [
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0]
      ]
    }
  },
  computed: {
    rowHints () {
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
    columnHints () {
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
    }
  },
  watch: {
    matrix () {
      let isEqual = true

      for (let i in this.matrix) {
        for (let j in this.matrix[i]) {
          if ((this.matrix[i][j] === 1 && this.matrix[i][j] !== this.solution[i][j]) || (this.matrix[i][j] !== 1 && this.solution[i][j] === 1)) {
            isEqual = false
            break
          }
        }
        if (!isEqual) break
      }

      if (isEqual) this.$emit('victory')
    },
    solution () {
      let matrix = []
      for (let i = 0; i < this.rows; i++) {
        matrix.push([])
        for (let j = 0; j < this.columns; j++) {
          matrix[i].push(0)
        }
      }
      this.matrix = matrix
    }
  },
  methods: {
    select (row, column) {
      if (!this.solved) {
        this.$set(
          this.matrix[row],
          column,
          this.matrix[row][column] == 1 ? 0 : 1
        )
      }
    },
    mark (row, column) {
      if (!this.solved) {
        this.$set(
          this.matrix[row],
          column,
          this.matrix[row][column] == 2 ? 0 : 2
        )
      }
    },
    removeZeros (hints) {
      // remove zeros and turn into string
      for (let i in hints) {
        hints[i] = hints[i].filter(hint => hint > 0).join(' ')
      }

      return hints
    }
  }
}
</script>

<style lang="scss" scoped>
/* td,
th {
  width: 25%;
} */
td {
  cursor: pointer;
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
</style>

