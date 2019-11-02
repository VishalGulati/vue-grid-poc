<template>
  <div class='home'>
    <!--<img alt='Vue logo' src='../assets/logo.png'>
                                                                                                                                                                                                                                                                                                <HelloWorld msg='Welcome to Your Vue.js App' />-->
    <grid-layout :layout.sync='layout' :col-num='2' :row-height='80' :is-draggable='true' :is-resizable='true' :is-mirrored='false' :vertical-compact='true' :margin='[10, 10]' :use-css-transforms='true'>

      <grid-item v-for='item in layout' :x='item.x' :y='item.y' :w='item.w' :h='item.h' :i='item.i' :key='item.i' @move="moveEvent" @moved="movedEvent">
        {{item.i}}<br/>
        <LineChart />
      </grid-item>
    </grid-layout>
  </div>
</template>

<script>
// @ is an alias to /src

import VueGridLayout from 'vue-grid-layout'
import LineChart from './Chart.vue'
var testLayout = [
  { 'x': 0, 'y': 0, 'w': 2, 'h': 5, 'i': '0' },
  { 'x': 0, 'y': 5, 'w': 2, 'h': 5, 'i': '1' },
  { 'x': 0, 'y': 10, 'w': 2, 'h': 5, 'i': '2' },
  { 'x': 0, 'y': 15, 'w': 2, 'h': 5, 'i': '3' },
  { 'x': 0, 'y': 20, 'w': 2, 'h': 5, 'i': '4' },
  { 'x': 0, 'y': 25, 'w': 2, 'h': 5, 'i': '5' }
]
export default {
  name: 'home',
  components: {
    GridLayout: VueGridLayout.GridLayout,
    GridItem: VueGridLayout.GridItem,
    LineChart
  },
  data: function () {
    return {
      layout: testLayout,
      lastMoved: '',
      prevOverlap: '',
      originalNeighbour: ''
    }
  },
  methods: {
    findOriginalNeighbour: function (movingGridIndex, x, y) {
      for (let ind in this.layout) {
        let grid = this.layout[ind]
        // console.log(grid)
        if (grid.y === y && ind !== movingGridIndex) {
          this.originalNeighbour = ind
        }
      }
    },
    moveEvent: function (i, newX, newY) {
      // console.log('MOVE i=' + i + ', original X=' + this.layout[i].x + ', original Y=' + this.layout[i].y)
      // console.log('MOVE i=' + i + ',  X=' + newX + ',  Y=' + newY)
      this.findOriginalNeighbour(i, this.layout[i].x, this.layout[i].y)
      if (!this.lastMoved || this.lastMoved !== i) {
        this.lastMoved = i
        this.prevOverlap = ''
        this.originalNeighbour = ''
      }
      this.checkOverlap(newX, newY, i)
    },

    movedEvent: function (i, newX, newY) {
      // console.log('MOVE i=' + i + ', X=' + newX + ', Y=' + newY)
      // this.checkOverlap(newX, newY, i)
      // this.prevOverlap = ''
      // console.log(this.layout)
      // console.log('last moved ', this.lastMoved, ' its last neighbour - ', this.prevOverlap)
      this.checkForExpansion(this.originalNeighbour)
      this.checkForExpansion(this.prevOverlap)
      this.checkForExpansion(this.lastMoved)
    },

    checkOverlap: function (x, y, i) {
      for (let ind in this.layout) {
        let grid = this.layout[ind]
        // console.log(grid)
        if (grid.y <= y && grid.y + grid.h >= y && grid.w === 2) {
          // this.checkForExpansion(this.prevOverlap)
          if (this.prevOverlap) {
            this.layout[this.prevOverlap].w = 2
            this.layout[this.prevOverlap].x = 0
          }
          this.layout[ind].w = 1
          this.layout[i].y = y
          this.layout[i].w = 1
          if (x >= 1) {
            this.layout[ind].x = 0
            this.layout[i].x = 1
          } else {
            this.layout[ind].x = 1
            this.layout[i].x = 0
          }
          this.prevOverlap = ind
        }
      }
    },
    checkForExpansion: function (gridIndex) {
      let isGridAlreadyThere = false
      if (gridIndex) {
        for (let ind in this.layout) {
          let grid = this.layout[ind]
          if (grid.y === this.layout[gridIndex].y && ind !== gridIndex) {
            // console.log(gridIndex, ' now living with ', ind)
            isGridAlreadyThere = true
          }
        }
        if (!isGridAlreadyThere) {
          this.layout[gridIndex].w = 2
          this.layout[gridIndex].x = 0
        }
      }
    }
  }
}
</script>
<style lang="scss">
.vue-grid-layout {
  background: #eee;
}

.layoutJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.eventsJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
  height: 100px;
  overflow-y: scroll;
}

.columns {
  -moz-columns: 120px;
  -webkit-columns: 120px;
  columns: 120px;
}

.vue-resizable-handle {
  z-index: 5000;
  position: absolute;
  width: 20px;
  height: 20px;
  bottom: 0;
  right: 0;
  background: url('data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=');
  background-position: bottom right;
  padding: 0 3px 3px 0;
  background-repeat: no-repeat;
  background-origin: content-box;
  box-sizing: border-box;
  cursor: se-resize;
}

.vue-grid-item:not(.vue-grid-placeholder) {
  background: #ccc;
  border: 1px solid black;
}

.vue-grid-item.resizing {
  opacity: 0.9;
}

.vue-grid-item.static {
  background: #cce;
}

.vue-grid-item .text {
  font-size: 24px;
  text-align: center;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
  height: 24px;
}

.vue-grid-item .minMax {
  font-size: 12px;
}

.vue-grid-item .add {
  cursor: pointer;
}
</style>
