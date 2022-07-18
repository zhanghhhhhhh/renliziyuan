<template>
  <div>
    <!-- Form -->
    <el-button type="text" @click="onSubmit">打开嵌套表单的 Dialog</el-button>

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
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { getRightsList } from '@/api/rights'

export default {
  created () {
  },
  data () {
    return {
      dialogFormVisible: false,
      data1: [],

      defaultProps: {
        children: 'children',
        label: 'authName'
      }
    }
  },
  methods: {
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
</style>
