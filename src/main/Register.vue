<template>
   <div class="container">
     <div class="row justify-content-md-center padding-tb-1">
       <label class="col-sm-3 text-left">用户名：</label>
       <input type="text" class="col-sm-6 form-control inp" v-model="username">
     </div>
     <div class="row justify-content-md-center padding-tb-1" >
       <label class="col-sm-3 text-left">手机号：</label>
       <input type="text" class="col-sm-6 form-control inp" v-model="telephone">
     </div>
     <div class="row justify-content-md-center padding-tb-1">
       <label class="col-sm-3 text-left">验证码：</label>
       <input type="text" class="col-sm-6 form-control ver" v-model="vercode">
       <button @click="getCode()" @tap="getCode()" class="pull-right btn btn-info ver">发送验证码{{count}}</button>
     </div>
     <div class="row justify-content-md-center padding-tb-1">
       <button @click="res()" @tap="res()" class="col-sm-5 btn btn-info inp">提交</button>
     </div>
   </div>
</template>

<script>
  export default {
    name: 'Register',
    data () {
      return {
        show: true,
        count: '',
        timer: null,
        code: '',
        vercode: '',
        username: '',
        telephone: '',
        path: ''
      }
    },
    methods: {
      getCode () {
        const phoneRex = /^1[345789][0-9]{9}/
        if (this.username === '') {
          alert('请输入用户名')
          return false
        } else if (!(phoneRex.test(this.telephone)) || this.telephone === '') {
          alert('手机号为空或者格式不正确')
          return false
        } else {
          const TIME_COUNT = 60
          if (this.show) {
            // this.$http.get('http://192.168.1.123:3000/getjson/test')
            this.$http.get('http://localhost:3000/getjson/test')
              .then((res) => {
                this.code = res.data.data.code
                this.$alert('已发送验证码')
              }).catch((err) => {
                this.$alert('信息发送失败' + err)
                this.count = 0
              })
          }
          this.show = false
          if (!this.timer) {
            this.count = TIME_COUNT
            this.timer = setInterval(() => {
              if (this.count > 0 && this.count <= TIME_COUNT) {
                this.count--
              } else {
                this.show = true
                clearInterval(this.timer)
                this.timer = null
                this.count = ''
              }
            }, 1000)
          }
        }
      },
      res () {
        if (this.vercode === this.code && this.vercode !== '') {
          this.$http.get('http://localhost:3000/getjson/login')
            .then((res) => {
              this.$router.push(res.data.data.path)
            }).catch((err) => {
              this.$alert('提交失败，请检查网络' + err)
            })
        } else {
          this.$alert('验证码错误')
        }
      }
    }
}
</script>

<style scoped>

</style>
