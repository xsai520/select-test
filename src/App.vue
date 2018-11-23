<template>
  <div id="app">
    <div class="select-box">
      <div class="select-input-area" :class="{active:isSelectListOpen}">
        <div class="select-input-box"><!--@click="openSelectList"-->
          <div class="select-input-render" @click="openSelectList">
            <ul class="select-input-list">
              <li class="select-selection__choice" v-for="item in select_input_list" :key="item.id">
                <div>{{item.name}}</div>
                <span  class="icon iconfont select-selection__choice__remove" @click="deleteSelectedItem(item.id)">&#xe602;</span>
              </li>
              <li class="select-search--inline">
                <div class="select-search__field__wrap">
                  <input autocomplete="off" class="select-search__field" v-model="inputValue" :style="{width:inputWidth+'px'}"
                         @keyup="keyUp()" id="inputVal" ref="inputVal">
                  <span ref="spanVal" class="select-search__field__mirror">{{inputValue}}</span>
                </div>
              </li>
            </ul>
          </div>

        </div>
        <!--<span class="icon iconfont delete-icon" contenteditable="false" @click="deleteInputSelected">&#xe61c;</span>
        <span class="select-icon" contenteditable="false">
            <i class="icon iconfont" :class="{selectIconActive: isSelectListOpen}">&#xe601;</i>
        </span>-->
      </div>
      <div class="select-list-box" :class="{'selectListOpen':isSelectListOpen,'selectListEmpty': isSelectListEmpty}">
        <ul class="select-list">
          <li v-for="item in select_list" :key="item.id" @click="selectItem(item.id)" :class="{itemActive:item.active}"><span>{{item.name}}</span><i class="selected-icon icon iconfont">&#xe64c;</i></li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
      return{
        isSelectListOpen:false,//判断列表结构是否展开
//        count:0,//输入区域点击的次数 奇数展开 偶数闭合
        isSelectedItem:false,//条目是否选中
        isSelectListEmpty:true,//如果select_list为空数组的时候是true，否则为false
        inputValue:"",
        inputWidth:34,
        select_input_list:[],
        select_list:[]
      }
  },
  mounted(){
    this.getSelectList();
  },
  methods:{
    openSelectList(){
      this.isSelectListOpen=true;
      this.$nextTick(function () {
        this.$refs.inputVal.focus()
      })
    },
    keyUp(){
        this.inputValue = this.$refs.inputVal.value;
        this.inputWidth = this.$refs.spanVal.offsetWidth;
      this.$http.get('/api/getItem',{name:this.$refs.inputVal.value}).then((res)=>{
          console.log(res)
        setTimeout(()=>{
          if(res.data.data && res.data.data.length>0){
            this.isSelectListEmpty = false;
            this.select_list = res.data.data;
          }
        },2000)

      })
    },
    deleteSelectedItem(id){
      let oEvent = window.event || arguments.callee.caller.arguments[0];
      oEvent.cancelBubble = true;
      oEvent.stopPropagation();
      this.select_input_list.forEach((v,i)=>{
        if(v.id==id){
          this.select_input_list.splice(i,1)
        }
      });
      this.select_list.forEach((v,i)=>{

      })
    },
//    deleteInputSelected(ev){//点击删除按钮删除编辑区域中内容
//      let oEvent = ev || event;
//      //js阻止事件冒泡
//      oEvent.cancelBubble = true;
//      oEvent.stopPropagation();
//      this.select_input_list=[];//将输入区域清除
//      this.select_list.forEach(function(v,i){ //将蓝色对号的数据清除
//        v.active=false;
//      })
//    },
    selectItem(id){
        //选择list中的数据 奇数是选中 偶数是取消选中 将选中的塞入到 select_input_list 取消的从select_input_list中剔除
      let _this = this;
      this.select_list.forEach((v)=> {
        if(v.id==id){
            v.active = !v.active;
            if(v.active){ //v.active==true 说明是选中的
              this.select_input_list.push(v);
            }else{ //取消选中
              _this.select_input_list.forEach((v1,i1)=>{
                  if(id==v1.id){ //点击的item中的id与输入区域中某条数据的id如果相同的话，则剔除
                    _this.select_input_list.splice(i1,1)
                  }
              })
            }
        }
      })
    },
    getSelectList(){
        this.$http.get('/list').then((res)=>{
           setTimeout(()=>{
             if(res.data.data && res.data.data.length>0){
               this.isSelectListEmpty = false;
               this.select_list = res.data.data;
             }
           },2000)

        })
    }
  }
}
</script>

