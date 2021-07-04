<template>
  <div class="container mt-5">
      <div class="row justify-content-center">
        <h1 class="h3 mb-3 font-weight-normal text-center">
          請先登入
        </h1>
        <div class="col-8">
          <form id="form" class="form-signin" @submit.prevent="login">
            <div class="form-floating mb-3">
              <input type="email" class="form-control" id="username" placeholder="name@example.com" required autofocus v-model="userprofile.username">
              <label for="username">Email address</label>
            </div>
            <div class="form-floating">
              <input type="password" class="form-control" id="password" placeholder="Password" required v-model='userprofile.password'>
              <label for="password">Password</label>
            </div>
            <button class="btn btn-lg btn-primary w-100 mt-3" type="submit">
              登入
            </button>
          </form>
        </div>
      </div>
      <p class="mt-5 mb-3 text-muted text-center">
        &copy; 2021~∞ - 六角學院
      </p>
    </div>
</template>

<script>
export default {
  data () {
    return {
      userprofile: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      this.$http.post(`${process.env.VUE_APP_URL}/admin/signin`, this.userprofile)
        .then(res => {
          if (res.data.success) {
            const { token, expired } = res.data
            document.cookie = `myToken = ${token}; expires = ${new Date(expired)}`
            console.log('hi')
            this.$router.push('/admin/products')
          } else {
            alert('帳號或密碼輸入有誤喲！')
          }
        })
        .catch(err => console.log(err))
    }
    // init () {
    //   const token = document.cookie.replace(/(?:(?:^|.*;\s*)myToken\s*\=\s*([^;]*).*$)|^.*$/, '$1')
    //   console.log(token)
    //   this.$http.defaults.headers.common['Authorization'] = token
    // }
  }
}
</script>
