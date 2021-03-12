<template>
  <div class="result-container">
    <div class="title-wrap">
      <h2 class="title">{{ $route.query.q }}</h2>
      <span class="sub-title">找到{{count}}个结果</span>
    </div>
    <el-tabs v-model="activeIndex" @tab-click="handleClick">
      <el-tab-pane label="歌曲" name="songs">
        <table class="el-table">
          <thead>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>
            <tr v-for="(item,index) in songList"
             :key="index"
             class="el-table__row"
             @dblclick="playMusic(item.id)">
              <td>{{ index+1 }}</td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <!-- 歌名 -->
                    <span>{{ item.name }}</span>
                    <!-- mv图标 -->
                    <span v-if="item.mvid!=0" class="iconfont icon-mv" @click="toMV(item.mvid)"></span>
                  </div>
                  <!-- 二级标题 -->
                  <span v-if="item.alias.length!=0">{{ item.alias[0] }}</span>
                </div>
              </td>
              <td>{{ item.artists[0].name }}</td>
              <td>{{ item.album.name }}</td>
              <td>{{ item.duration | playTimeFormat }}</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      
      <el-tab-pane label="歌单" name="lists">
        <div class="items">
          <div v-for="(item,index) in playlists" :key="index" class="item"
           @click="toPlaylist(item.id)">
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:
                <span class="num">{{ item.playCount | playNumFormat }}</span>
              </div>
              <img :src="item.coverImgUrl" alt="" />
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{ item.name }}</p>
          </div>
        </div>
        
      </el-tab-pane>
      
      <el-tab-pane label="MV" name="mv">
        <div class="items mv">
          <div v-for="(item,index) in mv" :key="index" class="item" @click="toMV(item.id)">
            <div class="img-wrap">
              <!-- 封面 -->
              <img :src="item.cover" alt="" />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <!-- 播放次数 -->
                <div class="num">{{ item.playCount | playNumFormat }}</div>
              </div>
              <!-- 持续时间 -->
              <span class="time">{{ item.duration | playTimeFormat }}</span>
            </div>
            <div class="info-wrap">
              <!-- mv名 -->
              <div class="name">{{ item.name }}</div>
              <!-- 歌手名 -->
              <div class="singer">{{ item.artistName }}</div>
            </div>
          </div>
        </div>
      </el-tab-pane>
    </el-tabs>
    <el-pagination
      @current-change="handleCurrentChange"
      background
      layout="prev, pager, next"
      :total="total"
      :current-page="page"
      :page-size="10">
      </el-pagination>
  </div>
