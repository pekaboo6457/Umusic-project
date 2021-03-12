<template>
  <div class="mv-container">
    <div class="mv-wrap">
      <h3 class="title">mv详情</h3>
      <!-- mv -->
      <div class="video-wrap">
        <video
          controls
          :src="url"
        ></video>
      </div>
      <!-- mv信息 -->
      <div class="info-wrap">
        <div class="singer-info">
          <div class="avatar-wrap">
            <!-- 头像 -->
            <img :src="icon" alt="" />
          </div>
          <!-- 歌手名 -->
          <span class="name">{{ mvInfo.artistName }}</span>
        </div>
        <div class="mv-info">
          <!-- 标题 -->
          <h2 class="title">{{ mvInfo.name }}</h2>
          <!-- 发布时间 -->
          <span class="date">发布：{{ mvInfo.publishTime }}</span>
          <!-- 播放次数 -->
          <span class="number">播放：{{ mvInfo.playCount }}</span>
          <!-- 描述 -->
          <p class="desc">
            {{ mvInfo.desc }}
          </p>
        </div>
      </div>
      <!-- 精彩评论 -->
      <div class="comment-wrap" v-if="hotComments !== undefined && hotComments.length !== 0">
        <p class="title">精彩评论<span class="number">({{ hotComments.length }})</span></p>
        <div class="comments-wrap">
          <div class="item" v-for="(item, index) in hotComments" :key="index">
            <div class="icon-wrap">
              <img :src="item.user.avatarUrl+'?param=50y50'" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">{{ item.user.nickname }}：</span>
                <span class="comment">：{{ item.content }}</span>
              </div>
              <!-- 回复 -->
              <div class="re-content" v-if="item.beReplied.length !== 0">
                <span class="name">{{ item.beReplied[0].user.nickname }}</span>
                <span class="comment">：{{ item.beReplied[0].content }}</span>
              </div>
              <div class="date">{{ item.time | dateFormat }}</div>
            </div>
          </div>
        </div>
      </div>
      <!-- 最新评论 -->
      <div class="comment-wrap">
        <p class="title">最新评论<span class="number">({{ total }})</span></p>
        <div class="comments-wrap">
          <div class="item" v-for="(item, index) in comments" :key="index">
            <div class="icon-wrap">
              <img :src="item.user.avatarUrl+'?param=50y50'" alt="" />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">{{ item.user.nickname }}：</span>
                <span class="comment">{{ item.content }}</span>
              </div>
              <div class="re-content" v-if="item.beReplied.length !== 0">
                <span class="name">{{ item.beReplied[0].user.nickname }}：</span>
                <span class="comment">{{ item.beReplied[0].content }}</span>
              </div>
              <div class="date">{{ item.time | dateFormat }}</div>
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
        :limit="limit"
      >
      </el-pagination>
    </div>
    <div class="mv-recommend">
      <h3 class="title">相关推荐</h3>
      <div class="mvs">
        <div class="items">
          <div v-for="(item,index) in simiMvs" :key="index" class="item" @click="toMV(item.id)">
            <div class="img-wrap">
              <img :src="item.cover" alt="" />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div class="num">{{ item.playCount | playNumFormat }}</div>
              </div>
              <span class="time">{{ item.duration | playTimeFormat }}</span>
            </div>
            <div class="info-wrap">
              <div class="name">{{ item.name }}</div>
              <div class="singer">{{ item.artistName }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'mv',
  data() {
    return {
      // 总条数
      total: 20,
      // 页码
      page: 1,
      // 页容量
      limit: 10,
      //mv地址
      url:"",
      //相关mv
      simiMvs:[],
      //mv的信息
      mvInfo:[],
      //头像
      icon:'',
      hotComments: [],
      comments: [],
      mvid:''
      
    };
  },
  // watch: {
  //   $route (to, from) {
  //     toMV(id){
  //     console.log('yes')
  //     this.$router.push(`/mv?q=${id}`)
  //   }
  //   },
  // },
  watch: {
　　'$route' (to, from) {
　　this.$router.go(0);
　　}
},
  created(){
    //获取mv播放地址
    axios({
      url:' https://autumnfish.cn/mv/url',
      method:'get',
      params:{
        //获取url中的id
        id:this.$route.query.q,
      }
    }).then(res=>{
      // console.log(res)
      this.url = res.data.data.url
      this.comments = res.data.comments
      this.hotComments = res.data.hotComments
    });
    //获取相关的mv
    axios({
      url:'https://autumnfish.cn/simi/mv',
      ethod:'get',
      params:{
        mvid:this.$route.query.q
      }
    }).then(res=>{
      // console.log(res)
      // 保存相关mv
      this.simiMvs = res.data.mvs
    });
    //获取mv的信息
    axios({
      url:'https://autumnfish.cn/mv/detail',
      method:'get',
      params:{
        mvid:this.$route.query.q
      }
    }).then(res=>{
      //mv的信息
      this.mvInfo = res.data.data
      //获取 歌手信息
      axios({
        url:'https://autumnfish.cn/artists',
        method:'get',
        params:{
          id:this.mvInfo.artists[0].id
        }
      }).then(res=>{
        // console.log(res)
        //歌手的封面信息
        this.icon = res.data.artist.picUrl
      })
    });
    //获取评论数据
    axios({
      url:'https://autumnfish.cn/comment/mv',
      method:'get',
      params:{
        id:this.$route.query.q,
        limit:10,
        offset:0
      }
    }).then(res=>{
      console.log(res)
      this.comments = res.data.comments
      this.hotComments = res.data.hotComments
    })
    },
  methods: {
    //跳转到mv
    toMV(id){
      // console.log('yes')
      this.$router.push(`/mv?q=${id}`)
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.page = val
      axios({
      url:'https://autumnfish.cn/comment/mv',
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

<style></style>
