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
        @click.right.prevent
        @mousedown="onMouseDown($event, rowIndex, columnIndex)"
        @mouseup="onMouseUp"
        @mouseover="onHover(rowIndex, columnIndex)"
      />
    </div>
  </div>
</template>

<script>
const actions = {
  INSERT: 1,
  MARK: 2,
}

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
      keyBeingPressed: -1,
      isInserting: false,
      isRemoving: false,
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
    affectCell(row, column, actionValue) {
      if (!this.solved) {
        let cellValue = -1

        if (this.isInserting && this.matrix[row][column] == 0)
          cellValue = actionValue
        else if (this.isRemoving && this.matrix[row][column] == actionValue)
          cellValue = 0
        else if (!this.isInserting && !this.isRemoving) {
          cellValue = this.matrix[row][column] == actionValue ? 0 : actionValue
          if (cellValue) this.isInserting = true
          else this.isRemoving = true
        }

        if (cellValue >= 0) this.$set(this.matrix[row], column, cellValue)
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

    onMouseDown(e, row, column) {
      this.keyBeingPressed = e.which
      if (this.keyBeingPressed === 1) {
        this.affectCell(row, column, actions.INSERT)
      } else if (this.keyBeingPressed === 3) {
        this.affectCell(row, column, actions.MARK)
      }
    },

    onMouseUp() {
      this.keyBeingPressed = -1
      this.isInserting = false
      this.isRemoving = false
    },

    onHover(row, column) {
      if (this.keyBeingPressed > -1) {
        if (this.keyBeingPressed === 1) {
          this.affectCell(row, column, actions.INSERT)
        } else if (this.keyBeingPressed === 3) {
          this.affectCell(row, column, actions.MARK)
        }
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.cell {
  width: 30px;
  text-align: center;
  letter-spacing: 1px;
  user-select: none;

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
