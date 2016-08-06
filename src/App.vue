<template>
  <div class="container">
    <div class="video-main mdl-shadow--2dp">
      <div class="video-player">
        <div class="video-playing">
          <video id="videoPlayer" style="background-image: url({{ currentVideo.coverForFeed }});" controls poster="1.jpg">
            <source id="videoSource" src="" >
          </video>
        </div>
        <div class="video-info">
          <h1>{{ currentVideo.title }}</h1>
          <p>{{ currentVideo.description}}</p>
      </div>
      </div>
      <div class="video-list">
        <div class="page-control">
          <button class="page-button" v-on:click="prePage">上一页</button>
          <button class="page-button" v-on:click="nextPage">下一页</button>
        </div>
        <ul class="video-ul">
          <li v-for="video in videoList">
            <div class="title">
              <span>{{ video.title }}</span>
            </div>
            <a href="#" v-on:click="changeCurrentVideo($index)">
              <img :src="video.coverForFeed" alt="img">
            </a>
          </li>
        </ul>
      </div>
    </div>
  </div>
  <a href="https://github.com/hanks-zyh/eye-video"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://camo.githubusercontent.com/38ef81f8aca64bb9a64448d0d70f1308ef5341ab/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f6461726b626c75655f3132313632312e706e67" alt="Fork me on GitHub" data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_darkblue_121621.png"></a>
</template>

<script>
var Vue = require('vue')
var vueResource = require('vue-resource')
Vue.use(vueResource)
export default{
  data () {
    return {
      url: 'http://baobab.wandoujia.com/api/v1/feed',
      videoList: [],
      currentVideo: null,
      date: 0,
      nextPageUrl: '',
      pageList: []
    }
  },
  ready () {
    this.pageList.push(this.url)
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
            this.videoList.length = 0
            this.currentVideo = videoList[0]
            this.reloadVideoSource(this.currentVideo.playUrl)
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
        this.reloadVideoSource(this.currentVideo.playUrl)
      }
    },
    prePage () {
      if (this.pageList.length > 0) {
        this.pageList.pop()
        var prePageUrl = this.pageList[this.pageList.length - 1]
        this.getData(prePageUrl)
      }
    },
    nextPage () {
      if (this.nextPageUrl) {
        this.pageList.push(this.nextPageUrl)
        this.getData(this.nextPageUrl)
      }
    },
    reloadVideoSource (playUrl) {
      var player = document.getElementById('videoPlayer')
      var videoSource = document.getElementById('videoSource')
      player.pause()
      videoSource.src = playUrl
      player.load()
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
        height: 85%;
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
    .video-list{
        display: flex;
        flex-direction: column;
    }
    .page-control{
        height: 40px;
        display: flex;
        justify-content: space-between;
        align-items: stretch;
    }
    button.page-button {
        flex: 1;
        background: black;
        color: white;
        font-size: 1.2em;
        border: none;
        border-right: 1px solid #444;
        -webkit-transition: background 0.2s;
        -moz-transition: background 0.2s;
        -ms-transition: background 0.2s;
        transition: background 0.2s;
    }
    button.page-button:hover {
        background: #353535;
        cursor: pointer;
    }
    .video-info>h1 {
        margin: 16px 10px;
    }
    .video-info>p {
        margin: 0 10px;
        line-height: 1.5em;
        letter-spacing: 0.1em;
        font-size: 1.2em;
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

    video{
      background:transparent 50% 50% no-repeat;
      -webkit-background-size:cover;
      -moz-background-size:cover;
      -o-background-size:cover;
      background-size:cover;
    }
</style>
