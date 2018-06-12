<template>
    <div class="dms_select" @click="stopProper"  >
          <!-- 输入 -->
          <div class="search">
             <span class="reset_bar" @click="resetSearch">X</span>
             <input type="text" name="" id="" class="ipt"  :placeholder="placeholder" @focus="iptFocus"  v-model="iptContent" >
          </div>
          <transition name="height">
            <div class="options_box" v-if='showoptions' >
                 <!-- <span class="ico">∧</span> -->
                <!-- 确定抛出信息就行 -->
                  <div class="top"> <button @click="output" class="btn_success">确认</button> &nbsp;&nbsp;<button @click="reset">重选</button></div>
                  <div class="content" >
                      <ul>
                        <li class="op">
                          <span class="lf">选择</span>
                          <span class='rt'>{{optionTitle}}</span>
                        </li>
                        <li v-for="(item,index) in filterOptions" :key="index" class="op">
                          <input type="checkbox" name="" :id="'opt'+item.key" :value="item" v-model="outputMessage" @click="optckick">
                          <label :for="'opt'+item.key" v-html='item.title'>{{item.title}}</label>
                          <!-- <label :for="'opt'+item.key" >{{item.title}}</label> -->

                        </li>

                      </ul>
                      <p v-show="noMessageShow" style="border-top:1px solid #ccc; margin: 0; padding:10px 0;text-align:center"><span>无匹配数据</span></p>
                  </div>
              </div>
          </transition>
    </div>
