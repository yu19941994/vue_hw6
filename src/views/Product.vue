<template>
  <div class="container">
     <div class="row">
      <div class="col-sm-6">
        <img class="img-fluid" alt="" :src="product.imageUrl">
      </div>
      <div class="col-sm-6">
        <span class="badge bg-primary rounded-pill">{{ product.category }}</span>
        <p>商品描述：{{ product.description }}</p>
        <p>商品內容：{{ product.content }}</p>
        <div class="h5">{{ product.origin_price }} 元</div>
        <del class="h6">原價 {{ product.origin_price }} 元</del>
        <div class="h5">現在只要 {{ product.price }} 元</div>
        <div>
          <div class="input-group">
            <input type="number" class="form-control" min="1" v-model.number="qty">
            <button type="button" class="btn btn-primary"
            @click="addCart(product.id, qty)">加入購物車</button>
          </div>
        </div>
      </div>
      <!-- col-sm-6 end -->
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loadingStatus: {
        loadingItem: ''
      },
      id: '',
      product: {},
      qty: 1
    }
  },
  methods: {
    getProduct () {
      const url = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/product/${this.id}`
      this.$http.get(url)
        .then(res => {
          console.log(res)
          this.product = res.data.product
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
    console.log(this.$route)
    const { id } = this.$route.params
    console.log(this.$router)
    this.id = id
    console.log(id)
    this.getProduct()
  }
}
</script>
