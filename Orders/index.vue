<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <div class="input">
        <el-input
          placeholder="请输入内容"
          v-model="inputAdd"
          @change="OrderSearch"
        >
          <el-button slot="append" icon="el-icon-search"></el-button>
        </el-input>
      </div>
      <div class="center">
        <el-table :data="ordersArr.goods" border stripe>
          <el-table-column type="index" :index="1" label="#"> </el-table-column>
          <el-table-column prop="order_number" label="订单编号">
          </el-table-column>
          <el-table-column prop="order_price" label="订单价格">
          </el-table-column>
          <el-table-column prop="order_pay" label="是否付款"></el-table-column>
          <el-table-column prop="is_send" label="是否发货"> </el-table-column>
          <el-table-column prop="create_time" label="下单时间">
          </el-table-column>

          <el-table-column>
            <template slot-scope="scope">
              <el-button
                type="primary"
                icon="el-icon-edit"
                @click="changeEdit(scope.row)"
              ></el-button>

              <el-dialog title="修改收货地址" :visible.sync="changeOrderDialog">
                <el-cascader
                  :options="cityOptions"
                  :value="city"
                  @change="changeProvince"
                >
                </el-cascader>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="changeOrderDialog = false">
                    取 消
                  </el-button>
                  <el-button type="primary" @click="changeOrderFormFn">
                    确 定
                  </el-button>
                </div>
              </el-dialog>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div class="footer">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[1, 2, 5, 10]"
          :page-size="selectSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="ordersArr.total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import cityOptions from '@/api/city_data'
import { OrdersList } from '@/api/orders'
export default {
  created () {
    this.OrderListFn()
    this.selectForm.pagesize = this.selectSize
  },
  data () {
    return {
      ordersArr: {},
      inputAdd: '', // 搜索用户的input
      changeOrderDialog: false, // 编辑按钮的弹出

      select: '',
      currentPage: 1, // 当前页
      selectForm: {
        pagenum: 1,
        pagesize: 10
      },
      selectSize: 10,
      cityOptions: cityOptions
    }
  },
  methods: {
    async OrderListFn () {
      try {
        const res = await OrdersList(this.selectForm)
        this.ordersArr = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    async OrderSearch () {
      try {
        const res = await OrdersList({ query: this.inputAdd })
        this.ordersArr = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    changeEdit (row) {
      // console.log(row)
      this.changeOrderDialog = true
    }, // 要修改数据的数据
    changeOrderFormFn () {

    }, // 发起修改的请求
    async handleSizeChange (val) {
      this.selectForm.pagesize = val
      const res = await OrdersList(this.selectForm)
      this.ordersArr = res.data.data
    },
    async handleCurrentChange (val) {
      this.selectForm.pagenum = val
      const res = await OrdersList(this.selectForm)
      this.ordersArr = res.data.data
    }

  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.container {
  .main {
    margin-top: 15px;
    padding: 10px 20px;
    border-radius: 4px;
    background-color: #fff;
    border: 1px solid #ebeef5;
    color: #303133;
    .input {
      width: 50%;
      margin-bottom: 20px;
    }
    .el-table {
      margin-bottom: 20px;
    }
  }
}
</style>
