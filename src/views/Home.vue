<template>
  <div class="home">
    <div class="head">
    </div>
    <div class="block main marginTop wow slideInLeft" style="width: 100%">
      <div style="display:inline-block;width:50%" >
          <img src="~@/assets/img/bp.png" v-if= "form.imgU === true" style="width: 100%;height: 100%;"/>
          <img :src="form.imgUrl" v-if="form.imgU === false" style="width: 100%;height: 100%;" />
      </div>
      <div style="display:inline-block; margin-left: 6%; height: 100%;">
        <el-upload
          class="upload-demo"
          drag
          action=""
          :http-request="upload"
          :auto-upload="true"
          multiple>
          <img v-if="isUpload === false" :src="roomImgUrl" style="width: 100%;height: 100%;"/>
          <i v-if="isUpload === true" class="el-icon-upload"></i>
          <div v-if="isUpload === true" class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div>
        </el-upload>
      </div>
      <el-button style="margin-left: 750px;" type="success" @click="submit"> 图像去雾 </el-button>
      <div style="display:inline-block;width:50%" >
          <img src="~@/assets/img/bp.png" v-if= "form.rImgU === true" style="width: 100%;height: 100%;"/>
          <img :src="form.rImgUrl" v-if="form.rImgU === false" style="width: 100%;height: 100%;" />
      </div>
      <el-button style="margin-left: 720px;" type="success" @click="recognized"> 交通标志检测 </el-button>
    </div>
  </div>
</template>
<!-- <script src="https://widget.qweather.net/standard/static/js/he-standard-common.js?v=2.0"></script> -->
<script>
import WOW from 'wowjs';
import BlogList from '../components/BlogList/BlogList.vue'
import TitleBox from '../components/TitleBox/titleBox.vue'
export default {
  name: 'Home',
  components: {
    BlogList,
    TitleBox,
  },
  // mounted () {
  //     // WebSocket
  //   },
  data() {
    return {
      isUpload: true,
      roomImgUrl: '',
      form: {
        imgUrl: '',
        imgU: true,
        rImgUrl: '',
        rImgU: true,
      },
      AllArticle: [],
      AllArticleClassName:[],
      // 头部文章数据
      headerArticle:[],
    }
  },
  methods:{
    submit(){
      let imgUrl = this.roomImgUrl
      this.$http.get('/img/test',{
          params:{
            imgUrl:imgUrl
          }
        })
    },
    recognized(){
      let rImgUrl = this.form.imgUrl
      this.$http.get('/img/recognition',{
          
          params:{
            rImgUrl:rImgUrl
          }
        })
        console.log('这里这里' + data)
    },
    submitUpload() {
      this.$refs.upload.submit();
    },
    //通过onchanne触发方法获得文件列表
    handleChange(file, fileList) {
      this.fileList = fileList;
      console.log(fileList);
    },
    handlePreview(file) {
      console.log(file);
    },
    upload(file) {
      var that = this;
      let fd = new FormData();
      //文件信息中raw才是真的文件
      fd.append("file", file.file);
      this.$http.post(`/img/upload`, fd).then((res) => {
        this.isUpload = false;
        console.log(res.data)
        that.roomImgUrl = res.data;
      });
    },
     initWebSocket () {
        // 连接错误
        this.websocket.onerror = this.setErrorMessage
 
        // 连接成功
        this.websocket.onopen = this.setOnopenMessage
 
        // 收到消息的回调
        this.websocket.onmessage = this.setOnmessageMessage

  
  
        // 连接关闭的回调
        this.websocket.onclose = this.setOncloseMessage
 
        // 监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
        window.onbeforeunload = this.onbeforeunload
      },
      setErrorMessage () {
        console.log('WebSocket连接发生错误   状态码：' + this.websocket.readyState)
      },
      setOnopenMessage () {
        console.log('WebSocket连接成功    状态码：' + this.websocket.readyState)
      },
      setOnmessageMessage (event) {
        // 根据服务器推送的消息做自己的业务处理
        console.log('服务端返回：' + event.data)
        let URL = event.data;
        console.log("URL"+URL)
        
        if(URL.indexOf(',') != -1){
          this.$delete(this.form,'rImgUrl')
          this.$delete(this.form,'rImgU')
          // this.form.rImgU = false;
          this.$set(this.form,'rImgU',false)
          this.$set(this.form,'rImgUrl',event.data.replace(',',''))
          // // this.rImgUrl = event.data.replace(',','');
          // this.form = Object.assign({},this.form,{
          //   rImgUrl: event.data.replace(',',''),
          //   rImgU: false
          // })
          console.log(111)
        }else{
          this.$delete(this.form,'imgUrl')
          this.$delete(this.form,'imgU')
          // this.imgU = false;
          // // this.$set(data,'imgUrl',event.data)
          // this.imgUrl = event.data.replace(',','');
          this.$set(this.form,'imgU',false)
          this.$set(this.form,'imgUrl',event.data)
          // console.log("this.imgUrl"+this.imgUrl)
          //   this.form = Object.assign({},this.form,{
          //   imgUrl: event.data,
          //   imgU: false
          // })
        }

      },
      setOncloseMessage () {
        console.log('WebSocket连接关闭    状态码：' + this.websocket.readyState)
      },
      onbeforeunload () {
        this.closeWebSocket()
      },
      closeWebSocket () {
        this.websocket.close()
      }
  },
  created(){
    // this.GetAllArticleClassName();
    // this.GetAllArticle();

        if ('WebSocket' in window) {
        this.websocket = new WebSocket('ws://localhost:8089/websocket/' + "admin")
        this.initWebSocket()
      } else {
        alert('当前浏览器 Not support websocket')
      }
  },
  mounted(){
      	let wow = new WOW.WOW({
          boxClass: 'wow',
          animateClass: 'animated',
          offset: 0,
          mobile: true,
          live: true
        });
        wow.init();
    },
}
</script>

<style lang="scss" scoped>
.home {
  width: 100%;
}
.headImg {
  width: 100%;
  transform: translateY(-60px);
}


.head {
  display: flex;

  .slideshow {
    margin-right: 10px;
  }
  .block {
    background-color: rgba(255, 255, 255,0);
  }
}

// 轮播图
.slideshowBox :hover .title {
  opacity: 1;
}
.slideshowBox {
  display: flex;
  flex-wrap: wrap;
  position: relative;
  justify-content: center;
  align-content: center;
  cursor:pointer;
  .title {
    font-size: 40px!important;
    padding: 25px;
    color: #fff;
    opacity: 0;
    position: absolute;
    text-align: center;
    top: 60px;
     transition: all 0.5s;
  }
  .slideshow {
    flex: 2;
    border-radius: 5px;
  }
  .block {
    flex: 1;
    padding: 0;
  }
}




// 轮播图旁边的盒子
.block .img1 {
  width: 100%;
  position: relative;
  overflow: hidden;
  border-radius: 5px;
  img {
    width: 100%;
    border-radius: 5px;
    height: 128px;
    transition: all 0.6s;
    cursor:pointer;
  }
  .tit {
    position:absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
    color: #fff;
    width: 100%;
    font-weight: bold;
    font-size: 40px;
    text-align: center;
  }
  img:hover {
    transform: scale(1.2);
  }

}

.main {
  padding: 20px!important;
  background-color: white!important;
}


.BlogList {
  margin-top: 15px;
  .title-box {
    margin-top: 15px;
  }
}

.rightBox {
   margin-top: 15px;
  .el-tag {
    margin: 5px;
  }
}

</style>