</template>
<script>
export default {
  name:'cSelect',
  data(){
    return {
        showoptions: false, // 是否展示options
        iptContent:'', // 输入框内容
        noMessageShow:false, // 是否显示暂无数据
        outputMessage:[], // 对外抛出信息
    }
  },
  props:{
    placeholder:{
      default:'请输入'
    },
    optionTitle:{
      default:'名称'
    },
    optionsList: Array,
    multiple:Boolean, // 是否多选
  },
  computed:{
    filterOptions(){
      let newArr = this.optionsList.filter((value,index)=>{
        let flag = this.iptContent.replace(/\s+/g,"");
        // console.log('flag',flag)
        // console.log(typeof flag)
        // console.log(value)
        // init
        value.title = value.title.replace(new RegExp("(<font color=skyblue>)",'g'),"");
        value.title = value.title.replace(new RegExp("(</font>)",'g'),"");
        let rt_flag = value.title.indexOf(flag)!= -1
        if(value.title.indexOf(flag)!= -1 && this.iptContent.replace(/\s+/g,"")!=''){
          //  console.log(value.title.indexOf(flag))
          let str = this.iptContent.replace(/\s+/g,"");
          console.log(str)
          let oldtitle = value.title;
          let reg = new RegExp("("+str+")",'g');
          // console.log(str)
          let newtitle = oldtitle.replace(reg,"<font color=skyblue>$1</font>")
          value.title = newtitle;
          console.log(value)
        }
        return  rt_flag;
      })
      newArr.length>0?this.noMessageShow = false:this.noMessageShow = true;
      console.log(newArr)
      return newArr
    }
  },
  methods:{
    // 重置搜索项
    resetSearch(){
      this.iptContent = '';
    },
    // 重置
    reset(){
      this.outputMessage = [];
    },
    optckick(e){
      // console.log(e);
      // console.log(this.multiple)
      if(this.multiple) return ;
      this.outputMessage = []; // 让复选框多选变单选，先触发click 后被选中。所以可以这样用
    },
    // 对外抛出信息
    output(){
      // console.log(this.outputMessage)
      let message = this.outputMessage
      // 过滤掉<font color=skyblue></font>
      message.forEach((value,index,arr)=>{
        value.title = value.title.replace(new RegExp("(<font color=skyblue>)",'g'),"");
        value.title = value.title.replace(new RegExp("(</font>)",'g'),"");
      })
      this.$emit('checkedMessage',message);
      this.showoptions = false;
    },
    // 输入框获取焦点时
    iptFocus(){
      this.showoptions = true;
      this.outputMessage=[];
    },
    // 输入框失去焦点时
    iptBlur(){
      this.showoptions = false;
    },
    // 阻止冒泡
    stopProper(){
      event.stopPropagation();
    }
  },
  created(){
    window.a = this;
    document.onclick=()=>{
      this.showoptions = false;
    }
  }
}
</script>
<style scoped lang='less'>
  .height-enter-active, .height-leave-active {
    transition: all .5s;
    height: 500px;
  }
  .height-enter, .height-leave-to{
    opacity: 0;
    height: 0px;
  }
  .dms_select{
    display: inline-block;
    position: relative;
    .ico {
      position: absolute;
      top: 42px;
      left: 15px;
      color: #cccccc;
      z-index: 10;
      background-color: #fff;
      height: 10px;
      line-height: 1px;
    }
    .reset_bar{
      display: none;
      position: absolute;
      width: 22px;
      height: 22px;
      text-align: center;
      line-height: 22px;
      border-radius: 50%;
      background: #cccccc;
      right: 4px;
      top: 8px;
      cursor: pointer;
    }
    .search:hover .reset_bar {
      display: inline-block;
    }
    .reset_bar:active {
      background-color: rgb(185, 185, 159);
    }
    .ipt {
      padding-left: 10px;
      height: 32px;
      width: 300px;
      border-radius: 5px;
      border: 1px solid #ccc;
      outline:none;
    }
    .top{
      position:fixed;
      top:0;
      left:0;
      width:100%;
      text-align:center;
      height: 30px;
      line-height: 30px;
      border-bottom:1px solid #ccc ;
      button {
        cursor: pointer;
        display: inline-block;
        width: 50px;
        border: none;
        border-radius: 3px;
        height: 22px;
        color: #fff;
      }
      .btn_success {
        background-color: #59C0DE;
      }
    }
    .content {
      margin-top:30px;
       overflow-y: auto;
       max-height:220px;
         &::-webkit-scrollbar {
          width: 10px;
          height: 10px;
      }
      &::-webkit-scrollbar-thumb {
          /*滚动条里面小方块*/
          border-radius: 5px;
          -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
          background: #c2c2c2;
      }
      &::-webkit-scrollbar-track {
          /*滚动条里面轨道*/
          -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
          border-radius: 0;
          background: rgba(0, 0, 0, 0.1);
      }
    }
    .options_box{
      background-color:#fff;
      border: 1px solid #ccc;
      width: 300px;
      max-height: 250px;
      // overflow-y: auto;
      // padding: 10px 0px;
      position: absolute;
      top: 45px;
      left: 50%;
      transform:translateX(-50%);
      .lf{
        display: inline-block;
        width: 80px;
        // height: 100%;
        // padding: 10px 0;
        text-align: center;
      }
      .rt {
        width: 200px;
        display: inline-block;
        text-align: center;
        border-left: 1px solid #ccc;
      }
      ul {
        padding: 0;
        margin: 0;
        .op{
          border-top: 1px solid #ccc;
        }
        li {
          list-style: none;
          padding: 0;
          // border: 1px solid #ccc;
          text-align: left;
          height: 30px;
          line-height: 30px;
          input {
            display: inline-block;
            width: 80px;
            // height: 70%;
            padding: 10px 0;
            margin: 0;
          }
          label {
            cursor: pointer;
            // width:-webkit-calc(100% - 50px);
            border-left: 1px solid #ccc;
            width: 200px;
            display: inline-block;
            text-align: center;
          }
        }
      }
      &::-webkit-scrollbar {
          width: 10px;
          height: 10px;
      }
      &::-webkit-scrollbar-thumb {
          /*滚动条里面小方块*/
          border-radius: 5px;
          -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
          background: #c2c2c2;
      }
      &::-webkit-scrollbar-track {
          /*滚动条里面轨道*/
          -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
          border-radius: 0;
          background: rgba(0, 0, 0, 0.1);
      }

    }


  }

</style>
