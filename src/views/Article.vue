<template>
    <div class="wrapper">
        <div class="article">
            <el-button class="addBtn" @click="handleAdd">新增+</el-button>
            <el-table :data="articleList" border stripe>
                <el-table-column
                    prop="diseaseName"
                    label="标题"
                    width="180">
                </el-table-column>
                <el-table-column
                    label="日期"
                    width="180">
                    <template slot-scope="scope">
                        <i class="el-icon-time"></i>
                        <span>{{ scope.row.createTime | formatTimer}}</span>
                    </template>
                </el-table-column>
                <el-table-column
                    label="操作">
                    <template slot-scope="scope">
                        <el-button size="mini" type="primary" @click="handleLook(scope.row)">查看</el-button>
                        <el-button size="mini" type="success" @click="handleEdit(scope.row)">编辑</el-button>
                        <el-button size="mini" type="danger" @click="handleDelect(scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                articleList:[]
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
        methods:{
            handleAdd() {
                this.$router.push({name:'editArticle'})
            },
            handleLook(row) {
                let id = row.id
                this.$router.push({path:'/detail/'+id})
            },
            handleEdit(row) {
                let id = row.id;
                this.$router.push({name:'editArticle',params: {id:id}})
            },
            handleDelect(row){
                let id = row.id
                this.$confirm('此操作将删除该文章, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                  this.$message({
                    type: 'success',
                    message: `文章删除成功!`,
					offset: 80
                  });
                    // 发起删除的网络请求
                    this.$http.post('/disease/deleteDisease',{
                        disease_id: id
                    })
                    .then(res => {
                        if(res.data.code === 200){
                            //发起删除请求操作
                            this.$message({
                                type: 'success',
                                message: `${row.diseaseName}文章删除成功!`
                            });
                            setTimeout(() => {
                                location.reload()
                            }, 500);  
                        }
                    }).catch(e=>{
                        console.log(e)
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });          
                });
            },
            getMyBlogList(){
			   this.$http.get('/disease/findAllDisease').then(res => {
				   if (res.data.code === 200) {
					   this.articleList = res.data.data
					   this.$message({
						   type:'success',
						   message:res.data.msg,
						   offset:80
					   })
				   }				   
			   })
            }
        },
        created() {
            this.getMyBlogList()
        },
    }
</script>

<style lang="scss" scoped>
.title {
    margin: 30px 0;
    text-align: center;
    font-weight: bold;
    font-size: 28px;
}
.article {
    .addBtn {
        float: right;
        margin-bottom: 20px;
    }
}
/deep/ .el-table {
    .cell {
        text-align: center;
    }
}
</style>