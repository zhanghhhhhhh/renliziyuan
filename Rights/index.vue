<template>
  <div>
    <div class="container">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>权限管理</el-breadcrumb-item>
        <el-breadcrumb-item>权限列表</el-breadcrumb-item>
      </el-breadcrumb>

      <div class="main">
        <el-table :data="rightsNewData" style="width: 100%" stripe border>
          <el-table-column type="index" label="#" width="50"> </el-table-column>

          <el-table-column prop="authName" label="权限名称" width="180">
          </el-table-column>
          <el-table-column prop="path" label="路径" width="180">
          </el-table-column>
          <el-table-column prop="level" label="权限等级">
            <!-- <el-tag>等级一</el-tag>
            <el-tag type="success">等级二</el-tag>
            <el-tag type="warning">等级三</el-tag> -->
          </el-table-column>
        </el-table>
      </div>
    </div>
  </div>
</template>

<script>
import { getRightsList } from '@/api/rights'

export default {
  created () {
    this.getRightsList()
  },

  data () {
    return {
      rightsData: [], // 接口请求回来的数据
      rightsNewData: [], // 将请求回来的数据进行添加对应的level
      level2: [] // 对level属性的数据进行的配置
    }
  },
  methods: {
    async getRightsList () {
      try {
        const res = await getRightsList({ type: 'list' })
        this.rightsData = res.data.data

        this.rightsNewData = this.rightsData
        this.rightsData.forEach(item => {
          this.$set(item, 'level', '0')
          this.rightsNewData.push(...item.children)

          item.children.forEach(item1 => {
            this.$set(item1, 'level', '1')
            this.level2.push(...item1.children)
            this.level2.forEach(item2 => {
              this.$set(item2, 'level', '2')
            })
            this.rightsNewData.push(...item1.children)
          })
        })
      } catch (err) {
        console.log(err)
      }
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped>
.main {
  margin-top: 15px;
  padding: 15px 20px 20px;
  border-radius: 4px;
  background-color: #fff;
  border: 1px solid #ebeef5;
  color: #303133;
}
</style>
