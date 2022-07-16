<template>
  <div class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加商品</el-breadcrumb-item>
    </el-breadcrumb>

    <div class="main">
      <el-alert title="添加商品信息" center show-icon :closable="false">
      </el-alert>
      <el-steps :active="+changeVal" align-center>
        <el-step title="基本信息">
          <el-input-number
            v-model="price"
            controls-position="right"
            @change="changePrice"
            :min="0"
          ></el-input-number>
        </el-step>
        <el-step title="商品参数"></el-step>
        <el-step title="商品属性"></el-step>
        <el-step title="商品图片"></el-step>
        <el-step title="商品内容"></el-step>
        <el-step title="完成"></el-step>
      </el-steps>

      <el-tabs
        v-model="changeVal"
        @tab-click="handleClick"
        tab-position="left"
        :stretch="true"
      >
        <el-tab-pane label="商品信息">
          <el-form label-position="top" :model="addForm">
            <el-form-item label="商品名称">
              <el-input v-model="addForm.name"></el-input>
            </el-form-item>
            <el-form-item label="商品价格">
              <el-input-number
                v-model="addForm.price"
                controls-position="right"
                @change="changePrice"
                :min="0"
              ></el-input-number>
            </el-form-item>
            <el-form-item label="商品重量">
              <el-input-number
                v-model="addForm.weight"
                controls-position="right"
                @change="changeWeight"
                :min="0"
              ></el-input-number>
            </el-form-item>
            <el-form-item label="商品数量">
              <el-input-number
                v-model="addForm.number"
                controls-position="right"
                @change="changeNumber"
                :min="0"
              ></el-input-number>
            </el-form-item>
            <el-form-item label="商品分类">
              <div class="block">
                <span class="demonstration">选择商品分类:</span>
                <el-cascader
                  v-model="value"
                  :options="selectList"
                  :props="{ label: 'cat_name', value: 'cat_id' }"
                ></el-cascader>
              </div>
            </el-form-item>
          </el-form>
        </el-tab-pane>
        <el-tab-pane @click="goodsParams" label="商品参数"
          >配置管理</el-tab-pane
        >
        <el-tab-pane label="商品属性">
          <el-form label-position="top" :model="paramsData">
            <el-form-item label="名称">
              <el-input v-model="paramsData.attr_vals"></el-input>
            </el-form-item>
          </el-form>
        </el-tab-pane>
        <el-tab-pane label="商品图片">
          <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :file-list="fileList"
            list-type="picture"
          >
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">
              只能上传jpg/png文件，且不超过500kb
            </div>
          </el-upload>
        </el-tab-pane>
        <el-tab-pane label="商品内容">
          <quill></quill>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
import quill from '@/views/Goods/Add/quill.vue'
import { getParamsList } from '@/api/params'
import { getCategories } from '@/api/categories'

export default {
  created () {
    this.ParamsList()
  },
  data () {
    return {
      changeVal: 0,
      price: 0,
      addForm: {
        name: '',
        list: '',
        price: 0,
        number: 0,
        weight: 0,
        desc: '',
        prcs: [],
        attrs: []
      },
      value: '',
      sel: 'many',
      id: 0, // 商品id用于发送动态或静态的请求
      select: {
        pagenum: 1,
        pagesize: 5
      },
      selectList: [],
      fileList: [],
      paramsData: []

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
    async goodsParams () {
      try {
        const res = await getCategories({ id: this.id, sel: this.sel })
        this.paramsData = res.data.data
        // console.log(this.paramsData)
      } catch (err) {
        console.log(err)
      }
    },
    changePrice (val) {
      this.addForm.price = val
    },
    changeWeight (val) {
      this.addForm.weight = val
    },
    changeNumber (val) {
      this.addForm.number = val
      // console.log(this.addForm)
    },
    // 侧标签页切换事件
    handleClick (tab) {
      if (tab.index === '1') {
        this.sel = 'many'
        this.goodsParams()
        console.log(this.paramsData)
      } else if (tab.index === '2') {
        this.sel = 'only'
        this.goodsParams()
        console.log(this.paramsData)
      }
    },
    // 上传图片触发
    handleRemove (file, fileList) {
      console.log(file, fileList)
    },
    handlePreview (file) {
      console.log(file)
    }
  },
  computed: {},
  watch: {
    value () {
      if (this.value.length === 3) {
        this.id = this.value[this.value.length - 1]
        console.log(this.id)
      } else {
        this.value = 0
      }
    }
  },
  filters: {},
  components: {
    quill
  }
}
</script>

<style scoped lang='less'>
.main {
  margin-top: 15px;
  padding: 20px 20px;
  border-radius: 4px;
  background-color: #fff;
  border: 1px solid #ebeef5;
  color: #303133;
  ::v-deep .el-steps {
    margin: 20px 0;

    .el-steps .is-finish {
      color: #67c23a;
    }
    .is-finish {
      color: #67c23a;
      border-color: #67c23a;
    }
    .el-step__title {
      font-size: 14px;
    }
  }
}
</style>
