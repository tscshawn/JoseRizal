<template>
  <div class="appContainer">
    <div class="pageContainer">
      <div class="pageHeader">
        <AppHeaderComponent @changePage="scrollToComponent" :isLoggedIn="isLoggedIn" />
          <div class="pageContent">
            <LandingPageComponent />
          </div>
          <div class="pageContent" id="introduction">
            <IntoductionPageComponent />
          </div>
          <div class="pageContent" id="walkingTour">
            <WalkingTour />
          </div>
          <div class="pageContent" id="customTour">
            <CustomTour />
          </div>
          <div class="pageContent" id="articleList">
            <ArticleList />
          <div class="pageContent" id="login">
            <LoginPageComponent @login="handleLogin" @logout="handleLogout" />
          </div>
          <div class="pageContent" id="addEdit" v-if="isLoggedIn">
            <AddEditPageComponent />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import AppHeaderComponent from '../components/AppHeaderComponent.vue';
import IntoductionPageComponent from '../components/IntroductionPageComponent.vue';
import AddEditPageComponent from '../components/AddEditPageComponent.vue';
import LandingPageComponent from '../components/LandingPageComponent.vue'
import LoginPageComponent from '../components/LoginPageComponent.vue'
import ArticleList from '../components/ArticleList.vue';
import WalkingTour from '@/components/WalkingTour.vue';
import CustomTour from '@/components/CustomTour.vue';
</script>

<script>
export default {
  data() {
    return {
      selectedComponent: 'introduction',
      isLoggedIn: false
    }
  },

  mounted(){
  const token = localStorage.getItem('token');
    const username = localStorage.getItem('username');
    if (token && username) {
      this.isLoggedIn = true;
    }
  },

  methods: {
  scrollToComponent(componentId) {
    this.selectedComponent = componentId;
    this.$nextTick(() => {
      const element = document.getElementById(componentId);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
      } else {
        console.error(`Element with ID '${componentId}' not found`);
      }
    });
  },
  handleLogin() {
    this.isLoggedIn = true;
  },
  handleLogout() {
    this.isLoggedIn = false;
  }
}
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Pompiere&display=swap');
</style>

<style scoped>
.appContainer {
  font-family: 'Pompiere', sans-serif;
  font-style: normal;
  color: black;
  background-image: url("@/assets/background.png");
  background-size: cover;
  background-attachment: fixed;
}

.pageContainer {
  padding: 5px;
}
</style>