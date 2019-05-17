<template>
  <svg height="300" ref="svg" width="550"></svg>
</template>
<script>
import * as d3 from 'd3'
export default {
  name: 'pieChart',
  props: {
    info: Array
  },
  mounted() {
    this.calcPercent()
    const width = 550,
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
    const slices = main.append('g').attr('class', 'slices')
    const lines = main.append('g').attr('class', 'lines')
    const labels = main.append('g').attr('class', 'labels')
    const arcs = slices
      .selectAll('g')
      .data(pieData)
      .enter()
      .append('path')
      .attr('fill', (d, i) => this.info[i].color)
      .attr('stroke', '#ffffff')
      .attr('stroke-width', '2px')
      .transition()
      .ease(d3.easeBounce)
      .duration(1000)
      .attrTween('d', this.tweenPie)
      .transition()
      .ease(d3.easeElastic)
      .delay((d, i) => {
        return 500 + i * 50
      })
      .attrTween('d', this.tweenDonut)
      .attr('d', d => arc(d))
    const texts = labels
      .selectAll('text')
      .data(pieData)
      .enter()
      .append('text')
      .style('opacity', '0')
      .attr('dy', '0.35em')
      .attr('fill', (d, i) => {
        return this.info[i].color
      })
      .transition()
      .duration(1000)
      .delay(3000)
      .text((d, i) => {
        return d.data.name + this.percent[i]
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
      .transition()
      .duration(1000)
      .delay(2500)
      .attr('stroke', (d, i) => this.info[i].color)
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
    return {
      radius: 100,
      arc: d3
        .arc()
        .innerRadius(0)
        .outerRadius(this.radius),
      percent: []
    }
  },
  methods: {
    midAngel(d) {
      return d.startAngle + (d.endAngle - d.startAngle) / 2
    },
    tweenPie(b) {
      b.innerRadius = 0
      const i = d3.interpolate({ startAngle: 0, endAngle: 0 }, b)
      return t => this.arc(i(t))
    },
    tweenDonut(b) {
      b.innerRadius = this.radius * 0.6
      const i = d3.interpolate({ innerRadius: 0 }, b)
      return t => this.arc(i(t))
    },
    calcPercent() {
      let arr = []
      let sum = 0
      for (let item of this.info) {
        sum += item.value
      }
      this.info.forEach((item) => {
        let num = ((item.value / sum) * 100).toFixed(2)
        this.percent.push(`${num}%`)
      })
      console.log(this.percent)
    }
  }
}
</script>
<style scoped>
svg {
  border-radius: 10px;
  border: 1px solid #000;
}
</style>
