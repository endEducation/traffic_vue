<template>
	<el-row class="tac">
	  <el-col :span="4">
	    <el-menu
	      :default-active="activeIndex"
	      class="el-menu-vertical-demo"
	      @open="handleOpen"
	      @close="handleClose">
		  <el-menu-item index="1" id="currentPage">	  	
		    <router-link to="/article"><i class="el-icon-location"></i>文章管理</router-link>
		  </el-menu-item>
	      <el-menu-item index="2">        
	        <router-link to="/usermanage"><i class="el-icon-menu"></i>用户管理</router-link>
	      </el-menu-item>
	    </el-menu>
		
	  </el-col>
	  <el-col :span="20">
		  <router-view></router-view>
	  </el-col>
	</el-row>
</template>

<script>
	export default {
		data() {
			return {
				activeIndex:'1',
			}
		},
		methods: {
		  handleOpen(key, keyPath) {
			console.log(key, keyPath);
		  },
		  handleClose(key, keyPath) {
			console.log(key, keyPath);
		  }
		}
	}
</script>

<style>
</style>
