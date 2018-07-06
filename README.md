# echarts-middleware
在vue中优雅，高效的使用echarts

2018年7月6日更新 可以自动检测图表数据变化

## 快速上手

安装

```
npm install -save echarts
npm install -save echarts-middleware
```

引入并注册

```
<script>
  import 'echarts'
  import Chart from 'echarts-middleware'
  export default {
    components: {
      Chart
    }
  }
</script>
```

使用
```
<template>
  <div class="my-chart">
    <Chart v-model="mock" :size="{w: 400, h: 400}"></Chart>
  <div>
</template>

<script>
  import Chart from 'echarts-middleware'
  export default {
    components: {
      Chart
    },
    data () {
      return {
        mock: {
          'series': [{
            'name': 'gauge',
            'type': 'gauge',
            'detail': {'formatter': '{value}'},
            'data': [{'value': 34}]
          }]
        }
      }
    }
  }
</script>
```
结果
![结果](http://my-owo-ink.b0.upaiyun.com/puge/github/echarts-middleware/1.png)


## 参数

| 参数名      | 含义         | 类型  | 是否必须  |
| ----------- |:-------------:| -----:| -----:|
|value| 图表的配置 | Object | true |
|size| 图表的宽高| Object | false |
|theme| 主题| String, Object | false |
|renderer| 渲染器| String | false |

## 事件

| 事件名        | 触发情况         | 返回值类型  |
| ----------- |:-------------:| -----:|
|init| 图表注册完成 | echartsInstance(Echart实例) |


使用实例：

```
<Chart v-model="data" :size="{w: 400, h: 400}"></Chart>
```

注:如果 w h 参数没有设置，组件会以父组件宽高作为组件宽高， 如果父组件没有宽高则会以400px作为宽高。

## 组件特点
* 高效: 直接将echarts实例暴露给vue,可以直接操作
* 优雅: 自动处理echarts的 注册、重新赋值 及 销毁 操作
* 简洁: 主要代码少于50行 没有性能损失
* 接口丰富: 由于直接暴露 echarts实例 所以理论上能用 100% echarts方法

## 简单对比

传统vue中使用echarts
```
<template>
  <div class="box">
    <div class="charts" v-for="item in 6" :id="'chart' + item" :key="item"></div>
  </div>
</template>

<script>
  import echarts from 'echarts'
  let myChart = []
  export default {
    data () {
      return {
        mock: {
          'series': [{
            'name': 'gauge',
            'type': 'gauge',
            'detail': {'formatter': '{value}'},
            'data': [{'value': 34}]
          }]
        }
      }
    },
    created () {
      for (let i = 0; i < 6; i++) {
        myChart[i] = echarts.init(document.getElementById('chart' + i))
        myChart[i].setOption(this.mock)
      }
    },
    beforeDestroy () {
      for (let i = 0; i < 6; i++) {
        myChart[i].dispose()
      }
    }
  }
</script>
```

使用echarts-middleware达到同样的效果

```
<template>
  <div class="box">
    <Chart v-for="item in 6" v-model="mock" :key="item"></div>
  </div>
</template>

<script>
  import echarts from 'echarts'
  import Chart from 'echarts-middleware'
  export default {
    components: {
      Chart
    },
    data () {
      return {
        mock: {
          'series': [{
            'name': 'gauge',
            'type': 'gauge',
            'detail': {'formatter': '{value}'},
            'data': [{'value': 34}]
          }]
        }
      }
    }
  }
</script>
```


## 获取Echarts对象的方法

1.使用init事件取得Echarts实例
```
<template>
  <div>
    <Chart v-model="mock" :size="{w: 400, h: 400}", @init="chartInit"></Chart>
    <button @click="click">销毁图表</button>
  </div>
</template>
<script>
  import Chart from 'echarts-middleware'
  export default {
    components: {
      Chart
    },
    data () {
      return {
        chart: null,
        mock: {
          'series': [{
            'name': 'gauge',
            'type': 'gauge',
            'detail': {'formatter': '{value}'},
            'data': [{'value': 34}]
          }]
        }
      }
    },
    methods: {
      // 图表初始化完毕后的回调
      chartInit (chart) {
        this.chart = chart
      },
      click () {
        console.log('输出echarts对象', this.chart)
        console.log('销毁echarts图表')
        this.chart.dispose()
      }
    }
  }
</script>
```
支持的方法，理论支持echarts所有方法，具体方法列表请查阅 [API文档](http://echarts.baidu.com/api.html#echarts)