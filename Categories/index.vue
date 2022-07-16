<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <el-button @click="addCatDialog = true" type="primary"
        >添加商品</el-button
      >
      <el-dialog title="添加商品" :visible.sync="addCatDialog">
        <el-form>
          <el-form-item label-width="100px" label="分类名称：">
            <el-input v-model="input"></el-input>
          </el-form-item>
        </el-form>

        <div class="block">
          <span>选择商品分类：</span>
          <el-cascader
            v-model="value"
            :options="cateArr.result"
            :props="{ label: 'cat_name', value: 'cat_id' }"
          ></el-cascader>
        </div>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addCatDialog = false">取 消</el-button>
          <el-button type="primary" @click="addCatFormFn">确 定</el-button>
        </div>
      </el-dialog>

      <div class="center">
        <el-table :data="cateArr.result" border stripe style="width: 100%">
          <el-table-column type="index" :index="1" label="#"> </el-table-column>
          <el-table-column prop="cat_name" label="分类名称"> </el-table-column>
          <el-table-column prop="cat_deleted" label="是否有效" width="100">
          </el-table-column>
          <el-table-column prop="cat_level" label="排序" width="100">
          </el-table-column>
          <el-table-column label="操作" width="200">
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
              <el-dialog title="编辑用户" :visible.sync="catChangeDialog">
                <el-form :model="catChangeForm" label-width="80px">
                  <el-form-item label="名称" prop="name">
                    <el-input v-model="catChangeForm.name"></el-input>
                  </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="catChangeDialog = false">
                    取 消
                  </el-button>
                  <el-button type="primary" @click="catChangeFormFn">
                    确 定
                  </el-button>
                </div>
              </el-dialog>
              <!-- 编辑按钮 -->

              <!-- 删除按钮 -->
              <el-dialog title="提示" :visible.sync="deleteCatDialog">
                <p>
                  <i class="el-icon-warning"></i
                  ><span>此操作将永久删除该商品，是否继续?</span>
                </p>
                <div slot="footer">
                  <el-button @click="deleteStop"> cancel </el-button>
                  <el-button type="primary" @click="deleteCatFormFn">
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
          :page-sizes="[1, 2, 4, 6, 10]"
          :page-size="selectSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="cateArr.total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { getParamsList } from '@/api/params'
import { deleteCategories, changeCategories } from '@/api/categories'

export default {
  created () {
    this.cateListFn()
    this.selectForm.pagesize = this.selectSize
  },
  data () {
    return {
      cateArr: {},
      addCatDialog: false, // 点击添加分类是否弹出
      catChangeDialog: false, // 点击编辑分类是否弹出
      catChangeForm: {
        id: 0,
        name: ''
      }, // 编辑分类的数据对象
      deleteCatDialog: false, // 点击删除分类是否弹出
      deleteId: 0,
      select: '',
      currentPage: 1, // 当前页
      selectForm: {
        pagenum: 1,
        pagesize: 4
      },
      selectSize: 4,
      value: '',
      input: ''

    }
  },
  methods: {

    async cateListFn () {
      try {
        const res = await getParamsList(this.selectForm)
        console.log(res)
        this.cateArr = res.data.data

        console.log(this.data)
      } catch (err) {
        console.log(err)
      }
    },

    changeEdit (row) {
      this.catChangeDialog = true
      this.catChangeForm.id = row.cat_id
      this.catChangeForm.name = row.cat_name
    },
    async catChangeFormFn () {
      try {
        console.log(this.catChangeForm)
        const res = await changeCategories(this.catChangeForm)
        this.catChangeForm = res.data.data
        console.log(res)

        this.$message({
          message: '修改成功',
          type: 'success'
        })
        this.cateListFn()
        this.catChangeDialog = false
      } catch (err) {
        console.log(err)
      }
    },
    deleteStop () {
      this.deleteCatDialog = false
      this.$message('已取消删除')
    },
    deleteEdit (row) {
      console.log(row)
      this.deleteId = row.cat_id
      this.deleteCatDialog = true
    },
    async deleteCatFormFn () {
      try {
        await deleteCategories(this.deleteId)
        this.$message({
          message: '删除成功',
          type: 'success'
        })
        this.cateListFn()
        this.deleteCatDialog = false
      } catch (err) {
        console.log(err)
        this.$message.error('删除失败')
      }
    },

    async handleSizeChange (val) {
      this.selectForm.pagesize = val
      const res = await getParamsList(this.selectForm)
      this.cateArr = res.data.data
    },
    async handleCurrentChange (val) {
      this.selectForm.pagenum = val
      const res = await getParamsList(this.selectForm)
      this.cateArr = res.data.data
    },
    async addCatFormFn () {
      // console.log(this.value, this.input)
      console.log(this.cateArr.result)
      // this.addCatDialog = false
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

    .center {
      margin-top: 15px;
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
