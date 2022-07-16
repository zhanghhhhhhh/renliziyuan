<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <div class="header">
        <div class="input">
          <el-input
            placeholder="请输入内容"
            v-model="inputAdd"
            @change="UsersSearch"
          >
            <el-button slot="append" icon="el-icon-search"></el-button>
          </el-input>
        </div>
        <button @click="addUserDialog = true">添加用户</button>
        <el-dialog title="添加用户对话框" :visible.sync="addUserDialog">
          <el-form :model="addUserForm" :rules="rules" label-width="80px">
            <el-form-item label="用户名" prop="username">
              <el-input v-model.trim="addUserForm.username"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password">
              <el-input
                type="password"
                v-model.trim="addUserForm.password"
              ></el-input>
            </el-form-item>
            <el-form-item label="邮箱" prop="email">
              <el-input v-model.trim="addUserForm.email"></el-input>
            </el-form-item>
            <el-form-item label="手机号" prop="mobile">
              <el-input v-model.trim="addUserForm.mobile"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="addUserDialog = false">取 消</el-button>
            <el-button type="primary" @click="addUserFormFn">确 定</el-button>
          </div>
        </el-dialog>
      </div>
      <div class="center">
        <el-table
          :data="UsersArr.users"
          border
          stripe
          max-height="400"
          style="width: 100%"
        >
          <el-table-column type="index" :index="1" label="#"> </el-table-column>
          <el-table-column prop="username" label="姓名"> </el-table-column>
          <el-table-column prop="mobile" label="电话"> </el-table-column>
          <el-table-column prop="role_name" label="角色"> </el-table-column>
          <el-table-column prop="mg_state" label="状态">
            <template slot-scope="scope">
              <el-switch
                v-model="scope.row.mg_state"
                active-color="##dcdfe6"
                inactive-color="##409effs"
              >
              </el-switch>
            </template>
          </el-table-column>
          <el-table-column prop="role_name" label="操作">
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

                <el-button
                  type="warning"
                  icon="el-icon-setting"
                  @click="setEdit(scope.row)"
                ></el-button>
              </div>
              <!-- 编辑按钮 -->
              <el-dialog title="编辑用户" :visible.sync="changeUserDialog">
                <el-form
                  :model="changeUserForm"
                  :rules="rules1"
                  label-width="80px"
                >
                  <el-form-item label="用户名称">
                    <el-input
                      v-model="changeUserForm.username"
                      disabled
                    ></el-input>
                  </el-form-item>
                  <el-form-item label="邮箱" prop="email">
                    <el-input v-model="changeUserForm.email"></el-input>
                  </el-form-item>
                  <el-form-item label="电话" prop="mobile">
                    <el-input v-model="changeUserForm.mobile"></el-input>
                  </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                  <el-button @click="changeUserDialog = false">
                    取 消
                  </el-button>
                  <el-button type="primary" @click="changeUserFormFn">
                    确 定
                  </el-button>
                </div>
              </el-dialog>
              <!-- 编辑按钮 -->

              <!-- 删除按钮 -->
              <el-dialog title="提示" :visible.sync="deleteUserDialog">
                <p>
                  <i class="el-icon-warning"></i
                  ><span>此操作将永久删除该用户，是否继续?</span>
                </p>
                <div slot="footer">
                  <el-button @click="deleteStop"> cancel </el-button>
                  <el-button type="primary" @click="deleteUserFormFn">
                    ok
                  </el-button>
                </div>
              </el-dialog>
              <!-- 删除按钮 -->

              <!-- 设置按钮 -->
              <el-dialog title="分配角色" :visible.sync="setUserDialog">
                <p>当前的用户：{{ setUserForm.username }}</p>
                <p>当前的角色：{{ setUserForm.role_name }}</p>
                <p>
                  分配新角色：
                  <el-select
                    v-model="select"
                    slot="prepend"
                    placeholder="请选择"
                  >
                    <el-option
                      v-for="(item, index) in roles"
                      :key="index"
                      :label="item.roleName"
                      :value="index"
                    ></el-option>
                  </el-select>
                </p>

                <div slot="footer">
                  <el-button @click="setUserDialog = false"> 取消 </el-button>
                  <el-button type="primary" @click="setUserFormFn">
                    确定
                  </el-button>
                </div>
              </el-dialog>
              <!-- 设置按钮 -->
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
          :total="UsersArr.total"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { UsersList, UserAdd, UserChange, UserDelete, rolesUser, UserSet } from '@/api/user'
