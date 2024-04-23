<template>
  <div class="hello" >
    <div>
      点击验证码图片更换验证码
      <br/>
      验证码不区分大小写
    </div>
    <yanzheng v-on:validate='result'  size='4' width='200' />

  </div>
</template>

<script>

import yanzheng from './验证'
export default {
  name: 'HelloWorld',
  data () {
    return {

    }
  },
  components:{
    yanzheng
  },
  methods:{
    result(data){
      alert('验证结果为：'+data)
    }
  },
  mounted(){
  }
}
</script>