</template>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'result',
  data() {
    return {
      activeIndex: 'songs',
      // 总条数
        total: 0,
        // 页码
        page: 1,
      //保存 查询歌曲
      songList:[],
      //保存歌单的字段
      playlists:[],
      //保存mv的字段
      mv:[],
      // 搜索结果的总数
      count:0,
      type:'1',
      limit:15,
      mvid:'',
    };
  },
  created(){
    axios({
      url:'https://autumnfish.cn/search',
      method:'get',
      params:{
        keywords:this.$route.query.q,
        type:this.type,
        limit:this.limit,
        offset:(this.page-1)*10
      }
    }).then(res=>{
      console.log(res)
      if(this.type == 1){
      this.songList=res.data.result.songs
      this.count = res.data.result.songCount
      }
      else if(this.type == 1000){
      this.playlists = res.data.result.playlists
      this.count = res.data.result.playlistCount
      }
      else{
      this.mv = res.data.result.mvs
      this.count = res.data.result.mvCount
      }
      this.total = this.count
    })
  },
  //侦听器
  watch:{
    
    //搜索框输入新内容
    
    $route(to, from){
      
    this.keywords= this.$route.query.q
    
      
      this.type=1
      
      // this.count = res.data.result.songCount

      this.creat()
    
    
  },

  //   //tab切换歌曲歌单mv
  //   activeIndex(){
  //   //songs 歌曲
  //   //lists 歌单
  //   //mv    mv
  //   console.log(this.type)
  //   // let type = 1;
  //   let limit = 10;
  //   switch(this.activeIndex){
  //     case 'songs':
       
  //       limit=10;
  //       this.page=1;
  //       break;
  //     case 'lists':
        
  //       limit=10;
  //       this.page=1;
  //       break;
  //     case 'mv':
        
  //       limit=8;
  //       this.page=1;
  //       break;

  //     default:
  //       break;
  //   }
  //   axios({
  //     url:'https://autumnfish.cn/search',
  //     method:'get',
  //     params:{
  //       keywords: this.$route.query.q,
  //       type,
  //       limit,
  //     }
  //   }).then(res=>{
  //     console.log(res)
  //     //获取歌曲
  //     // this.creat()
  //     if(type == 1){
  //     this.songList=res.data.result.songs
  //     //计算歌曲时间
  //     this.page = 1;
  //     //保存总数
  //     this.count = res.data.result.songCount
  //     }
  //     //获取歌单
  //     else if(type == 1000){
  //       //歌单逻辑
  //       this.playlists = res.data.result.playlists
  //       //总数
  //       this.count = res.data.result.playlistCount
  //       //处理播放次数
  //       this.page = 1;
  //     }
  //     //获取mv
  //     else{
  //       //保存mv数据
  //       this.mv = res.data.result.mvs
  //       //总数
  //       this.count = res.data.result.mvCount
  //       this.page = 1;
  //     }
  //   })


  //   },
   type(){
    //  this.creat()
    console.log(this.type)
   }
  },
  //方法
  methods:{
    handleClick(tab, event) {
        // console.log(tab, event);
        if(tab.name == 'songs'){
        	this.type = 1
          this.creat()
        }else if(tab.name == 'lists'){
        	this.type = 1000
          this.creat()
        }else if(tab.name == 'mv'){
        	this.type = 1004
          this.creat()
        }
      },

    //created方法
    creat(){
      axios({
      url:'https://autumnfish.cn/search',
      method:'get',
      params:{
        keywords:this.$route.query.q,
        type:this.type,
        limit:10,
        offset:(this.page-1)*10
      }
    }).then(res=>{
      console.log(res)
      if(this.type == 1){
      this.songList=res.data.result.songs
      this.count = res.data.result.songCount
      }
      else if(this.type == 1000){
      this.playlists = res.data.result.playlists
      this.count = res.data.result.playlistCount
      }
      else{
      this.mv = res.data.result.mvs
      this.count = res.data.result.mvCount
      }
      this.total = this.count
    })
    },
    // 页码改变事件
    handleCurrentChange (val) {
      // 保存页码
      this.page = val
      // 重新获取数据
      this.creat()
    },
    //去mv详情页
    toMV(id){
      this.$router.push(`/mv?q=${id}`)
    },
    //去歌单详情页
    toPlaylist(id){
      this.$router.push(`/playlist?q=${id}`)
    },
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
    search(){
    axios({
      url:'https://autumnfish.cn/search',
      method:'get',
      params:{
        keywords: this.$route.query.q,
        type:1,
        limit:10,
      }
    }).then(res=>{
      console.log(res)
      //获取歌曲
      if(type == 1){
      this.songList=res.data.result.songs
      //计算歌曲时间
    
      //保存总数
      this.count = res.data.result.songCount
      }
      //获取歌单
      else if(type == 1000){
        //歌单逻辑
        this.playlists = res.data.result.playlists
        //总数
        this.count = res.data.result.playlistCount
        //处理播放次数
      }
      //获取mv
      else{
        //保存mv数据
        this.mv = res.data.result.mvs
        //总数
        this.count = res.data.result.mvCount
        
      }
    })
  },
  }
  
};
</script>

<style >

</style>
