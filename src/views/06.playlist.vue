<template>
  <div class="playlist-container">
    <div class="top-wrap">
      <div class="img-wrap">
        <!-- 封面 -->
        <img :src="playlist.coverImgUrl" alt="" />
      </div>
      <div class="info-wrap">
        <p class="title">{{ playlist.name }}</p>
        <div class="author-wrap">
          <img class="avatar" :src="playlist.creator.avatarUrl" alt="" />
          <span class="name">{{ playlist.creator.nickname }}</span>
          <span class="time">{{ playlist.createTime }}</span>
        </div>
        <div class="play-wrap">
          <span class="iconfont icon-circle-play"></span>
          <span class="text">播放全部</span>
        </div>
        <div class="tag-wrap">
          <span class="title">标签:</span>
          <!-- 分类标签 -->
          <ul>
            <li v-for="(item,index) in playlist.tags" :key="index" >{{ item }}</li>
          </ul>
        </div>
        <div class="desc-wrap">
          <span class="title">简介:</span>
          <!-- 简介 -->
          <span class="desc">
            {{ playlist.description }}
          </span>
        </div>
      </div>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲列表" name="1">
        <table class="el-table playlit-table">
          <thead>
            <th></th>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>
            <tr class="el-table__row" v-for="(item, index) in playlist.tracks" :key="index">
              <td>{{ index + 1 }}</td>
              <td>
                <div class="img-wrap" @click="playMusic(item.id)">
                  <img :src="item.al.picUrl+'?param=130y130'" alt="" />
                  <span class="iconfont icon-play"></span>
                </div>
              </td>
              <td>
                <div class="song-wrap" @dblclick="playMusic(item.id)">
                  <div class="name-wrap" >
                    <span>{{ item.name }}</span>
                    <span v-if="item.mv !== 0" class="iconfont icon-mv" @click="toMV(item.id)"></span>
                  </div>
                  <span v-if="item.alia.length!=0">{{ item.alia[0] }}</span>
                </div>
              </td>
              <td>{{ item.ar[0].name }}</td>
              <td>{{ item.al.name }}</td>
              <td>{{ item.dt | playTimeFormat }}</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane label="评论" name="2">
        <!-- 精彩评论 -->
        <div class="comment-wrap">
          <p class="title">精彩评论
            <!-- <span class="number" v-if="this.hotCount!=0" >{{ hotCount }}</span> -->
            </p>
          <div class="comments-wrap">
            <div v-for="(item,index) in hotComment" :key="index" class="item">
              <div class="icon-wrap">
                <!-- 头像 -->
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <!-- 昵称 -->
                  <span class="name">{{ item.user.nickname }}：</span>
                  <span class="comment">{{ item.content }}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{ item.beRepiled[0].user.nickname }}</span>
                  <span class="comment">{{ item.beRepiled[0].content }}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>
        </div>
        <!-- 最新评论 -->
        <div class="comment-wrap">
          <p class="title">最新评论<span class="number">({{ total }})</span></p>
          <div class="comments-wrap">
            <div v-for="(item,index) in comments" :key="index" class="item">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{ item.user.nickname }}：</span>
                  <span class="comment">{{ item.content }}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{ item.beReplied[0].user.nickname }}：</span>
                  <span class="comment">{{ item.beReplied[0].content }}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>
        </div>
        <!-- 分页器 -->
        <el-pagination
          @current-change="handleCurrentChange"
          background
          layout="prev, pager, next"
          :total="total"
          :current-page="page"
          :page-size="5"
        >
        </el-pagination>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
//导入axios
import axios from 'axios'
export default {
  name: 'playlist',
  data() {
    return {
      activeIndex: '1',
      // 总条数
      total: 0,
      // 页码
      page: 1,
      //歌单详情
      playlist:[],
      //热门评论
      hotComment:[],
      //热门评论的个数
      hotCount:0,
      //普通评论
      comments:[],
      //歌曲
      songList:[],
    };
  },
  created(){
    //获取歌单详情
    axios({
      url:'https://autumnfish.cn/playlist/detail',
      method:'get',
      params:{
        id:this.$route.query.q
      }

    }).then(res=>{
      console.log(res)
      this.playlist = res.data.playlist
      this.songList = res.data.playlist.tracks
    })
    //获取歌单的歌曲列表
    // axios({
    //   url:''
    // })
     //获取 评论
    axios({
      url:'https://autumnfish.cn/comment/hot',
      method:'get',
      params:{
        id:this.$route.query.q,
        //传递类型
        type:2
      }
    }).then(res=>{
      // console.log(res)
      this.hotComment = res.data.hotComments
      //保存个数
      this.hotCount = res.data.total
    })

    //获取 最新评论
    axios({
      url:'https://autumnfish.cn/comment/playlist',
      method:'get',
      params:{
        id:this.$route.query.q,
        limit:10,
        offset:0
      }
    }).then(res=>{
      console.log(res)
      //总个数
      this.total = res.data.total
      //评论数据
      this.comments = res.data.comments
    })
  },
   
  methods: {
    //播放
    playMusic(id){
      // console.log(id)
      axios({
          url: 'https://autumnfish.cn/song/url',
          method: 'get',
          params: {
            id // id:id
          }
        }).then(res => {
          // console.log(res)
          let url = res.data.data[0].url
          // console.log(this.$parent.musicUrl)
          // 设置给父组件的 播放地址
          this.$parent.musicUrl = url
        })
    },
    //去mv详情页
    toMV(id){
      console.log('yes')
      this.$router.push(`/mv?q=${id}`)
    },
    handleCurrentChange(val) {
      // console.log(`当前页: ${val}`);
      // 保存页码
      this.page = val
      // 重新获取数据
      axios({
      url:'https://autumnfish.cn/comment/playlist',
      method:'get',
      params:{
        id:this.$route.query.q,
        limit:10,
        //根据页码计算
        offset:(this.page-1)*10
      }
    }).then(res=>{
      console.log(res)
      //总个数
      this.total = res.data.total
      //评论数据
      this.comments = res.data.comments
    })
    }
  }
};
</script>

<style >

</style>
