<template>
    <div class="container">
       <div class="video-main mdl-shadow--2dp">
           <div class="video-player">
               <div class="video-playing">
                 <video controls poster="{{ currentVideo ? currentVideo.coverForFeed : '#' }}">
                   <source  src="{{ currentVideo ? currentVideo.playUrl : '#' }}" >
               </video>
               </div>
               <div class="video-info">
                   <h1>{{ currentVideo ?  currentVideo.title : 'title' }}</h1>
                   <p>{{ currentVideo ?  currentVideo.description : 'des' }}</p>
                   <div>{{ currentVideo ? currentVideo.category: 'TAG' }}</div>
               </div>
           </div>
           <div class="video-list">
               <ul class="video-ul">
                   <li v-for="video in videoList">
                       <div class="title">
                         <span>{{ video.title }}</span>
                       </div>
                       <a href="#" v-on:click="changeCurrentVideo($index)">
                         <img src="{{ video.coverForFeed }}" alt="img">
                      </a>
                   </li>
               </ul>
           </div>
       </div>
   </div>
</template>

<script>
var Vue = require('vue')
var vueResource = require('vue-resource')
Vue.use(vueResource)
export default{
  data () {
    return {
      url: 'http://baobab.wandoujia.com/api/v1/feed',
      nextPageUrl: '',
      videoList: [],
      currentVideo: null,
      date: 0
    }
  },
  ready () {
    this.getData(this.url)
  },
  methods: {
    getData (url) {
      this.$http.get(url).then((response) => {
        var json = JSON.parse(response.body)
        this.nextPageUrl = json.nextPageUrl
        var list = json.dailyList
        if (list.length > 0) {
          this.date = list[0].date
          var videoList = list[0].videoList
          if (videoList.length > 0) {
            this.currentVideo = videoList[0]
          }
          for (var i = 0; i < videoList.length; i++) {
            this.videoList.push(videoList[i])
          }
        }
      }, (error) => {
        console.error(error)
      })
    },
    changeCurrentVideo (position) {
      if (this.videoList) {
        this.currentVideo = this.videoList[position]
      }
    }
  }
}

</script>

<style>

    * {
        margin: 0;
        padding: 0;
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
    }
    ::-webkit-scrollbar {
        width: 6px;
        height: 6px;
        background-color: transparent;
    }

    ::-webkit-scrollbar-thumb {
        border-radius: 3px;
        -moz-border-radius: 3px;
        -webkit-border-radius: 3px;
        background-color: rgba(189, 189, 189, 0.4);
    }
    .mdl-shadow--2dp {
        -webkit-box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.2), 0 1px 5px 0 rgba(0,0,0,0.12);
        box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 3px 1px -2px rgba(0,0,0,0.2), 0 1px 5px 0 rgba(0,0,0,0.12);
    }
    html {
        height: 100%;
        width: 100%;
        overflow: hidden;
    }

    body {
        font-size: 12px;
        font-family: "Microsoft Yahei", "Helvetica Neue", Helvetica, Arial, sans-serif;
        -moz-osx-font-smoothing: grayscale;
        -webkit-font-smoothing: antialiased;
        text-rendering: optimizeLegibility;
        background-color: #EEEEEE;
        height: 100%;
        width: 100%;
        overflow: hidden;
    }
    .container{
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        height: 100%;
        width: 100%;
    }
    .video-main{
        background: black;
        height: 80%;
        width: 80%;
        display: flex;
        justify-content: space-between;
    }
    .video-player{
        display: flex;
        width: 77%;
        flex-direction: column;
        align-self: stretch;
    }
    .video-playing{
        background: black;
        /*flex:5;*/
        align-items: flex-start;
        display: flex;
    }
    .video-playing>video {
      width: 100%;
    }
    .video-info{
        background: white;
        flex:2;
    }
    .video-ul{
        height: 100%;
        display: flex;
        flex-direction: column;
        overflow: hidden;
        overflow-y: scroll;
    }
    .video-ul>li>a>img{
        height: 140px;
    }
    .video-ul>li{
        position: relative;
        bottom: 0;
    }
    .title {
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
    }
    .title>span {
        color: white;
        display: block;
        background: rgba(0,0,0,0.5);
        text-decoration: none;
        padding: 1em;
        font-size: 1em;
    }
</style>
