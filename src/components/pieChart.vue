<template>
  <svg height="300" ref="svg" width="600"></svg>
</template>
<script>
import * as d3 from 'd3'
export default {
  name: 'pieChart',
  props: {
    info: Array
  },
  mounted() {
    const width = 600,
      height = 300
    const main = d3
      .select(this.$el)
      .append('g')
      .classed('main', true)
      .attr('transform', 'translate(' + width / 2 + ',' + height / 2 + ')')
    const pie = d3
      .pie()
      .sort(null)
      .value(d => d.value)
    const pieData = pie(this.info)
    const radius = 100
    const outerArc = d3
      .arc()
      .innerRadius(1.2 * radius)
      .outerRadius(1.2 * radius)
    const oArc = d3
      .arc()
      .innerRadius(1.1 * radius)
      .outerRadius(1.1 * radius)
    const arc = d3
      .arc()
      .innerRadius(0)
      .outerRadius(radius)
    main
      .selectAll('g')
      .data(pieData)
      .enter()
      .append('path')
      .attr('fill', (d, i) => {
        return this.info[i].color
      })
      .attr('d', d => {
        return arc(d)
      })
    const slices = main.append('g').attr('class', 'slices')
    const lines = main.append('g').attr('class', 'lines')
    const labels = main.append('g').attr('class', 'labels')
    var arcs = slices
      .selectAll('g')
      .data(pieData)
      .enter()
      .append('path')
      .attr('fill', (d, i) => {
        return this.info[i].color
      })
      .attr('d', d => {
        return arc(d)
      })
    const texts = labels
      .selectAll('text')
      .data(pieData)
      .enter()
      .append('text')
      .attr('dy', '0.35em')
      .attr('fill', (d, i) => {
        return this.info[i].color
      })
      .text((d, i) => {
        return d.data.name
      })
      .style('text-anchor', (d, i) => {
        return this.midAngel(d) < Math.PI ? 'start' : 'end'
      })
      .attr('transform', (d, i) => {
        const pos = outerArc.centroid(d)
        pos[0] = radius * (this.midAngel(d) < Math.PI ? 1.5 : -1.5)
        return `translate(${pos})`
      })
      .style('opacity', 1)
    const polylines = lines
      .selectAll('polyline')
      .data(pieData)
      .enter()
      .append('polyline')
      .attr('stroke', (d, i) => {
        return this.info[i].color
      })
      .attr('fill', 'none')
      .attr('stroke-width', '2px')
      .attr('stroke-dasharray', '5px')
      .attr('points', d => {
        return [arc.centroid(d), arc.centroid(d), arc.centroid(d)]
      })
      .attr('points', d => {
        const pos = outerArc.centroid(d)
        pos[0] = radius * (this.midAngel(d) < Math.PI ? 1.5 : -1.5)
        return [oArc.centroid(d), outerArc.centroid(d), pos]
      })
  },
  data() {
    return {}
  },
  methods: {
    midAngel(d) {
      return d.startAngle + (d.endAngle - d.startAngle) / 2
    }
  }
}
</script>
<style scoped>
svg {
  border-radius: 10px;
  border: 1px solid #000;
}
/* polyline {
  fill: none;
  stroke: green;
  stroke-width: 2px;
  stroke-dasharray: 5px 5px;
  opacity: .1;
} */
</style>
