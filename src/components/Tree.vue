<template>
  <div class="tree">
    <h1 class="title">Vue + D3 : 줌과 패닝이 가능한 Tree 그래프 그리기</h1>
    <svg class="container" :width="viewer.w" :height="viewer.h" ref="svg">
      <g :transform="`translate(${zoom.x},${zoom.y})scale(${zoom.k})`">
        <path
          class="link"
          v-for="line in lines"
          :d="getDiagonal(line)"
        ></path>
        <g
          class="node"
          v-for="node in nodes"
          :class="node.children ? 'node--internal' : 'node--leaf'"
          :transform="`translate(${node.y},${node.x})`"
        >
          <circle r="2.5"></circle>
          <text
            dy="3"
            :x="node.children ? -8 : 8"
            :style="{ textAnchor: node.children ? 'end' : 'start' }"
          >{{ node.data.name }}</text>
        </g>
      </g>
    </svg>
  </div>
</template>

<script>
  import * as d3 from 'd3'
  import data from '@/data'

  export default {
    name: 'tree',
    data () {
      return {
        viewer: {
          w: 600,
          h: 600
        },
        nodes: [],
        lines: [],
        zoom: {
          x: 40,
          y: 0,
          k: 1
        }
      }
    },
    created () {
      this.setData()
    },
    mounted () {
      this.setZoom()
    },
    methods: {
      setData () {
        const stratify = d3.stratify().id((d) => d.id).parentId((d) => d.parentId)
        const stratified = stratify(data)
        const tree = d3.tree().size([this.viewer.w, this.viewer.h])

        tree(stratified)
        this.nodes = stratified.descendants()
        this.lines = stratified.descendants().slice(1)
      },
      getDiagonal (d) {
        return `M${d.y},${d.x}C${d.parent.y + 100},${d.x} ${d.parent.y + 100},${d.parent.x} ${d.parent.y},${d.parent.x}`
      },
      setZoom () {
        const zoom = d3.zoom().scaleExtent([1, 10]).on('zoom', this.onZoom)
        const selection = d3.select(this.$refs.svg)

        selection.call(zoom)
      },
      onZoom () {
        this.zoom.x = d3.event.transform.x
        this.zoom.y = d3.event.transform.y
        this.zoom.k = d3.event.transform.k
      }
    }
  }
</script>

<style>
  html {
    background-color: #f3f3f3;
  }
  .tree {
    padding: 20px;
    font-size: 16px;
    text-align: center;
  }
  .title {
    margin: 20px 0;
    font-size: 24px;
  }
  .container {
    margin: 20px auto;
    border: 1px solid #ccc;
    background-color: #fff;
  }

  .node circle {
    fill: #999;
  }
  .node text {
    font: 10px sans-serif;
  }
  .node--internal circle {
    fill: #555;
  }
  .node--internal text {
    text-shadow: 0 1px 0 #fff, 0 -1px 0 #fff, 1px 0 0 #fff, -1px 0 0 #fff;
  }
  .link {
    fill: none;
    stroke: #555;
    stroke-opacity: 0.4;
    stroke-width: 1.5px;
  }
  form {
    font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    position: absolute;
    left: 10px;
    top: 10px;
  }
  label {
    display: block;
  }
</style>
