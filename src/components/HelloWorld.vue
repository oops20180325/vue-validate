<template>
  <div class="hello" >
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



