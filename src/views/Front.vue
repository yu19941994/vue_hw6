<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">導覽列</a>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link to="/" class="nav-link">首頁</router-link>
            </li>
            <li class="nav-item">
              <router-link to="/products" class="nav-link">產品頁</router-link>
            </li>
            <li class="nav-item">
              <router-link to="/cart" class="nav-link">購物車</router-link>
            </li>
            <li class="nav-item">
              <router-link to="/login" class="nav-link">登入頁</router-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>
    <router-view></router-view>
  </div>
</template>
