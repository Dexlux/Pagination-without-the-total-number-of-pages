不需要总页数的分页
Pagination of the total number of pages is not required
传入： 当前页 数据长度 获取数据函数 [每页显示条数,默认8条]
在外监听： 页数变化
<template>
  <div class="pagination" v-show="dataLength == this.limit && startPage == 0 || startPage > 0">
    <a-divider />
    <div style="float:left">
      <span>当前第{{ currentPage }}页</span>
      <span>跳至<a-input @pressEnter="turnToPage" @blur="turnToPage" v-model="turnPage" />页</span>
    </div>
    <div style="float: right;">
      <a-button type="primary" style="margin-right: 20px;" @click="prePage" :disabled="preDisabled">上一页</a-button>
      <a-button type="primary" @click="afterPage" :disabled="afterDisabled">下一页</a-button>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        startPage: {
            type: Number,
            default: 0
        },
        dataLength: {
            type: Number,
            default: 0
        },
        limit: {
            type: Number,
            default: 8
        },
        getList: {
            type: Function,
            default: function () {}
        }
    },
    computed: {
      currentPage () {
        return (this.startPage / this.limit) + 1
      }
    },
    watch: {
      startPage (newVal, oldValue) {
        console.log(this.cachePage)
        if (newVal !== oldValue) {
          this.preDisabled = false
          this.afterDisabled = false
          // 判断跳转页是否为第一页
          if (newVal === 0) {
            this.preDisabled = true
          }
          // 缓存有数据的页数
          if (this.dataLength > 0) {
            this.cachePage = oldValue
          }
        }
      },
      dataLength  (newVal, oldValue) {
        // 判断能否点下一页
        if (newVal !== oldValue) {
          if (newVal < this.limit) {
            this.afterDisabled = true
          } else {
            this.afterDisabled = false
          }
        }
      }
    },
    data () {
        return {
            page: 0,
            cachePage: 0,
            turnPage: '',
            preDisabled: false,
            afterDisabled: false
        }
    },
    created () {
        this.page = this.startPage
        // 判断跳转页是否为第一页
        if (this.page === 0) {
          this.preDisabled = true
        }
    },
    methods: {
        // 跳转页数
        turnToPage () {
          const page = parseInt(this.turnPage)
          const type = isNaN(page)
          if (!type) {
            this.$emit('changePage', (page - 1) * this.limit)
            this.getList()
          }
          this.turnPage = ''
        },
        // 分页
        prePage () {
          if (this.dataLength === 0) {
            this.page = this.cachePage
          } else {
            this.page -= this.limit
          }
          this.$emit('changePage', this.page)
          this.getList()
        },
        afterPage () {
          this.page += this.limit
          this.$emit('changePage', this.page)
          this.getList()
        }
    }
}
</script>

<style lang="less" scoped>
  span{
    margin: 0 4px;
  }
  .ant-input{
    width: 40px;
    margin: 0 4px;
  }
  .pagination {
    margin-top: 30px;
  }
  .pagination:after {
    content: "";
    height: 0;
    display: block;
    clear: both;
  }
</style>
