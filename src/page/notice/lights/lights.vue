<template>
  <div class="container">
    <h1>{{msg}}</h1>
    <div class="comment-list">
      <router-link to="/post/帖子id" v-for="item in commentList" :key="item._id">
        <div class="comment" v-if="!item.comment_id">
          <div class="header">
            <span class="text">你&nbsp;</span>
            <span class="datetime">{{item.datetime}}</span>
            <span class="text">&nbsp;在&nbsp;</span>
            <span class="title">{{item.title}}</span>
            <span class="text">&nbsp;中的回复被亮了&nbsp;</span>
            <span class="lights">{{item.lights}}</span>
            <span class="text">&nbsp;次</span>
          </div>
          <div class="content">{{item.content}}</div>
          <!-- <div class="post">
            <span class="text">来自：</span>
            <span class="title">{{item.title}}</span>
          </div> -->
        </div>
        <div class="comment" v-if="item.comment_id">
          <div class="header">
            <span class="text">你&nbsp;</span>
            <span class="datetime">{{item.datetime}}</span>
            <span class="text">&nbsp;在&nbsp;</span>
            <span class="title">{{item.title}}</span>
            <span class="text">&nbsp;中的回复被亮了&nbsp;</span>
            <span class="lights">{{item.lights}}</span>
            <span class="text">&nbsp;次</span>
          </div>
          <div class="quote">
            <div class="header">
              <img :src="item.commentor_avatar" alt="">
              <span class="uname">{{item.commentor_name}}</span>
            </div>
            <div class="content">{{item.comment_content}}</div>
          </div>
          <div class="content">{{item.content}}</div>
          <!-- <div class="post">
            <span class="text">来自：</span>
            <span class="title">{{item.title}}</span>
          </div> -->
        </div>
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      msg: "亮了通知的组件",
      commentList: [
       
      ]
    };
  }
};
</script>

<style scoped lang="stylus">
.container {
  border-radius 5px

  .comment {
    padding 10px 20px 5px 20px
    margin 5px 0
    border-radius 5px
    background-color #ffffff
    text-align left

    &:hover {
      cursor pointer
    }

    .header {
      display flex
      flex-direction row
      align-items center

      .text{
        color #555
        font-size 14px
      }

      .datetime {
        font-size 14px
        color #999999
      }

      .title{
        font-size 14px
        color #009688
      }

      .lights{
        font-size 14px
        color #009688
      }
    }

    .quote{
      border-radius 5px
      background-color #ececec
      margin-top 5px

      .header{
        display flex
        flex-direction row
        align-items center
        justify-content left 
        padding 10px
        
        img{
          width 20px
          height 20px 
          border-radius 50%
        }

        .uname{
          font-size 12px
          color #666
          margin-left 5px
        }
      }

      .content{
        padding 0 10px 10px 10px
        font-size 14px
      }
    }

    .content {
      padding 10px 0
      color #171717
      font-size 14px
    }
  }
}
</style>
