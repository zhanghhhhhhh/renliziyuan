<template>
  <el-container class="container">
    <el-header>
      <div class="left">
        <i class="el-icon-sunrise-1"></i>
        <span>商品后台管理系统</span>
      </div>
      <div class="right">
        <button @click="$router.push('/')">退出</button>
      </div>
    </el-header>
    <el-container>
      <el-aside width="50">
        <el-radio-group v-model="isCollapse">
          <button
            :class="isCollapse ? 'boxShow' : 'boxNoShow'"
            @click="isCollapse = !isCollapse"
          >
            <i :class="isCollapse ? 'el-icon-s-unfold' : 'el-icon-s-fold'"></i>
          </button>
        </el-radio-group>
        <el-menu
          background-color="#33445c"
          text-color="#fff"
          class="el-menu-vertical-demo"
          :collapse="isCollapse"
        >
          <el-submenu
            v-for="(item, index) in RightMenus"
            :key="item.id"
            :index="index + 1 + ''"
          >
            <template #title>
              <i :class="iconFontArr[index]"></i>
              <span> {{ item.authName }}</span>
            </template>
            <el-menu-item
              v-for="(item1, ind) in item.children"
              :key="item1.id"
              :index="index + 1 + '-' + (ind + 1) + ''"
              @click="$router.push({ path: `/${item1.path}` })"
            >
              <i class="el-icon-menu"></i>
              {{ item1.authName }}
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <div v-if="this.$route.path === '/home'">欢迎登陆</div>
        <div v-else>
          <router-view></router-view>
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import { RightMenus } from '@/api/user'
export default {
  async created () {
    const res = await RightMenus()
    this.RightMenus = res.data.data

    console.log()
  },
  data () {
    return {
      RightMenus: [],
      isCollapse: true,
      iconFontArr: ['el-icon-user', 'el-icon-files', 'el-icon-s-goods', 'el-icon-tickets', 'el-icon-data-line']
    }
  },
  methods: {

  },
  computed: {},
  watch: {

  },
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.container {
  height: 525px;
  .el-header {
    background-color: #33445c;
    display: flex;
    justify-content: space-between;
    align-items: center;
    .left {
      width: 300px;
      height: 60px;
      line-height: 60px;
      color: #fff;
      font-size: 22px;
      display: flex;
      .el-icon-sunrise-1 {
        font-size: 60px;
        margin-right: 10px;
      }
    }
    .right {
      width: 70px;
      height: 40px;
      button {
        width: 100%;
        height: 100%;
        color: #71757b;
      }
    }
  }
  .el-aside {
    background-color: #33445c;
    height: 100%;
  }
  .el-main {
    background-color: #eaedf1;
    padding: 20px;
  }

  .el-menu {
    border-right: none;
  }
  .el-menu-vertical-demo:not(.el-menu--collapse) {
    width: 200px;
    min-height: 400px;
  }
  .boxShow {
    width: 65px;
    height: 40px;
    border: 0;
    background-color: rgb(107, 245, 255);
  }
  .boxNoShow {
    width: 200px;
    height: 40px;
    border: 0;
    background-color: rgb(107, 245, 255);
  }
  .el-icon-s-unfold {
    font-size: 20px;
  }
  .el-icon-s-fold {
    font-size: 20px;
  }
}
</style>
