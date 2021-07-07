<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">後台</a>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link to="/admin/products" class="nav-link">後台產品列表</router-link>
            </li>
            <li class="nav-item">
              <button type="button" class="btn btn-light" @click="logOut">登出</button>
            </li>
          </ul>
        </div>
      </div>
    </nav>
    這是後台
    <router-view v-if="checkSuccess"></router-view>
  </div>
</template>

<script>
export default {
  data () {
    return {
      checkSuccess: false
    }
  },
  methods: {
    checkLogin () {
      const token = document.cookie.replace(/(?:(?:^|.*;\s*)myToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
      if (token) {
        this.$http.defaults.headers.common.Authorization = `${token}`
        const api = `${process.env.VUE_APP_URL}/api/user/check`
        this.$http.post(api)
          .then(res => {
            console.log(res.data)
            if (res.data.success) {
              this.checkSuccess = true
            } else {
              alert(res.data.message)
              this.$router.push('/login')
            }
          })
      }
    },
    logOut () {
      const url = `${process.env.VUE_APP_URL}/logout`
      this.$http.post(url)
        .then(res => {
          console.log(res)
          document.cookie = 'hexToken=;expires=;'
          alert('token已清除')
          this.$router.push('/login')
        })
        .catch(err => console.log(err))
    }
  },
  created () {
    this.checkLogin()
  }
}
</script>
