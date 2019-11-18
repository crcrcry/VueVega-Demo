<template>
  <div id="app">
    <div>
      <Scatterplot 
        id="comp1" 
        :data="points" 
        :selection="[]" 
        :encoding="{ x: 'attrA', y: 'attrB' }"
        @brush="onBrush" 
      />
    </div>
    <div>
      <Scatterplot 
        id="comp2" 
        :data="points" 
        :selection="selection" 
        :encoding="{ x: 'attrA', y: 'attrC' }"
      />
    </div>
    </div>
  </div>
</template>

<script>
import Scatterplot from './components/Scatterplot'
import _ from 'lodash'

export default {
  data () {
    return {
      points: [
        { "id": 1, "attrA": 3.3, "attrB": 3.7, "attrC": 4.2 }, 
        { "id": 2, "attrA": 3.5, "attrB": 3.7, "attrC": 4.9 }, 
        { "id": 3, "attrA": 3.9, "attrB": 3.8, "attrC": 6.2 }, 
        { "id": 4, "attrA": 3.2, "attrB": 3.0, "attrC": 5.3 }, 
        { "id": 5, "attrA": 3.6, "attrB": 3.0, "attrC": 4.4 }, 
        { "id": 6, "attrA": 3.7, "attrB": 3.6, "attrC": 4.7 }, 
        { "id": 7, "attrA": 3.2, "attrB": 3.4, "attrC": 5.2 }, 
        { "id": 8, "attrA": 3.5, "attrB": 3.9, "attrC": 6.0 }, 
        { "id": 9, "attrA": 3.1, "attrB": 3.2, "attrC": 4.0 }, 
        { "id": 10, "attrA": 3.0, "attrB": 3.3, "attrC": 4.9 }
      ],
      selection: [],
    }
  },
  methods: {
    onBrush(selectedPts) {
      // 有新数据时，更新另一张图
      const newPts = _.differenceBy(selectedPts, this.selection, 'id')
      if (newPts.length > 0) {
        this.selection = selectedPts
      }
    }
  },
  components: {
    Scatterplot
  }
}
</script>

<style>
#app {

}
</style>
