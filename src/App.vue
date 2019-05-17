<template>
  <div id="app">
    <div>
      <circularChart
        :config="item"
        :index="index"
        :key="index"
        v-for="(item, index) in circleConfig"
      ></circularChart>
    </div>
    <div>
      <pieChart :info="info"></pieChart>
    </div>
    <div></div>
  </div>
</template>

<script>
import circularChart from './components/circularChart'
import pieChart from './components/pieChart'
export default {
  name: 'App',
  components: {
    circularChart,
    pieChart
  },
  data() {
    return {
      circleConfig: [
        {
          strokeOutBg: '#f2f2f2',
          strokeBg: '#ffd700',
          strokeOutW: '16.5px',
          strokeW: '15px',
          percent: 89.74
        },
        {
          strokeOutBg: '#f2f2f2',
          strokeBg: '	#6495ED',
          strokeOutW: '16.5px',
          strokeW: '15px',
          percent: 56.39,
          gradualChange: [
            {
              percent: '0%',
              color: '#6495ED'
            },
            {
              percent: '50%',
              color: '#4169E1'
            },
            {
              percent: '100%',
              color: '#0000CD'
            }
          ]
        },
        {
          strokeOutBg: '#f2f2f2',
          strokeBg: '	#000000',
          strokeOutW: '10px',
          strokeW: '15px',
          percent: 76
        }
      ],
      info: [
        { name: '杂费', value: 456, color: '#E6E6FA' },
        { name: '购物', value: 678, color: '#B0C4DE' },
        { name: '日常饮食', value: 1245, color: '#6495ED' },
        { name: '医药', value: 342, color: '#4169E1' },
        { name: '交通', value: 1289, color: '#000080' }
      ]
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  width: 100%;
  height: 1000px;
  background-color: #f2f2f2;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#app > div {
  padding: 20px;
  background-color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
}
</style>
