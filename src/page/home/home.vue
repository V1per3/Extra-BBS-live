<template>
  <div class="container">
    <Loading :loading="loading"></Loading>
    <div class="left topic-list">
      <TopicList class="topic-list-content" :isRouter="true"></TopicList>
    </div>
    <div class="right">
      <div class="right-list-content">
        <div class="hot-post-card">
          <HotPostCard></HotPostCard>
        </div>
        <div class="copyright">
          <span>©2022</span>
          <a href="#" target="_blank">Extra BBS</a>
          <span>created by</span>
          <a href="#" target="_blank">V1per3</a>
        </div>
      </div>
    </div>
    <div class="center post-list">
      <PostList :postList="postList" :sortBar="true" :noData="noData"></PostList>
      <LoadingBottom :state="hasMore"></LoadingBottom>
    </div>
  </div>
</template>

<script>
import Loading from 'component/loading/loading'
import LoadingBottom from 'component/loading-bottom/loading-bottom'
import TopicList from "component/topic-list/topic-list"
import PostList from "component/post-list/post-list"
import HotPostCard from "component/hot-post-card/hot-post-card"

import { getPostList } from 'api/post'
import util from 'common/js/util'
import { formatTime } from 'common/js/timeformat'

export default {
  name: 'Home',
  data(){
    return {
      pageNum: 1,
      pageSize: 20,
      total: 0,
      loadMoreState: false,
      hasMore: true,
      postList: [],
      sort: 1,
      noData: false,
      loading: true
    }
  },
  components: {
    Loading,
    LoadingBottom,
    HotPostCard,
    TopicList,
    PostList
  },
  watch: {
    $route(to, from){   
      if(from.path == '/' || from.path == '/new-post'){
        this.removeListenScroll() 
        if(to.query.sort){
          this.pageNum = 1
          this.loadMoreState = false
          this.hasMore = true
          this.sort = to.query.sort
          this.noData = false
          this.loading = true
          this.getPostList()
        }
      }
      if(to.path == '/'){
        this.listenScroll() 
      }
    }
  },
  created(){
    this.sort = this.$route.query.sort
    this.getPostList()
  },
  mounted(){
    this.listenScroll()
    this.$bus.$on('newPost', () => {
      this.pageNum = 1
      this.loadMoreState = false
      this.hasMore = true
      this.sort = 1
      this.noData = false
      this.loading = true
      this.getPostList()
    })
  },
  methods: {
    listenScroll(){
      window.addEventListener('scroll', this.handleScroll)
    },
    removeListenScroll(){
      window.removeEventListener('scroll', this.handleScroll)
    },
    handleScroll(){
      let scrollDiff = util.getScrollHeight() - util.getClientHeight() - util.getScrollTop()
      if(scrollDiff < 50){
        this.loadMore()
      }
    },
    getPostList(){
      let data = {
        page_num: this.pageNum,
        page_size: this.pageSize,
        sort: this.sort
      }
      getPostList(data).then(response => {
        if(response.data.status === 200){
          this.total = response.data.message.total
          let list = response.data.message.list
          for(let i=0; i<list.length; i++){
            list[i].create_time = formatTime(list[i].create_time)
            list[i].content = list[i].content.replace(/<[^>]+>/g, "")
          }
          this.postList = list
          //隐藏加载动画
          this.loading = false
          if(this.total < this.pageSize){
            this.hasMore = false
          }
        }else if(response.data.status === 10003){
          this.postList = []
          this.noData = true
          //隐藏加载动画
          this.loading = false
          this.hasMore = false
        }else{
          //不作处理
          //隐藏加载动画
          this.loading = false
          this.hasMore = false
        }
      }).catch(error => {
        console.log('服务器丢失了，请稍后重试！')
      })
    },
    loadMore(){
      //判断当前是否正在加载，避免多次加载
      if(this.loadMoreState){
        return
      }
      //进入加载模式
      this.loadMoreState = true
      this.pageNum ++
      let data = {
        page_num: this.pageNum,
        page_size: this.pageSize,
        sort: this.sort
      }
      getPostList(data).then(response => {
        if(response.data.status === 200){
          let list = response.data.message.list
          for(let i=0; i<list.length; i++){
            list[i].create_time = formatTime(list[i].create_time)
            list[i].content = list[i].content.replace(/<[^>]+>/g, "")
            this.postList.push(list[i])
          }
          this.loadMoreState = false
        }else if(response.data.status === 10003){
          this.hasMore = false
        }else{
          //不作处理
        }
      }).catch(error => {
        console.log('服务器丢失了，请稍后重试！')
      })
    },
    changeSortType(type){
      console.log(type)
    }
  }
}
</script>

<style scoped lang="stylus">
.container {
  position relative
  max-width 1180px
  margin auto

  .left {
    position fixed
    width 250px
    // height 500px
    border-radius 5px
  }

  .center {
    position relative
    margin-left 260px
    margin-right 260px
    // height 800px
    border-radius 5px
  }

  .right {
    position absolute
    top 0
    right 0
    width 250px
    height 600px
    border-radius 5px

    .right-list-content {
      position fixed
      width 250px

      .hot-post-card {
        margin-bottom 10px
      }

      .copyright {
        font-size 12px
        padding 10px
        text-align left
      }

      .record {
        font-size 12px
        padding 0 10px
        margin-top -8px
        text-align left
        
        a {
          color #515151 !important
        }
      }
    }
  }

  @media screen and (max-width: 850px) {
    .left {
      position relative
      width 100vw
    }
    .center {
      margin 10px 0 0
    }
    .right {
      // display none
      position relative
      width 100vw
      height auto
      border-radius 5px
      margin-top 10px

      .right-list-content {
        position relative
        width 100vw

        .link-card {
          display none
        }

        .copyright {
          padding 0 10px
        }
      }
    }
  }
}
</style>