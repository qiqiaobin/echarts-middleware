<template>
  <div v-once class="echarts"></div>
</template>

<script>
  import echarts from 'echarts/lib/echarts'
  // echarts实例
  export default {
    props: {
      value: null,
      size: Object,
      theme: [String, Object],
      renderer: {
        type: String,
        default: 'canvas'
      }
    },
    data () {
      return {
        chart: null
      }
    },
    mounted () {
      const value = this.value
      if (!value) {
        console.error('没有绑定图表数据!')
        return
      }
      let width, height
      if (!echarts) {
        console.error('本组件需要配合echarts组件使用,请运行npm i -save echarts安装!')
        return null
      }
      // 判断用户是否指定了组件的 宽 和 高，如果指定了，那么使用用户指定的值
      // 如果没有指定则使用 父组件 宽高
      // 如果出错 使用400px默认宽高
      if (this.size && this.size.w && this.size.h) {
        width = this.size.w
        height = this.size.h
      }
      if (!width && !height) {
        const parent = this.$el.parentNode
        if (parent && parent.clientWidth && parent.clientHeight) {
          height = parent.clientHeight
          width = parent.clientWidth
        } else {
          height = width = 400
          console.error('图表 宽度(w) 和 高度(h) 未设置!')
        }
      }
      // 注册echarts
      if (this.value) {
        setTimeout(() => {
          const renderer = this.renderer
          this.chart = echarts.init(this.$el, this.theme, {width, height, renderer})
          // 绘制图表
          this.chart.setOption(this.value)
          // 将chart对象暴露给父组件
          this.$emit('init', this.chart)
        }, 0)
      }
    },
    watch: {
      size: {
        handler: function (val, oldVal) {
          // 避免不正常调用导致报错
          if (!this.chart) return
          if (val === undefined || val === null) return
          // 如果高度配置发生改变，更改图表大小
          this.chart.resize({
            width: val.w,
            height: val.h
          })
        },
        deep: true
      },
      value: {
        handler: function (val, oldVal) {
          // 组件刚刚没创建时value会发生修改，但是这时Echarts实例还没有生成
          if (!this.chart) return
          // 判断传入的格式是否正确,如果不正确或者为空则销毁图表
          if (val !== null && typeof val === 'object') {
            this.chart.setOption(val) 
          } else {
            console.info('没有传入配置项或者配置项不正确!')
            this.chart.clear()
          }
        },
        deep: true
      }
    },
    beforeDestroy () {
      if (this.chart) {
        this.chart.dispose()
        this.chart = null
      }
    }
  }
</script>
