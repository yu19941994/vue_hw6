<template>
  <div class="container">
    <h1>This is an about page</h1>
    <Form v-slot="{ errors }">
      <Field id="email" name="信箱" type="email" class="form-control"
        :class="{ 'is-invalid': errors['信箱'] }" placeholder="請輸入 Email"
        rules="email|required" v-model="user.email"></Field>
      <ErrorMessage name="信箱" class="invalid-feedback"></ErrorMessage>
    </Form>
  </div>
</template>

<script>
export default {
  data () {
    return {
      user: {}
    }
  }
}
</script>
