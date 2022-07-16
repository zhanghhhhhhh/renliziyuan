<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <div class="header">
        <div class="input">
          <el-input
            placeholder="请输入内容"
            v-model="inputAdd"
            @change="goodsSearch"
          >
            <el-button slot="append" icon="el-icon-search"></el-button>
          </el-input>
        </div>
        <button @click="$router.push('/goods/add')">添加商品</button>
      </div>
      <div class="center">
        <el-table
          :data="GoodsArr.goods"
          border
          stripe
          max-height="400"
          style="width: 100%"
        >
          <el-table-column type="index" :index="1" prop="username" label="#">
          </el-table-column>
          <el-table-column prop="goods_name" label="商品名称">
          </el-table-column>
          <el-table-column
            prop="goods_price"
            label="商品价格（元）"
            width="100"
          >
          </el-table-column>
          <el-table-column prop="goods_weight" label="商品重量" width="100">
          </el-table-column>
          <el-table-column prop="upd_time" label="创建时间" width="150">
          </el-table-column>
          <el-table-column prop="role_name" label="操作" width="200">
            <template slot-scope="scope">
              <div class="btn">
                <el-button
                  type="primary"
                  icon="el-icon-edit"
                  @click="changeEdit(scope.row)"
                ></el-button>

                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  @click="deleteEdit(scope.row)"
                ></el-button>
              </div>
              <!-- 编辑按钮 -->
              <el-dialog title="编辑用户" :visible.sync="goodsChangeDialog">
                <el-form :model="goodsChangeForm" label-width="80px">
                  <el-form-item label="商品名称" prop="name">
                    <el-input v-model="goodsChangeForm.name"></el-input>
                  </el-form-item>
                  <el-form-item label="商品价钱" prop="price">
                    <el-input v-model="goodsChangeForm.price"></el-input>
                  </el-form-item>
                  <el-form-item label="商品数量" prop="number">
                    <el-input v-model="goodsChangeForm.number"></el-input>
                  </el-form-item>
                  <el-form-item label="商品重量" prop="weight">
                    <el-input v-model="goodsChangeForm.weight"></el-input>
                  </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="goodsChangeDialog = false">
                    取 消
                  </el-button>
                  <el-button type="primary" @click="goodsChangeFormFn">
                    确 定
                  </el-button>
                </div>
              </el-dialog>
              <!-- 编辑按钮 -->

              <!-- 删除按钮 -->
              <el-dialog title="提示" :visible.sync="deleteUserDialog">
                <p>
                  <i class="el-icon-warning"></i
                  ><span>此操作将永久删除该商品，是否继续?</span>
                </p>
                <div slot="footer">
                  <el-button @click="deleteStop"> cancel </el-button>
                  <el-button type="primary" @click="deleteUserFormFn">
                    ok
                  </el-button>
                </div>
              </el-dialog>
              <!-- 删除按钮 -->
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
          :total="GoodsArr.total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { GoodsList, GoodsChange, GoodsDelete } from '@/api/goods'
export default {
  created () {
    this.goodsListFn()
    this.selectForm.pagesize = this.selectSize
  },
  data () {
    return {
      GoodsArr: {},
      goodsChangeArr: {},
      inputAdd: '', // 搜索用户的input

      goodsChangeDialog: false, // 点击编辑用户是否弹出
      goodsChangeForm: {
        id: 0,
        name: '',
        price: 0,
        number: 1,
        weight: 0
      }, // 编辑用户的数据对象
      deleteUserDialog: false, // 点击删除用户是否弹出
      deleteId: 0,
      select: '',
      currentPage: 1, // 当前页
      selectForm: {
        pagenum: 1,
        pagesize: 5
      },
      selectSize: 5
    }
  },
  methods: {
    async goodsListFn () {
      try {
        const res = await GoodsList(this.selectForm)
        this.GoodsArr = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    async goodsSearch () {
      try {
        const res = await GoodsList({ query: this.inputAdd })
        this.GoodsArr = res.data.data
      } catch (err) {
        console.log(err)
      }
    },

    changeEdit (row) {
      this.goodsChangeDialog = true
      this.goodsChangeForm.id = row.goods_id
      this.goodsChangeForm.name = row.goods_name
      this.goodsChangeForm.price = row.goods_price
      this.goodsChangeForm.number = row.goods_number
      this.goodsChangeForm.weight = row.goods_weight
    },
    async goodsChangeFormFn () {
      try {
        console.log(this.goodsChangeForm)
        const res = await GoodsChange(this.goodsChangeForm)
        this.goodsChangeForm = res.data.data
        console.log(res)

        this.$message({
          message: '修改成功',
          type: 'success'
        })
        this.goodsListFn()
        this.goodsChangeDialog = false
      } catch (err) {
        console.log(err)
      }
    },
    deleteStop () {
      this.deleteUserDialog = false
      this.$message('已取消删除')
    },
    deleteEdit (row) {
      this.deleteId = row.goods_id
      this.deleteUserDialog = true
    },
    async deleteUserFormFn () {
      try {
        const res = await GoodsDelete(this.deleteId)
        console.log(res)
        this.$message({
          message: '删除成功',
          type: 'success'
        })
        this.goodsListFn()
        this.deleteUserDialog = false
      } catch (err) {
        console.log(err)
        this.$message.error('删除失败')
      }
    },

    async handleSizeChange (val) {
      this.selectForm.pagesize = val
      const res = await GoodsList(this.selectForm)
      this.GoodsArr = res.data.data
    },
    async handleCurrentChange (val) {
      this.selectForm.pagenum = val
      const res = await GoodsList(this.selectForm)
      this.GoodsArr = res.data.data
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
    .header {
      height: 40px;
      display: flex;
      margin-bottom: 10px;
      button:nth-child(2) {
        left: 20px;
        min-width: 98px;
        height: 40px;
        color: #fff;
        background-color: #5eadff;
        border: 1px solid #dcdfe6;
        margin-left: 20px;
      }
    }
    .center {
      /deep/.el-dialog__body {
        .el-icon-warning {
          color: #e6a23c;
          font-size: 24px;
          margin-right: 15px;
        }
        span {
          font-size: 20px;
        }
      }
      .btn {
        /deep/.el-button {
          width: 44px;
          height: 28px;
          margin-right: 5px;
          position: relative;
          i {
            position: absolute;
            top: 6px;
            left: 0;
            right: 0;
          }
        }
      }
    }
    .footer {
      margin-top: 10px;
    }
  }
}
</style>
