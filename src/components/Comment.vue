<template>
  <div>
    <div class="comment-box">
      <div v-if="isSignIn === 0" class="signInText"><span class="login" @click="GotoLogin">登录</span>留言吧</div>
        <div class="bottom">
          <div class="dec">
              <span>共<span class="commentTotal">{{commentArray.length}}</span>条留言</span>
          </div>
          <div class="CommentInput">
              <img  :src="imageUrl?imageUrl:defaultAvatar"  alt="">
              <el-input
              type="textarea"
              :rows="3"
              placeholder="请输入内容并按回车键发送"
              v-model="textarea"
              class="inputbox"
              size="350"
              maxlength="350"
              resize="none"
              ref="content"
              @keyup.13.native="SendComment(textarea)">
              </el-input>

          </div>
          <!-- 评论区 -->
          <ul class="Commentarea">
              <h3>热门留言</h3>
              <li :key="index" v-for="(item,index) in commentArray">
                  <div class="Commentareabox">
                      <div class="pic">
                          <img :src="item.avatar" alt="">
                      </div>
                      <div class="side">
                          <div class="CommentTitle">
                              <span class="nickname">{{item.nickname}}:</span>
                              <span class="nickname" v-if="item.replyName!=null">@{{item.replyName}}  </span>
                              <span class="cmcontent">{{item.content}}</span>
                          </div>
                          <div class="timerorlike">
                              <span class="timer">{{item.update_time}}</span>
                              <span  @click="clickLike($event)"><i class="iconfont  My-new-icondianzan"></i>{{likeNum}}</span>
                              <span class="vertical">|</span>
                              <span @click="getFocus(item.user_id,$event)" style="cursor: pointer">回复</span>
                              <span class="delete" v-if="isAdmin !== '0'"  @click="handleDelect(item.id)">删除</span>
                          </div>
                      </div>
                  </div>
              </li>
          </ul>
        </div>
    </div>
  </div>
</template>

<script>
import Cookie from 'js-cookie'
  export default {
    inject: ['reload'],
    props:['articleId'],
    data() {
      return {
        commentArray:[],
        textarea:'',
        replyId: -1,
        imageUrl:sessionStorage.getItem("avatar"),
        defaultAvatar:require("@/assets/img/pl.jpg"),
		likeNum: 999,
        isAdmin:sessionStorage.getItem("isAdmin"),
      }
    },
    methods:{
      getFocus(user_id){
        this.replyId = user_id
        this.$refs.content.focus()
      },
	  // 点赞事件
	  clickLike() {
		  
	  },
     handleDelect(id) {
        this.$confirm('此操作将永久删除该评论, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            // 发起删除的网络请求
                    this.$http.post('/messageBoard/deleteMessageById',{
						Did:id,
                    })
                    .then(res => {
                        if(res.data.code === 200){
                            //发起删除请求操作
                            this.$message({
                                type: 'success',
                                message: `评论删除成功!`,
								offset: 80
                            });
                            setTimeout(() => {
                                this.reload()
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

      GotoLogin(){
        this.$router.push({name:'login'})
      },
      // 获取网站文章评论
      GetArticleComment(){
		// this.articleId 代表当前文章id
        if(this.articleId){
          this.$http.get('/messageBoard/findMessageById/'+this.articleId)
          .then(res => {
            // 将得到的数组进行反转再保存
            this.commentArray = res.data.data.reverse();
          })
        }else {
          // 如果当前没有文章id,则属于留言 ，则把文章id赋值为0再发送请求
           this.$http.get('/messageBoard/findAllMessage')
          .then(res => {
			  console.log(res)
            // 将得到的数组进行反转再保存
            this.commentArray = res.data.data.reverse();
          })
        }
      },
      // 发送评论
      SendComment(textarea){
		 if(textarea === '') return
         if(Cookie.get('token')){
            // 发起评论请求
            this.$http.post('/messageBoard/insertMessage',{
              content:textarea,

              // 有则发送文章id，无则发送0
              disease_id:this.articleId ? this.articleId:-1,
              reply_id:this.replyId
            }).then(res => {
              this.GetArticleComment();
              this.textarea = ''
              this.replyId=-1
            })
          }else {
            this.$message({
                message: '请登录后进行操作',
                type: 'warning'
              });
              this.$router.push({name:'login'})
          }
      }
    },
    computed:{
      // 查询登录状态
      isSignIn(){
        return this.$store.state.isSignIn;
      },


    },
    created(){
      this.GetArticleComment();
    }
  }
</script>

<style lang="scss" scoped>
.top {
    overflow: hidden;
    position: relative;
    margin-bottom: 85px;
    .top-left,
    .top-right {
        float: left;
    }
    .top-left {
        margin-right:25px;
        img {
            width: 130px;
        }
    }
    .top-right {
       div {
           margin: 15px 0;
       }
       .title {
           font-size: 25px;
           font-weight: 400;
       }
       
    }
    .top-right-btn {
          position: absolute;
          bottom: 1px;
          right: 0px;
    }
}
.bottom {
    .dec {
        margin-bottom: 25px;
    }
    .CommentInput {
      display: flex;
        margin-bottom: 55px;
        img {
            width: 75px;
            height: 75px;
            margin: 0 15px;
            border-radius: 5px;
        }
    }
}

.commentTotal {
  color: red;
}
.Commentarea {
    li {
        font-size: 14px;
        margin: 10px 0;
        width: 100%;
        border-bottom: 1px dashed  rgb(204, 204, 204);
        .Commentareabox {
            padding: 15px 0;
            overflow: hidden;
            display: flex;
            .pic,
            .side {
                .nickname {
                    color: skyblue;
                }              
                .timerorlike {
                    margin-top: 15px;
                    .timer {
                        color: #999;
                    }
                    i {
                      margin-right: 8px;
                    }
                    .vertical {
                        margin: 0 10px;
                    }
                    .delete {
                      margin-right: 10px;
                      color: skyblue;
                      cursor:pointer
                    }
                }
            }
            .pic {
                img {
                    width: 50px;
                    margin-right: 10px;
                }
            }

             .side {
                width: 93%;
                .timerorlike {
                     .timer {
                    float: left;
                }
                span {
                    float: right;
                }
                }
            }
        }
    }
}
.My-new-icondianzan {
      // 鼠标小手
  cursor:pointer;
}

.signInText {
   margin-bottom: 10px;
   .login {
    // 鼠标小手
    cursor:pointer;
    color: skyblue;
  }
}

</style>