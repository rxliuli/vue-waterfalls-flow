<template>
  <rx-waterfalls-flow :load="load">
    <!-- 这里 slotProps 绑定的便是子组件的数据，通过 slotProps 可以访问到子组件绑定到模板上的数据，当然，更简单的方法是使用 ES6 的解构 -->
    <template slot-scope="{item}">
      <!-- 在模板里面便可以使用集合中的元素 item 了 -->
      <li
        class="item-style"
        :key="item.id"
      >
        {{item.text}}
      </li>
    </template>
  </rx-waterfalls-flow>
</template>

<script>
// 引入瀑布流组件
import RxWaterfallsFlow from '@/components/common/RxWaterfallsFlow'
import _ from 'lodash'

export default {
  components: {
    RxWaterfallsFlow
  },
  methods: {
    // 使用 Promise 封装 setTimeout，模拟 ajax 的异步造成的延迟
    await: ms => new Promise(resolve => setTimeout(resolve, ms)),
    load: (page => {
      // 该方法用于模拟 ajax 数据加载
      return async function () {
        await this.await(1000)
        console.log(`加载了第 ${page.current} 页，共 ${page.pages} 页`)
        // 使用 lodash 模拟数据
        page.records = _.range(
          (page.current - 1) * page.size + 1,
          (++page.current - 1) * page.size + 1
        )
          .map(i => ({
            id: i,
            text: `第 ${i} 行内容`
          }))
        return page
      }
    })({
      current: 1,
      size: 10,
      pages: 100,
      total: this.current * this.pages,
      records: []
    })
  }
}
</script>

<style>
li {
  width: 500px;
  height: 200px;
  line-height: 200px;
  background-color: aqua;
  margin: 10px auto;
}
</style>
