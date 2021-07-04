<template>
  <div class="container">
    <div class="text-end" v-if="cart.carts">
      <button class="btn btn-outline-danger" type="button" @click="deleteAll" v-if="cart.carts.length !== 0">清空購物車</button>
    </div>
    <table class="table align-middle">
      <thead>
        <tr>
          <th></th>
          <th>品名</th>
          <th style="width: 150px">數量/單位</th>
          <th>單價</th>
        </tr>
      </thead>
      <tbody>
        <template v-if="cart.carts">
          <tr v-for="item in cart.carts" :key="item.id">
            <td>
              <button type="button" class="btn btn-outline-danger btn-sm" @click="deleteCart(item)"
              :disabled="loadingStatus.loadingItem === item.id">
                <i class="fas fa-spinner fa-pulse" v-if="loadingStatus.loadingItem === item.id"></i>
                x
              </button>
            </td>
            <td>
              {{ item.product.title }}
              <div class="text-success">
                已套用優惠券
              </div>
            </td>
            <td>
              <div class="input-group input-group-sm">
                <div class="input-group mb-3">
                  <input min="1" type="number" class="form-control"
                  @change="updateCart(item)"
                  :disabled="item.id === loadingStatus.loadingItem"
                  v-model.number="item.qty">
                  <span class="input-group-text" id="basic-addon2"></span>
                </div>
              </div>
            </td>
            <td class="text-end">
              <small class="text-success">折扣價：</small>
              {{ item.product.price }}
            </td>
          </tr>
        </template>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3" class="text-end">總計</td>
          <td class="text-end">{{ cart.total }}</td>
        </tr>
        <tr>
          <td colspan="3" class="text-end text-success">折扣價</td>
          <td class="text-end text-success">{{ cart.final_total }}</td>
        </tr>
      </tfoot>
    </table>

  </div>
  <div class="my-5 row justify-content-center">
    <Form ref="form" class="col-md-6" v-slot="{ errors }" @submit="onSubmit">
      <div class="mb-3">
        <label for="email">E-mail</label>
        <Field id="email" name="email" type="email" class="form-control"
          :class="{ 'is-invalid': errors['email'] }"
          placeholder="請輸入 Email" rules="email|required" v-model="form.user.email"></Field>
        <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="name">姓名</label>
        <Field id="name" name="姓名" type="姓名" class="form-control"
          :class="{ 'is-invalid': errors['姓名'] }"
          placeholder="請輸入姓名" rules="required" v-model="form.user.name"></Field>
        <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="phone">電話</label>
        <Field id="phone" name="電話" type="tel" class="form-control"
          :class="{ 'is-invalid': errors['電話'] }"
          placeholder="請輸入電話" :rules="isPhone" v-model="form.user.tel"></Field>
        <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="address">地址</label>
        <Field id="address" name="地址" type="地址" class="form-control"
          :class="{ 'is-invalid': errors['地址'] }"
          placeholder="請輸入地址" rules="required" v-model="form.user.address"></Field>
        <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="message" class="form-label">留言</label>
        <textarea id="message" class="form-control" cols="30" rows="10"></textarea>
      </div>
      <div class="text-end" v-if="cart.carts">
        <button type="submit" class="btn btn-danger" :disabled="cart.carts.length === 0">送出訂單</button>
      </div>
    </Form>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loadingStatus: {
        loadingItem: ''
      },
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      },
      cart: {}
    }
  },
  methods: {
    getCart () {
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/cart`
      this.$http.get(api)
        .then(res => {
          this.cart = res.data.data
        })
        .catch(err => console.log(err))
    },
    updateCart (item) {
      this.loadingStatus.loadingItem = item.id
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/cart/${item.id}`
      const cart = {
        product_id: item.product.id,
        qty: item.qty
      }
      console.log(cart, api)
      this.$http.put(api, { data: cart })
        .then(res => {
          this.loadingStatus.loadingItem = ''
          this.cart = res.data.data
          console.log()
          this.getCart()
        })
        .catch(err => console.log(err))
    },
    deleteCart (item) {
      this.loadingStatus.loadingItem = item.id
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/cart/${item.id}`
      this.$http.delete(api)
        .then(res => {
          console.log(res)
          this.loadingStatus.loadingItem = ''
          this.getCart()
        })
        .catch(err => console.log(err))
    },
    deleteAll () {
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/carts`
      this.$http.delete(api)
        .then(res => {
          console.log(res)
          this.getCart()
        })
        .catch(err => console.log(err))
    },
    isPhone (value) {
      const phoneNumber = /^(09)[0-9]{8}$/
      return phoneNumber.test(value) ? true : '需為09開頭且為10碼數字'
    },
    onSubmit () {
      console.log(this.form)
      const api = `${process.env.VUE_APP_URL}/api/${process.env.VUE_APP_PATH}/order`
      this.$http.post(api, { data: this.form })
        .then(res => {
          if (res.data.success) {
            console.log(res)
            this.getCart()
            this.$refs.form.resetForm()
            alert('已送出訂單')
          }
        })
        .catch(err => console.log(err))
    }
  },
  mounted () {
    this.getCart()
  }
}
</script>
