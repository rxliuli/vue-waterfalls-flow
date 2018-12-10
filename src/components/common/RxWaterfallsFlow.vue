/**
自定义瀑布流组件
使用方法如下：
<rx-waterfalls-flow :load="load">
    <!-- 这里 slotProps 绑定的便是子组件的数据，通过 slotProps 可以访问到子组件绑定到模板上的数据，当然，更简单的方法是使用 ES6 的解构 -->
    <template slot-scope="{item}">
  <!-- 在模板里面便可以使用集合中的元素 item 了 -->
  <li :key="item.id">
    {{item.text}}
  </li>
</template>
  </rx-waterfalls-flow>
 */
<template>
  <div id="rx-waterfalls-flow-container">
    <slot
      v-for="item in items"
      :item="item"
    />
  </div>
</template>

<script>
export default {
  props: {
    load: {
      type: Function,
      default: function () {
        throw new Error('你需要为 RxWaterfallsFlow 组件定义分页加载的参数')
      }
    }
  },
  data: () => ({
    items: [],
    page: {
      total: 0,
      size: 10,
      pages: 10,
      current: 1,
      records: []
    }
  }),
  methods: {
    async loadPage (current, size) {
      this.page = await this.load(current, size)
      this.items.push(...this.page.records)
      this.page.records = []
    },
    /**
     * 初始化方法，加载第一页的数据，加载监听器
     */
    async init () {
      this.loadPage()
      // 绑定窗口滚动事件
      // 获得文档高度和滚动高度
      // 计算是否已经到底了
      // 到底的话就加载下一页的数据，否则忽略
      const otherOnscrollFn = document.onscroll ? document.onscroll : function () { }
      document.onscroll = () => {
        otherOnscrollFn()
        const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
        const clientHeight = document.documentElement.clientHeight
        const scrollHeight = document.documentElement.scrollHeight
        // console.log(`已滚动的高度：${scrollTop}, 滚动条高度：${scrollHeight}, ${clientHeight}`)
        // 向下滚动时判断判断是否正在向上滚动，是的话就清除定时器，停在当前位置
        if (scrollHeight - scrollTop - clientHeight <= 0 && this.page.current < this.page.pages) {
          this.loadPage(this.page.current + 1, this.page.size)
        }
      }
    }
  },
  mounted () {
    this.init()
  }
}
</script>

<style scoped>
/* 容器宽度要占 100% */
#rx-waterfalls-flow-container {
  width: 100%;
}
</style>
