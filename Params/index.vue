<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <el-alert
        show-icon
        title="注意：只允许为第三级分类设置相关参数！"
        type="warning"
        :closable="false"
      >
      </el-alert>

      <div class="block">
        <span class="demonstration">选择商品分类:</span>
        <el-cascader
          v-model="value"
          :options="selectList"
          :props="{ label: 'cat_name', value: 'cat_id' }"
        ></el-cascader>
      </div>

      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="动态参数" name="many">
          <el-button
            type="primary"
            @click="dialogFormVisible = true"
            :disabled="!flag"
            >添加属性</el-button
          >
          <el-table
            :data="paramsData"
            border
            stripe
            max-height="400"
            style="width: 100%"
          >
            <el-table-column type="expand">
              <template slot-scope="props">
                <el-form label-position="left" inline class="demo-table-expand">
                  <el-form-item label="商品名称">
                    <span>{{ props.row.name }}</span>
                    <el-tag
                      :key="tag"
                      v-for="tag in dynamicTags"
                      closable
                      :disable-transitions="false"
                      @close="handleClose(tag)"
                    >
                      {{ tag }}
                    </el-tag>
                    <el-input
                      class="input-new-tag"
                      v-if="inputVisible"
                      v-model="inputValue"
                      ref="saveTagInput"
                      size="small"
                      @keyup.enter.native="handleInputConfirm"
                      @blur="handleInputConfirm"
                    >
                    </el-input>
                    <el-button
                      v-else
                      class="button-new-tag"
                      size="small"
                      @click="showInput"
                      >+ New Tag</el-button
                    >
                  </el-form-item>
                </el-form>
              </template>
            </el-table-column>
            <el-table-column type="index" :index="1" label="#">
            </el-table-column>
            <el-table-column label="分类名称" prop="attr_name">
            </el-table-column>
            <el-table-column label="操作" prop="">
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
                <el-dialog title="编辑用户" :visible.sync="changeParamsDialog">
                  <el-form :model="changeParamsForm" label-width="80px">
                    <el-form-item label="用户名称">
                      <el-input v-model="changeParamsForm.name"></el-input>
                    </el-form-item>
                  </el-form>
                  <div slot="footer" class="dialog-footer">
                    <el-button @click="changeParamsDialog = false">
                      取 消
                    </el-button>
                    <el-button type="primary" @click="changeParamsFormFn">
                      确 定
                    </el-button>
                  </div>
                </el-dialog>
                <!-- 编辑按钮 -->

                <!-- 删除按钮 -->
                <el-dialog title="提示" :visible.sync="deleteParamsDialog">
                  <p>
                    <i class="el-icon-warning"></i
                    ><span>此操作将永久删除该属性，是否继续?</span>
                  </p>
                  <div slot="footer">
                    <el-button @click="deleteStop"> cancel </el-button>
                    <el-button type="primary" @click="deleteParamsFormFn">
                      ok
                    </el-button>
                  </div>
                </el-dialog>
                <!-- 删除按钮 -->
              </template>
            </el-table-column>
          </el-table>
          <!-- <el-dialog title="添加动态参数" :visible.sync="dialogFormVisible">
            <el-form :model="form" :rules="rules" label-width="80px">
              <el-form-item label="动态参数">
                <el-input v-model="form.name"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="dialogFormVisible = false"
                >确 定</el-button
              >
            </div>
          </el-dialog> -->
        </el-tab-pane>
        <el-tab-pane label="静态属性" name="only">配置管理</el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
import { getParamsList, ParamsDelete, ParamsChange } from '@/api/params'
import { getCategories } from '@/api/categories'
export default {
  created () {
    this.ParamsList()
  },
  data () {
    return {
      select: {
        pagenum: 1,
        pagesize: 5
      },
      selectList: [],
      value: '',
      flag: false,
      activeName: 'many', // 点击默认的tabs
      dialogFormVisible: false, // 点击的弹出层
      form: {
        name: ''
      },
      rules: [{}], // 校验规则
      id: 0, // 商品id用于发送动态或静态的请求
      paramsForm: {
        id: 0,
        sel: ''
      },
      paramsData: [],
      dynamicTags: ['标签一', '标签二', '标签三'],
      inputVisible: false,
      inputValue: '',
      changeParamsDialog: false, // 点击编辑是否弹出
      changeParamsForm: {
        name: '',
        id: 0,
        attrId: 0,
        sel: ''
      }, // 编辑的数据对象
      deleteParamsDialog: false, // 点击删除是否弹出

      deleteList: {
        id: 0,
        attrId: 0
      }

    }
  },
  methods: {
    async ParamsList () {
      try {
        const res = await getParamsList(this.select)
        this.selectList = res.data.data.result
      } catch (err) {
        console.log(err)
      }
    },
    async handleClick () {
      if (this.value.length !== 3) {
        this.$message.error('请选择三级分类')
      } else {
        this.paramsForm.id = this.id
        this.paramsForm.sel = this.activeName
        try {
          const res = await getCategories(this.paramsForm)
          this.paramsData = res.data.data
        } catch (err) {
          console.log(err)
        }
      }
    },
    changeEdit (row) {
      console.log(row)
      this.changeParamsForm.id = row.cat_id
      this.changeParamsForm.attrId = row.attr_id
      this.changeParamsForm.name = row.attr_name
      this.changeParamsForm.sel = row.attr_sel

      this.changeParamsDialog = false
    },
    async changeParamsFormFn () {
      try {
        console.log(this.changeParamsForm)
        const res = await ParamsChange(this.changeParamsForm)
        console.log(res)
      } catch (err) {
        console.log(err)
      }
    },
    deleteStop () {
      this.deleteParamsDialog = false
      this.$message('已取消删除')
    },
    deleteEdit (row) {
      console.log(row)
      this.deleteList.id = row.cat_id
      this.deleteList.attrId = row.attr_id
      this.deleteParamsDialog = true
    },
    async deleteParamsFormFn () {
      try {
        console.log(this.deleteList)
        const res = await ParamsDelete(this.deleteList)
        console.log(res)
        this.$message({
          message: '删除成功',
          type: 'success'
        })
        this.handleClick()
        this.deleteUserDialog = true
      } catch (err) {
        console.log(err)
        this.$message.error('删除失败')
      }
    },
    handleClose (tag) {
      this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1)
    },
    showInput () {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },
    handleInputConfirm () {
      const inputValue = this.inputValue
      if (inputValue) {
        this.dynamicTags.push(inputValue)
      }
      this.inputVisible = false
      this.inputValue = ''
    }

  },
  computed: {},
  watch: {
    value () {
      if (this.value.length === 3) {
        this.id = this.value[this.value.length - 1]
        this.flag = true
      } else {
        this.value = 0
        this.flag = false

        this.$message.error('请选择三级分类')
      }
    }
  },
  filters: {},
  components: {
  }
}
</script>

<style scoped lang='less'>
.main {
  margin-top: 15px;
  padding: 10px 20px;
  border-radius: 4px;
  background-color: #fff;
  border: 1px solid #ebeef5;
  color: #303133;
  .block {
    margin: 10px 0;
  }
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
}
</style>
