<template>
  <div>
    <nav class="navbar navbar-dark navbar-expand-lg bg-dark justify-content-md-center justify-content-end">
      <router-link class="navbar-brand d-md-none d-inline" :to="{ name: 'Products' }">
        Opinion Collector
      </router-link>
      <button class="navbar-toggler ml-1" type="button" data-bs-toggle="collapse" data-bs-target="#collapsingNavbar2">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="navbar-collapse collapse justify-content-between align-items-center w-100" id="collapsingNavbar2">
        <ul class="navbar-nav mx-auto text-md-center text-left">
          <li class="nav-item">
            <router-link v-if="isLoggedIn" class="nav-link mx-3" :to="{ name: 'Panel' }">
              Panel
            </router-link>
          </li>

          <li class="nav-item my-auto">
            <router-link
              class="nav-link navbar-brand fs-3 fw-bold mx-0 d-none d-md-inline"
              :to="{ name: 'Products' }">
              Opinion Collector
            </router-link>
          </li>
          <li class="nav-item">
            <router-link v-if="isLoggedIn && this.user.isAdmin" class="nav-link mx-3" :to="{ name: 'UsersPanel' }">
              Admin
            </router-link>
          </li>
        </ul>
        <ul
          class="nav navbar-nav flex-row justify-content-md-center align-items-center justify-content-center flex-nowrap"
        >
          <li class="nav-item">
            <div v-if="isLoggedIn" class="text-light mx-1">Hi {{ this.user.firstName }}!</div>
          </li>
          <li class="nav-item dropdown mx-auto">
            <div class="nav-item" @click="toggleDropdown()">
              <img src="@/assets/avatarprofile.svg" alt="Dropdown trigger" />
            </div>
            <div class="dropdown-menu" v-bind:class="{ show: isDropdownVisible }">
              <router-link v-if="isLoggedIn" class="dropdown-item" :to="{ name: 'Panel' }">
                Panel
              </router-link>
              <a v-if="!isLoggedIn" role="button" class="dropdown-item" @click="this.loginModal.show()">Log in</a>
              <a v-if="!isLoggedIn" role="button" class="dropdown-item" @click="this.registerModal.show()">Register</a>
              <a v-if="isLoggedIn" role="button" class="dropdown-item" @click.prevent="signOut">Sign out</a>
            </div>
          </li>
        </ul>
      </div>
    </nav>
  </div>

  <login-modal @get-modal="(modal) => { this.loginModal = modal }"/>
  <register-modal @get-modal="(modal) => { this.registerModal = modal }"/>
</template>

<script>
import LoginModal from "@/components/modals/LoginModal";
import RegisterModal from "@/components/modals/RegisterModal";
import {MethodRequest} from "@/communication/Network.ts";

export default {
  components: {
    LoginModal,
    RegisterModal
  },
  data() {
    return {
      isDropdownVisible: false,
      isLoggedIn: MethodRequest.isTokenAvailable(),
      user: MethodRequest.getUser(),
      loginModal: null,
      registerModal: null,
      token: false,
    };
  },
  methods: {
    toggleDropdown() {
      this.isDropdownVisible = !this.isDropdownVisible;
    },
    signOut() {
      MethodRequest.userLogout();
      this.$router.push({ name: 'Products' });
      window.location.reload()
    },
  }
};
</script>

<style scoped>
img {
  width: 60px;
}

.dropdown {
  margin-right: 4em;
}

.dropdown-menu {
  position: fixed;
  transform: translate(-30%, 0%);
}
</style>
