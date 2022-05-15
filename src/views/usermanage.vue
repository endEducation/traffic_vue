<template>
  <el-table
    :data="userList"
    style="width: 100%">
    <el-table-column
      label="注册时间"
      width="180">
      <template slot-scope="scope">
        <i class="el-icon-time"></i>
        <span style="margin-left: 10px">{{ scope.row.createTime|formatTimer }}</span>
      </template>
    </el-table-column>
	<el-table-column
	  label="性别"
	  width="100">
	  <template slot-scope="scope">    
	    <span v-if="scope.row.gender === '1'" style="margin-left: 10px"><i class="el-icon-female"></i>女</span>
		<span v-else style="margin-left: 10px"><i class="el-icon-male"></i>男</span>
	  </template>
	</el-table-column>
    <el-table-column
      label="昵称"
	  width="100">
      <template slot-scope="scope">
        <el-popover trigger="hover" placement="top">
          <p>电话: {{ scope.row.phone }}</p>
          <p>邮箱: {{ scope.row.email }}</p>
          <div slot="reference" class="name-wrapper">
			<span>{{scope.row.nickname}}</span>
          </div>
        </el-popover>
      </template>
    </el-table-column>
	<el-table-column
	label="是否为管理员"
	width="130">
	<template slot-scope="scope">
		<el-tag size="medium" v-if="scope.row.isAdmin === 0">否</el-tag>
		<el-tag size="medium" v-else>是</el-tag>
	</template>
	</el-table-column>
    <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button
          size="mini"
		  type="warning"
          @click="resetPass(scope.$index, scope.row)">重置密码</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)">删除</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
	export default {
	    data() {
	      return {
	        userList: []
	      }
	    },
		filters: {
			formatTimer: function(value) {
			  let date = new Date(value);
			  let y = date.getFullYear();
			  let MM = date.getMonth() + 1;
			  MM = MM < 10 ? "0" + MM : MM;
			  let d = date.getDate();
			  d = d < 10 ? "0" + d : d;
			  let h = date.getHours();
			  h = h < 10 ? "0" + h : h;
			  let m = date.getMinutes();
			  m = m < 10 ? "0" + m : m;
			  let s = date.getSeconds();
			  s = s < 10 ? "0" + s : s;
			  return y + "-" + MM + "-" + d + " " + h + ":" + m;
			}
		 },
	    methods: {
	      handleDelete(index, row) {
	        console.log(index, row);
			this.$confirm("此操作将永久删除用户，确认删除吗？","提示",{
				confirmButtonText: '确定',
				cancelButtonText: '取消',
				type: 'warning'
			}).then(() => {
				this.$http.delete('/user/deleteUser/'+row.id)
				.then(res => {
					if(res.data.code === 200) {
						this.$message({
						  type: 'success',
						  message: res.data.msg,
						  offset: 80
						})
						this.getUserData();
					} else {
						this.$message({
						  type: 'error',
						  message: res.data.msg,
						  offset: 80
						})
					}
				})
			}).catch(() => {
				  this.$message({
					type: 'info',
					message: '已取消删除',
					offset: 80
				}); 				
			})
	      },
		  getUserData() {
			  this.$http.get('/user/getAllUser')
			  .then(res => {
				  console.log(res);
				  if(res.data.code === 200) {
					  this.userList = res.data.data
				  }
			  })
		  },
		  resetPass(index, row) {
			  this.$confirm("此操作将密码重置为123456，确认重置吗？","提示",{
				 confirmButtonText: '确定',
				 cancelButtonText: '取消',
				 type: 'warning'
			  }).then(() => {
				 this.$http.post('/user/resetPass',{
				 				  "nickname":row.nickname
				 }).then(res => {
				  if(res.data.code === 200) {
					  this.$message({
						  type:"success",
						  message: res.data.msg,
						  offset: 80
					  })
					  setTimeout(this.getUserData(), 500)
				  } else {
					  this.$message({
						  type:"error",
						  message: res.data.msg,
						  offset: 80
					  })
				  }
				 }) 
			  }).catch(() => {
				  this.$message({
					type: 'info',
					message: '已取消',
					offset: 80
				});     
			  });
		  }
	    },
		created() {
			this.getUserData();
		}
	  }
</script>

<style>
	
</style>
