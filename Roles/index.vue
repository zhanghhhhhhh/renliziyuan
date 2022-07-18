<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <div class="main">
      <el-button type="primary">主要按钮</el-button>

      <el-table :data="rolesData" style="width: 100%" stripe border>
        <el-table-column type="expand" label="#" width="50">
          <template slot-scope="props">
            {{ props.row }}
          </template>
        </el-table-column>
        <el-table-column type="index" label="#" width="50"> </el-table-column>
        <el-table-column prop="roleName" label="角色名称" width="200">
        </el-table-column>
        <el-table-column prop="roleDesc" label="角色描述" width="200">
        </el-table-column>
        <el-table-column prop="desc" label="操作">
          <template slot-scope="scope">
            <div class="btn">
              <el-button
                type="primary"
                icon="el-icon-edit"
                @click="changeEdit(scope.row)"
                >编辑</el-button
              >

              <el-button
                type="danger"
                icon="el-icon-delete"
                @click="deleteEdit(scope.row)"
              >
                删除</el-button
              >

              <el-dialog title="提示" :visible.sync="deleteUserDialog">
                <p>
                  <i class="el-icon-warning"></i
                  ><span>此操作将永久删除，是否继续?</span>
                </p>
                <div slot="footer">
                  <el-button @click="deleteStop"> cancel </el-button>
                  <el-button type="primary" @click="deleteUserFormFn">
                    ok
                  </el-button>
                </div>
              </el-dialog>

              <el-button type="warning" icon="el-icon-setting" @click="onSubmit"
                >分配权限</el-button
              >
              <el-dialog title="收货地址" :visible.sync="dialogFormVisible">
                <el-tree
                  :data="data1"
                  show-checkbox
                  :node-key="data1.id"
                  :props="defaultProps"
                  default-expand-all
                  :default-checked-keys="[146]"
                >
                </el-tree>

                <div slot="footer">
                  <el-button @click="dialogFormVisible = false"
                    >取 消</el-button
                  >
                  <el-button type="primary" @click="dialogFormVisible = false"
                    >确 定</el-button
                  >
                </div>
              </el-dialog>
            </div>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import { getRolesList, deleteUserFormFn } from '@/api/roles'
import { getRightsList } from '@/api/rights'

export default {
  created () {
    this.getRolesList()
  },
  data () {
    return {
      rolesData: [],
      deleteId: 0,
      deleteUserDialog: false,
      dialogFormVisible: false, // 分配弹出
      data1: [],
      defaultProps: {
        children: 'children',
        label: 'authName'
      }
    }
  },
  methods: {
    async getRolesList () {
      try {
        const res = await getRolesList()
        this.rolesData = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    changeEdit (row) {
      console.log(row)
    },
    deleteStop () {
      this.deleteCatDialog = false
      this.$message('已取消删除')
    },
    deleteEdit (row) {
      console.log(row)
      this.deleteId = row.id
      this.deleteUserDialog = true
    },
    async deleteUserFormFn () {
      try {
        await deleteUserFormFn({ id: this.deleteId })
        this.$message({
          message: '删除成功',
          type: 'success'
        })
        this.getRolesList()
        this.deleteCatDialog = false
      } catch (err) {
        console.log(err)
      }
    },
    async onSubmit () {
      this.dialogFormVisible = true
      const res = await getRightsList({ type: 'tree' })
      console.log(res)
      this.data1 = res.data.data
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.main {
  margin-top: 15px;
  padding: 15px 20px 20px;
  border-radius: 4px;
  background-color: #fff;
  border: 1px solid #ebeef5;
  color: #303133;
  .el-table {
    margin-top: 15px;
  }
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
</style>
