<template>
  <div class="card main-container d-inline-block">
    <div class="card-body">
      <grid
        :solution="solution"
        :solved="isGameSolved"
        :rows="rows"
        :columns="columns"
        @victory="isGameSolved = true"
      />

      <div class="d-grid">
        <button
          class="btn btn-sm"
          :class="{
            'btn-primary': !isGameSolved,
            'btn-success': isGameSolved,
          }"
          @click="generateGame"
        >
          New
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Grid from '@/components/Grid'

export default {
  components: {
    Grid,
  },

  data() {
    return {
      solution: [],
      isGameSolved: false,
      rows: 3,
      columns: 3,
    }
  },

  mounted() {
    this.generateGame()
  },

  methods: {
    generateGame() {
      let solution = []

      this.rows = Math.floor(Math.random() * 3) + 6
      this.columns = Math.floor(Math.random() * 3) + 6

      for (let i = 0; i < this.rows; i++) {
        solution.push([])
        for (let j = 0; j < this.columns; j++) {
          solution[i].push(Math.round(Math.random()))
        }
      }

      this.solution = solution
      this.isGameSolved = false
    },
  },
}
</script>

<style lang="scss" scoped>
.main-container {
  margin: 1rem;
  font-family: Inter;
}
</style>
