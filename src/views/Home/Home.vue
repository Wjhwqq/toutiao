<template>
  <div class="home-container">
    <!-- 头部区域 -->
      <van-nav-bar title="头条" fixed/>
    <!-- 导入 注册 并使用ArticleInfo组件  在使用自定义组件中 如果某个属性名是小驼峰 eg:cmtCount 绑定时官方建议写成cmt-count 连字符格式-->
    <van-pull-refresh v-model="isloading" @refresh="onRefresh" :disabled="finished">
        <van-list
          v-model="loading"
          :finished="finished"
          finished-text="没有更多了"
          @load="onLoad"
        >
          <ArticleInfo 
          v-for="item in artlist" 
          :key="item.id" 
          :title="item.title"
          :author="item.aut_name"
          :cmt-count="item.comm_count"
          :date="item.pubdate"
          :cover="item.cover"
        ></ArticleInfo>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import ArticleInfo from '@/components/Article/ArticleInfo.vue'
import request from '@/utils/request.js'
export default {
  name:'Home',
  components:{ArticleInfo},
  data() {
    return {
      page:1,
      limit:10,
      artlist:[],
      // 是否正在加载下一页数据
      loading:true,
      // 所有数据是否加载完毕
      finished:false,
      isloading:false
    }
  },
  created(){
    this.initArticleList()
  },
  methods:{
    // 封装获取文章列表数据的方法
    async initArticleList(isRefresh){
      const{data:res}= await request.get('/articles',{
        params:{_page:this.page,_limit:this.limit}
      })
      if(isRefresh){
        this.artlist=[...res,...this.artlist]
        this.isloading=false
      }else{
        this.artlist=[...this.artlist,...res]
        console.log(res);
        this.loading=false
        
      }
      if(res.length==0){
          this.finished=true
        }
    },
    // 请求数据
    onLoad(){
      this.page+=1
      this.limit+=10
      this.initArticleList()
      
    },
    onRefresh(){
      this.page++
      this.limit+=10
      this.initArticleList(true)
      
    }
  }
}
</script>

<style lang="less" scoped>
  .home-container{
    padding:46px 0 50px 0;
    .van-nav-bar{
      background-color:#007bff;
    }
    /deep/ .van-nav-bar__title{
      color:white;
    }
  }
</style>