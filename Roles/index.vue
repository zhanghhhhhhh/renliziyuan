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
            {{ props }}
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

              <el-button
                type="warning"
                icon="el-icon-setting"
                @click="setEdit(scope.row)"
                >分配权限</el-button
              >
            </div>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import { getRolesList } from '@/api/roles'
export default {
  created () {
    this.getRolesList()
  },
  data () {
    return {
      rolesData: []

    }
  },
  methods: {
    async getRolesList () {
      try {
        const res = await getRolesList()
        console.log(res)
        this.rolesData = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    changeEdit (row) {
      console.log(row)
    },
    deleteEdit (row) {
      console.log(row)
    },
    setEdit (row) {
      console.log(row)
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
