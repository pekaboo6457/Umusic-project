<template>
  <div class="top-container">
    <!-- <div class="left-box"> -->
      <!-- <div class="icon-wrapper">
        <span class="iconfont icon-home"></span>
        <span class="iconfont icon-sami-select"></span>
        <span class="iconfont icon-full-screen"></span>
      </div> -->
      <!-- <div class="history-wrapper">
        <span class="iconfont icon-arrow-lift"></span>
        <span class="iconfont icon-arrow-right"></span>
      </div> -->
    <!-- </div> -->
    <!-- <div class="right-box"> -->
      <!-- <div class="el-input el-input--small el-input--prefix">
        <input
          type="text"
          autocomplete="off"
          placeholder="搜索"
          class="el-input__inner"
          v-model="inputValue"
          @keyup.enter="toResult"
        />
        <span class="el-input__prefix">
          <i class="el-input__icon el-icon-search"></i>
        </span> -->
        <div class="search-box">
            <input class="search-text" type="text" name="" placeholder="Search" 
            v-model="inputValue"
            @keyup.enter="toResult">
             <a class="search-btn" href="#">
                <i class="fas fa-search"></i>
             </a>
          </div>
      </div>
    <!-- </div> -->
</template>

<script>
  export default {
    name: 'top',
    data() {
      return {
        //输入的内容
        inputValue: ''
      }
    },
    // watch:{
    // $route(){
    // this.inputValue= this.$route.query.q
    // this.toResult()
    // }
    // },
    methods:{
      toResult(){
        //非空判断
        if(this.inputValue==''){
          //提示用户
          this.$message.warning('请输入内容')
        }else{
          this.$router.push('/result?q='+this.inputValue)
        }
      }
    }
  }
</script>

<style scoped>
@import "//cdn.bootcdn.net/ajax/libs/font-awesome/5.15.2/css/all.css";

.search-box{
    position:absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
    background: #2c3e50;
    height: 40px;
    border-radius: 40px;
    padding: 0px;
}

.search-box:hover > .search-text{
    width: 150px;
    padding: 0 6px;
}

.search-box:hover > .search-btn{
    background: white;
}

.search-btn{
    color: #e74c3c;
    float: right;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: #2c3e50;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: 0.4s;
}

.search-text{
    border: none;
    background: none;
    outline: none;
    float: left;
    padding: 0;
    color: white;
    font-size: 20px;
    transition: 0.4s;
    line-height: 40px;
    width: 0;
    text-align: center;
}

.top-container{
    height: 100%;
    background-image:linear-gradient(90deg, #70e1f5 0%,#ffd194 100%);
  
}

</style>
