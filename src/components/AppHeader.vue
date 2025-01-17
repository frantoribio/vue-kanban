<template>
  <header>
    <span class="title">Vue Kanban</span>
    <!-- Si en vez de URL quiero meter el componente usa
    <router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link> -->
    <template v-if="userStore.user.email">
      <router-link class="btn-header" :to="{ name: 'Home' }">Mis Paneles</router-link>
      <div class="userinfo">
        <img :src="userStore.user.photoURL" :alt="userStore.user.displayName" />
        <span>{{ userStore.user.displayName }}</span>
        <button class="btn-header btn-login" @click="doLogOut">Logout</button>
      </div>
    </template>
    <template v-else>
      <button class="btn-header btn-login" @click="doLogIn">Login</button>
    </template>
  </header>
</template>

<script lang="ts">
import { defineComponent, onMounted } from 'vue';
import { useRouter } from 'vue-router';

import UserStore from '@/store/UserStore';
import { notify } from '@kyvg/vue3-notification';

export default defineComponent({
  name: 'AppHeader',

  setup() {
    const userStore = UserStore();
    const router = useRouter();

    // My MyLifeHooks
    onMounted(() => {
      if (userStore.user.email) {
        notify({
          title: 'Bienvenido/a',
          text: `Me alegro de verte de nuevo ${userStore.user.name}`,
        });
      }
    });

    async function doLogIn() {
      console.log('Header doLogIn');
      try {
        await userStore.logIn();
        router.go(0); // recargamos
        // console.log('Header doLogin ->', actualUser);
      } catch (error) {
        notify({
          title: 'Error',
          text: error.message,
          type: error,
        });
      }
    }

    async function doLogOut() {
      console.log('Header doLogOut');
      try {
        await userStore.logOut();
        router.go(0); // Recargamos
      } catch (error) {
        notify({
          title: 'Error',
          text: error.message,
          type: error,
        });
      }
    }

    return {
      userStore,
      doLogIn,
      doLogOut,
    };
  },
});
</script>

<style lang='scss' scoped>
header {
  box-sizing: border-box;
  background-color: #607d8b;
  // Layout flex
  display: flex;
  // Una row ocupan columnas
  flex-direction: row;
  // Alinea desde el comienzo
  justify-content: flex-start;
  margin: 0;
  padding: 1rem;

  .title {
    // Alineamos en el centro de la fila
    align-self: center;
    color: #eceff1;
    font-size: 1.2rem;
    font-weight: bold;
    margin-right: 1rem;
  }
}

.btn-header {
  background-color: #455a64;
  border: none;
  border-radius: 3px;
  box-shadow: 0 0 0 0.5px rgba(49, 49, 93, 0.03), 0 2px 5px 0 rgba(49, 49, 93, 0.1),
    0 1px 2px 0 rgba(0, 0, 0, 0.08);
  color: #d7e3e9;
  cursor: pointer;
  text-decoration: none;
  padding: 0.5rem;
  margin: 0 0.5rem;
}

.btn-login {
  background-color: #00695c;
  color: #fafafa;
}

.userinfo {
  align-items: center;
  display: flex;
  flex-direction: row;
  justify-content: space-between;

  span {
    color: #fafafa;
    font-size: 0.9rem;
    font-weight: normal;
  }
}

img {
  width: 32px;
  border-radius: 50%;
  margin: 0 1rem;
}
</style>
