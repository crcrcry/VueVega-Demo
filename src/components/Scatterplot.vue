<template>
  <Base>
    <div :id="id"></div>
  </Base>
</template>

<script>
import _ from 'lodash'
import Base from './BaseComp'
import embed from 'vega-embed'

export default {
  data() {
    return {
      view: null,
    }
  },
  computed: {
    vegaSpec() {
      return {
        data: { values: this.data },
        selection: { 
          brush: { type: "interval" },
          multi: { type: "multi" }
        },
        mark: 'point',
        encoding: {
          x: { field: this.encoding.x, type: 'quantitative', scale: { domain: [2.5, 4.3] } },
          y: { field: this.encoding.y, type: 'quantitative', scale: { domain: [2.5, 7] } },
          color: {
            condition: {
              selection: "brush",
              value: "blue"
            },
            value: "grey"
          },
          size: {
            value: 50
          },
        },
        width: 400,
        height: 400
      }
    },
  },
  props: ['id', 'data', 'selection', 'encoding'],
  watch: {
    async vegaSpec(value) {
      await this.embedVegaSpec(this.value)
    },
    selection(value) {
      // 计算brush range
      let range
      if (value.length === 0) range = [[0, 0], [0, 0]]
      else {
        const xMin = _.minBy(value, this.encoding.x)[this.encoding.x]
        const xMax = _.maxBy(value, this.encoding.x)[this.encoding.x]
        const yMin = _.minBy(value, this.encoding.y)[this.encoding.y]
        const yMax = _.maxBy(value, this.encoding.y)[this.encoding.y]
        // 和通常range模型不一样
        range = [[xMin, xMax], [yMax, yMin]]
      }
      
      // 不会立即更新
      // TODO: bug, interval范围可能包含别的点，得用multi处理才行
      this.view.data('brush_store', [{
        fields: [
          { channel: 'x', field: this.encoding.x, type: 'R' },
          { channel: 'y', field: this.encoding.y, type: 'R' }
        ],
        unit: '',
        values: range
      }])
      // 触发更新
      this.view.runAsync()
    }
  },
  // 初始化
  async mounted() {
    await this.embedVegaSpec(this.vegaSpec)
  },
  methods: {
    async embedVegaSpec(spec) {
      const view = (await embed(`#${this.id}`, spec, {renderer: 'svg'})).view
      // 监听数据变化
      view.addDataListener('brush_store', (name, value) => {
        const brushRange = [
          [value[0].values[0][0], value[0].values[0][1]], 
          [value[0].values[1][1], value[0].values[1][0]]
        ]

        const selectedPts = this.data.filter(v => {
          const x = v[this.encoding.x]
          const y = v[this.encoding.y]
          const inRange = x >= brushRange[0][0] 
            && x <= brushRange[0][1] 
            && y >= brushRange[1][0] 
            && y <= brushRange[1][1]
          
          return inRange
        })

        this.$emit('brush', selectedPts)
      })

      this.view = view
    }
  },
  components: {
    Base
  }
}
</script>

<style>
.box {
  width: 500px;
  height: 500px;
}
</style>