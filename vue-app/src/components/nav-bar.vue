<template>
  <div>
    <nav class="column is-2 menu">
      <p class="menu-label">Menu</p>
      <ul class="menu-list">
        <router-link to="/products">Products</router-link>
        <router-link to="/about">About</router-link>
      </ul>
    </nav>
    <nav class="menu auth">
      <p class="menu-label">Auth</p>
      <div class="menu-list auth">
        <template v-if="!userInfo">
          <div v-for="provider in providers" :key="provider">
            <a
              :href="`/.auth/login/${provider}?post_login_redirect_uri=${redirect}`"
            >
              {{ provider }}
            </a>
          </div>
        </template>
        <a
          v-if="userInfo"
          :href="`/.auth/login/${provider}?post_login_redirect_uri=${redirect}`"
        >
          Logout
        </a>
      </div>
    </nav>
    <div class="user" v-if="userInfo">
      <p>Welcome</p>
      <p>{{ userInfo.userDetails }}</p>
      <p>{{ userInfo.identityProvider }}</p>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'NavBar',
  data() {
    return {
      userInfo: {
        type: Object,
        default() {},
      },
      providers: ['google'],
      redirect: window.location.pathname,
    };
  },
  methods: {
    async getUserInfo() {
      try {
        const response = await fetch('/.auth/me');
        const payload = await response.json();
        const { clientPrincipal } = payload;
        this.userInfo = clientPrincipal;
      } catch (error) {
        console.error('No profile could be found');
        this.userInfo = undefined;
      }
    },
  },
  created() {
    this.userInfo = this.getUserInfo();
  },
});
</script>
