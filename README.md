# Pagination-without-the-total-number-of-pages
# 不需要总页数的分页
Pagination of the total number of pages is not required
  - 传入： startPage: 当前页  
          dataLength: 数据长度  
          [limit]: 每页显示条数,默认8条  
  - 在外监听： changePage: 返回值为新页数 赋值给父组件的当前页变量
