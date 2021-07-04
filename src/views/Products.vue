<template>
  <div class="container">
    <table class="table align-middle">
      <thead>
        <tr>
          <th>圖片</th>
          <th>商品名稱</th>
          <th>價格</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div style="height: 100px; background-size: cover; background-position: center"
            :style="{ backgroundImage: `url(${item.imageUrl})` }"></div>
          </td>
          <td>
            {{ item.title }}
          </td>
          <td>
            <div class="h5">{{ item.price }} 元</div>
            <del class="h6">原價 {{ item.origin_price }} 元</del>
            <div class="h5">現在只要 {{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
              <button type="button" class="btn btn-outline-secondary" @click="goToPage(item)"
              :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                查看更多
              </button>
              <button type="button" class="btn btn-outline-danger" @click="addCart(item.id)"
              :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                加到購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loadingStatus: {
        loadingItem: ''
      },
      products: [],
      content: '',
      image: '',
      tempProduct: {
        // imagesUrl: []
      },
      isNew: false,
      pagination: {}
    }
  },
  methods: {
    goToPage (item) {
      console.log(this.$route)
      console.log(this.$router)
      this.$router.push(`/product/${item.id}`)
    },
    getProduct (page = 1) {
      this.$http.get(`${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/products?all`)
        .then(res => {
          console.log(res.data)
          if (res.data.success) {
            this.products = res.data.products
            this.pagination = res.data.pagination
          }
        })
        .catch(err => console.log(err))
    },
    addCart (id, qty = 1) {
      this.loadingStatus.loadingItem = id
      const cart = {
        product_id: id,
        qty
      }
      console.log(cart)
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/cart`
      this.$http.post(api, { data: cart })
        .then(res => {
          this.loadingStatus.loadingItem = ''
          console.log(res)
          this.getProduct()
          alert('已新增至購物車')
        })
        .catch(err => console.log(err))
    }
  },
  created () {
    this.getProduct()
  }
}
</script>