export default {
  created () {
    this.UserListFn()
    this.selectForm.pagesize = this.selectSize
  },
  data () {
    return {
      UsersArr: {},
      UserChangeArr: {},
      inputAdd: '', // 搜索用户的input
      addUserDialog: false, // 点击添加用户是否弹出
      addUserForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      }, // 添加用户的数据对象
      changeUserDialog: false, // 点击编辑用户是否弹出
      changeUserForm: {
        username: '',
        id: 0,
        email: '',
        mobile: ''
      }, // 编辑用户的数据对象
      deleteUserDialog: false, // 点击删除用户是否弹出
      deleteId: 0,
      setUserDialog: false, // 点击分配角色是否弹出
      setUserForm: {
        username: '',
        role_name: '',
        id: 0,
        rid: 0
      }, // 分配角色的数据对象
      roles: {},
      rules: {
        username: [
          { required: true, message: '请输入用户名称', trigger: 'blur' },
          { min: 2, max: 7, message: '长度在2-7个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 2, max: 7, message: '长度在2-7个字符', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: /^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/, message: '邮箱格式不正确', trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { validator: /^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-79])|(?:5[0-35-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[189]))\d{8}$/, message: '手机号码格式不正确', trigger: 'blur' }]
      }, // 添加用户的校验规则
      rules1: {
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          { validator: /^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/, message: '邮箱格式不正确', trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { validator: /^(?:(?:\+|00)86)?1(?:(?:3[\d])|(?:4[5-79])|(?:5[0-35-9])|(?:6[5-7])|(?:7[0-8])|(?:8[\d])|(?:9[189]))\d{8}$/, message: '手机号码格式不正确', trigger: 'blur' }]
      },
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
    async UserListFn () {
      try {
        const res = await UsersList(this.selectForm)
        this.UsersArr = res.data.data
        console.log(res)
      } catch (err) {
        console.log(err)
      }
    },
    async UsersSearch () {
      try {
        const res = await UsersList({ query: this.inputAdd })
        this.UsersArr = res.data.data
      } catch (err) {
        console.log(err)
      }
    },
    async addUserFormFn () {
      try {
        if (this.addUserForm.username.length === 0 || this.addUserForm.password.length === 0 || this.addUserForm.email.length === 0 || this.addUserForm.mobile.length === 0) {
          return this.$message({
            message: '请输入正确数据',
            type: 'warning'
          })
        }
        const res = await UserAdd(this.addUserForm)
        this.$message({
          message: '创建成功',
          type: 'success'
        })
        this.UsersArr.users.push(res.data.data)
        this.$store.commit('setUserList', this.UsersArr.users)
        this.UserListFn()
        this.addUserForm = {}
        console.log(res)
        this.addUserDialog = false
      } catch (err) {
        console.log(err)
        this.$message({
          message: '创建失败',
          type: 'warning'
        })
      }
    },
    changeEdit (row) {
      this.changeUserDialog = true
      this.changeUserForm.username = row.username
      this.changeUserForm.id = row.id
      this.changeUserForm.email = row.email
      this.changeUserForm.mobile = row.mobile
    },
    async changeUserFormFn () {
      try {
        const res = await UserChange(this.changeUserForm)
        this.changeUserForm = res.data.data
        console.log(this.changeUserForm)

        this.$message({
          message: '修改成功',
          type: 'success'
        })
        this.UserListFn()
        this.changeUserDialog = false
      } catch (err) {
        console.log(err)
      }
    },
    deleteStop () {
      this.deleteUserDialog = false
      this.$message('已取消删除')
    },
    deleteEdit (row) {
      this.deleteId = row.id
      this.deleteUserDialog = true
    },
    async deleteUserFormFn () {
      try {
        console.log(this.deleteId)
        const res = await UserDelete(this.deleteId)
        console.log(res)
        this.$message({
          message: '删除成功',
          type: 'success'
        })
        this.UserListFn()
        this.deleteUserDialog = false
      } catch (err) {
        console.log(err)
        this.$message.error('删除失败')
      }
    },
    async setEdit (row) {
      const res = await rolesUser()
      this.roles = res.data.data
      console.log(this.roles)
      this.setUserForm.username = row.username
      this.setUserForm.role_name = row.role_name
      this.setUserForm.id = row.id
      this.setUserDialog = true
    },
    async setUserFormFn () {
      console.log(this.select)
      try {
        console.log(this.setUserForm)
        this.setUserForm.rid = this.roles[this.select].id
        const res = await UserSet(this.setUserForm)
        console.log(res)
        this.setUserDialog = false
      } catch (err) {
        console.log(err)
      }
    },
    async handleSizeChange (val) {
      this.selectForm.pagesize = val
      const res = await UsersList(this.selectForm)
      this.UsersArr = res.data.data
    },
    async handleCurrentChange (val) {
      this.selectForm.pagenum = val
      const res = await UsersList(this.selectForm)
      this.UsersArr = res.data.data
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
