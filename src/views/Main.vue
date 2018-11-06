<template>
  <div
    class="card main-container"
    :style="{'width': `${columns * 4}rem`}">
    <div class="card-body">
      <grid
        :solution="solution"
        :solved="isGameSolved"
        :rows="rows"
        :columns="columns"
        @victory="isGameSolved = true" />
      <button
        class="btn btn-sm btn-block"
        :class="{'btn-primary': !isGameSolved, 'btn-success': isGameSolved}"
        @click="generateGame()">
        New
      </button>
    </div>
  </div>
</template>

<script>
import Grid from '@/components/Grid'

export default {
  components: {
    Grid
  },
  data () {
    return {
      solution: [],
      isGameSolved: false,
      rows: 3,
      columns: 3
    }
  },
  mounted () {
    this.generateGame()
  },
  methods: {
    generateGame () {
      let solution = []

      // TODO: implement correct dynamic sizing of grid cells first
      // this.rows = Math.floor(Math.random() * 3) + 3
      // this.columns = Math.floor(Math.random() * 3) + 3

      for (let i = 0; i < this.rows; i++) {
        solution.push([])
        for (let j = 0; j < this.columns; j++) {
          solution[i].push(Math.round(Math.random()))
        }
      }

      this.solution = solution
      this.isGameSolved = false
    }
  }
}
</script>

<style lang="scss" scoped>
  .main-container {
    margin: 1rem;
  }
</style>
