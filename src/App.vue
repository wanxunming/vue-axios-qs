<template>
  <div id="app">
    <el-container style="height: 100%; border: 1px solid #eee">
  <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
    <el-menu :default-openeds="['1', '1']">
    <!-- <el-menu> -->
      <el-submenu index="1">
        <template slot="title"><i class="el-icon-menu"></i>E洁家政服务</template>
        <el-menu-item-group>
          <template slot="title"></template>
          <el-menu-item index="1-1" @click="current='Porducts'">产品管理</el-menu-item>
          <el-menu-item index="1-2" @click="current='Customers'">顾客管理</el-menu-item>
          <el-menu-item index="1-3" @click="current='Comment'">评价管理</el-menu-item>
          <el-menu-item index="1-4" @click="current='Address'">地址管理</el-menu-item>
          <el-menu-item index="1-5" @click="current='Category'">分类管理</el-menu-item>
          <el-menu-item index="1-6" @click="current='Order'">订单管理</el-menu-item>
          <el-menu-item index="1-7" @click="current='Waiter'">服务员管理</el-menu-item>
          <el-menu-item index="1-8" @click="current='Text0'">测试管理</el-menu-item>
        </el-menu-item-group>
      </el-submenu>
    </el-menu>
  </el-aside>
  
  <el-container>
    <el-header style="text-align: right; font-size: 12px">
      <el-dropdown>
        <i class="el-icon-user-solid" style="margin-right: 15px"></i>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>查看</el-dropdown-item>
          <el-dropdown-item>新增</el-dropdown-item>
          <el-dropdown-item>删除</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <span>万循铭</span>
    </el-header>
    
    <el-main>
      <!-- 为什么table数据渲染不上去？ -->
      <component :is="current"></component>
    </el-main>
  </el-container>
</el-container>
  </div>
</template>

<script>

import Porducts from './pages/porducts'
import Customers from './pages/customers'
import Comment from './pages/comment'
import Address from './pages/address'
import Category from './pages/category'
import Order from './pages/order'
import Waiter from './pages/waiter'
import Text0 from './pages/text'

export default {
  name: 'app',
  data(){
    return{
      current:"Text0",
    }
  },
  components: {
    Porducts,
    Customers,
    Comment,
    Address,
    Category,
    Order,
    Waiter,
    Text0,
  },
}
</script>

<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }
  
  .el-aside {
    color: #333;
  }
</style>
