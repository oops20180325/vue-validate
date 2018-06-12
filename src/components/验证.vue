<template>
  <div class="hello">

       <div class="验证" >
           <div id="v_container" ref='can_container' :style="'width:'+width+'px;height:' +height+'px;' " class='can_container'>
               <canvas ref='can' width=200  height=50 style='cursor:pointer;' @click='refresh'>您的浏览器版本不支持canvas</canvas>
           </div>
           <div style='margin-left:50%;transform:translate(-50%,0);text-align:center;'>
               <input type="text" id="code_input" ref="code_input" value="" placeholder="请输入验证码" autocomplete="off"  :class='[haveVali?"":valPass?"pass":"notpass","inpdefaule"]'  @focus='iptfocus'/>
                <button id="my_button" class='val_btn' @click='validate($refs.code_input.value)'>验证</button>
           </div>

       </div>
  </div>
</template>

<script>


export default {
  name: 'cValidate',
  data () {
    return {
        haveVali:true,
        valPass:false,
        options:{
            numArr : "0,1,2,3,4,5,6,7,8,9".split(","),
            letterArr: "a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z".split(","),
            code:'',
            width:200,
            height:50,
        }

    }
  },
  props:{
      cantype:{
          default:'blend' //图形验证码默认类型blend:数字字母混合类型、number:纯数字、letter:纯字母
      },
      size:{
          default:5
      },
      width:{
          default:200
      },
      height:{
          default:50
      }
  },
  components:{

  },
  methods:{
    randomColor(min, max) {
        var r = this.randomNum(min, max);
        var g = this.randomNum(min, max);
        var b = this.randomNum(min, max);
        return "rgb(" + r + "," + g + "," + b + ")";
    },
    randomNum(min, max) {
        return Math.floor(Math.random() * (max - min) + min);
    },
    init(){
        var con = this.$refs.can_container;
        var canvas = this.$refs.can;
        console.log(con.offsetWidth)
        this.options.width = con.offsetWidth > 0 ? con.offsetWidth : "100";
        this.options.height = con.offsetHeight > 0 ? con.offsetHeight : "30";
        canvas.width = this.options.width;
        canvas.height = this.options.height;
    },
    refresh(){
         this.options.code = "";
        var canvas = this.$refs.can;
        if (canvas.getContext) {
            var ctx = canvas.getContext('2d');
        } else {
            return;
        }
        ctx.textBaseline = "middle";
        ctx.fillStyle = this.randomColor(180, 240);
        ctx.fillRect(0, 0, this.options.width, this.options.height);

        if (this.cantype == "blend") { //判断验证码类型
            var txtArr = this.options.numArr.concat(this.options.letterArr);
        } else if (this.cantype == "number") {
            var txtArr = this.options.numArr;
        } else {
            var txtArr = this.options.letterArr;
        }

        for (var i = 1; i <= +this.size; i++) {
            var txt = txtArr[this.randomNum(0, txtArr.length)];
            this.options.code += txt;
            ctx.font = this.randomNum(this.options.height / 2, this.options.height) + 'px SimHei'; //随机生成字体大小
            ctx.fillStyle = this.randomColor(50, 160); //随机生成字体颜色
            ctx.shadowOffsetX = this.randomNum(-3, 3);
            ctx.shadowOffsetY = this.randomNum(-3, 3);
            ctx.shadowBlur = this.randomNum(-3, 3);
            ctx.shadowColor = "rgba(0, 0, 0, 0.3)";
            var x = this.options.width / (+this.size + 1) * i;
            var y = this.options.height / 2;
            var deg = this.randomNum(-30, 30);
            /**设置旋转角度和坐标原点**/
            ctx.translate(x, y);
            ctx.rotate(deg * Math.PI / 180);
            ctx.fillText(txt, 0, 0);
            /**恢复旋转角度和坐标原点**/
            ctx.rotate(-deg * Math.PI / 180);
            ctx.translate(-x, -y);
        }
        /**绘制干扰线**/
        for (var i = 0; i < 4; i++) {
            ctx.strokeStyle = this.randomColor(40, 180);
            ctx.beginPath();
            ctx.moveTo(this.randomNum(0, this.options.width), this.randomNum(0, this.options.height));
            ctx.lineTo(this.randomNum(0, this.options.width), this.randomNum(0, this.options.height));
            ctx.stroke();
        }
        /**绘制干扰点**/
        for (var i = 0; i < this.options.width / 4; i++) {
            ctx.fillStyle = this.randomColor(0, 255);
            ctx.beginPath();
            ctx.arc(this.randomNum(0, this.options.width), this.randomNum(0, this.options.height), 1, 0, 2 * Math.PI);
            ctx.fill();
        }
    },
    validate(data){
        // console.dir(data)
        this.haveVali = false;
        var code = data.toLowerCase();
        var v_code = this.options.code.toLowerCase();
        if (code == v_code) {
            this.$emit('validate',true)
            this.valPass=true;

        } else {
            this.$emit('validate',false)
            this.valPass=false;
            // this.refresh();

        }
    },
    iptfocus(){
        this.haveVali = true;
    }
  },
  mounted(){
      this.init();
      this.refresh();
  }
}
</script>
<style  scoped>
    .can_container {
        margin-left:50%;
        transform:translate(-50%,0);
        margin-bottom:5px;
    }
    .pass {
         border-color:#41b883!important;

    }
    .notpass {
         border-color:#ef5d5d!important;
    }
    .inpdefaule {
        outline:none ;
        border-radius:5px;
        padding-left:10px;
        height:26px;
        border:1px solid #ccc;
    }
    .val_btn {
        background: #fff;
        border: 1px solid #bfcbd9;
        color: #1f2d3d;
        border-radius: 4px;
        height:30px;
        vertical-align: bottom;
        outline:none ;
    }
    .hello{
    -webkit-user-select:none;
    -moz-user-select:none;
    -ms-user-select:none;
    user-select:none;
}
</style>