<style>
 *{
   margin:0;
   padding:0;
 }
 ul li{
   list-style: none;
 }
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
  background: #fff;
}
/*最外层包裹*/
.select-box{
  width:320px;
  margin:0 auto;
  color: #999;
}
/*输入区域最外层*/
 .select-input-area{
   position: relative;
   width: inherit;
   min-height:32px;
   border: 1px solid #ddd;
   box-sizing: border-box;
   border-top-left-radius: 4px;
   border-top-right-radius: 4px;
 }
 .select-input-area.active{
   border: 1px solid #1890FF;
 }
 /*输入区域*/
 .select-input-box{
   width:320px;
   min-height:30px;
   line-height: 30px;
   padding: 0 8px;
   box-sizing: border-box;
   overflow: hidden;
   outline: #1890FF;
   text-align: left;
   caret-color:#1890FF;
 }
 .select-input-render{
   width: 304px;
   height: auto;
   min-height: 30px;
   overflow: hidden;
 }
 .select-input-list{
   clear: both;
 }
 .select-input-list li{
   float: left;
 }
.select-selection__choice{
  position: relative;
  height: 24px;
  line-height: 25px;
  margin-top: 3px;
  margin-right: 5px;
  padding: 0 20px 0 10px;
  background-color: #eee;
  border-radius: 2px;
  cursor: default;
  overflow: hidden;
  user-select: none;
}
 .select-input-list li .select-search__field{
   flex:1;
   width: .75em;
   height:20px;
   max-width: 100%;
   border: none;
   outline: none;
 }
 .select-search--inline{
   width: auto;
   max-width: 100%;
 }
 .select-search__field__wrap{
   position: relative;
   width: 100%;
   height: 100%;
 }
 .select-search__field__mirror{
   position: absolute;
   top: 0;
   left: 0;
   white-space: pre;
   pointer-events: none;
   opacity: 0;
 }
 .select-selection__choice__remove{
   display: inline-block;
   position: absolute;
   top: 0;
   right: 2px;
   z-index: 2;
   cursor: pointer;

 }
  /*列表*/
  .select-list-box{
    display: none;
    width: 320px;
    height:307px;
    /*max-height: 306px;*/
    border:1px solid #1890FF;
    border-top-width: 0;
    box-sizing: border-box;
    overflow-y: scroll;
  }
  /*修改滚动条的样式*/
 /*定义滚动条宽高及背景，宽高分别对应横竖滚动条的尺寸*/
 .select-list-box::-webkit-scrollbar{
   width: 4px;
   height: 16px;
   background-color: #fff;
 }
 /*定义滚动条的轨道，内阴影及圆角*/
 .select-list-box::-webkit-scrollbar-track{
   border-radius: 10px;
   background-color: #fff;
 }
 /*定义滑块，内阴影及圆角*/
 .select-list-box::-webkit-scrollbar-thumb{
   height: 20px;
   border-radius: 10px;
   -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
   background-color: #ddd;
 }
 .select-list-box.selectListOpen{
   display: block;
 }
 .select-list-box.selectListEmpty{
   background: url("./assets/images/loading.gif") center center no-repeat;
 }
  .select-list{
    padding: 8px 0;
  }
  .select-list li{
    height:29px;
    line-height: 29px;
    padding:0 10px;
    text-align: left;
    cursor: pointer;
  }
  .select-list li span{
    color: #999;
  }
  .select-list li:hover{
    background: #eee;
    cursor: pointer;
  }
  /*selected-icon 对号图标
  1、如果没有选中某条 划过时候出现灰色对号
  2、如果已经选中了某条 划过的时候出现蓝色
  */
  .select-list li .selected-icon{
    float: right;
    font-size: 18px;
    color:#fff;
  }
  .select-list li:hover .selected-icon{
    color:#ddd;
  }
  .select-list li.itemActive .selected-icon{
    float: right;
    color:#1890FF;
  }
</style>
