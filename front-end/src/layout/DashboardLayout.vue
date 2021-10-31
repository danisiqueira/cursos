<template>
  <div class="wrapper" :class="{ 'nav-open': $sidebar.showSidebar }">
    <side-bar
      :background-color="sidebarBackground"
      short-title="Argon"
      title="Argon"
    >
      <template v-slot:links>
        <sidebar-item
          :link="{
            name: 'Dashboard',
            icon: 'ni ni-tv-2 text-primary',
            path: '/dashboard',
          }"
        />
      </template>
    </side-bar>
    <div class="main-content" :data="sidebarBackground">
      <dashboard-navbar></dashboard-navbar>

      <div @click="toggleSidebar">
        <!-- your content here -->
        <router-view></router-view>
        <content-footer v-if="!$route.meta.hideFooter"></content-footer>
      </div>
    </div>
  </div>
</template>
<script>
import DashboardNavbar from "./DashboardNavbar.vue";
import ContentFooter from "./ContentFooter.vue";

import axios from "axios";
import jwt_decode from "jwt-decode";

export default {
  components: {
    DashboardNavbar,
    ContentFooter,
  },
  data() {
    return {
      sidebarBackground: "vue", //vue|blue|orange|green|red|primary
    };
  },
  mounted() {
    try {
      const token = localStorage.getItem("token");
      const decoded = jwt_decode(token);

      this.profile(decoded.sub);

      // eslint-disable-next-line
      if (Math.floor((new Date).getTime() / 1000) >= decoded.exp) {
        localStorage.removeItem("token");
        this.$router.push("login");
      }
      // valid token format
    } catch (error) {
      //
    }
    if (!localStorage.getItem("token")) {
      this.$router.push("login");
    }
  },
  methods: {
    toggleSidebar() {
      if (this.$sidebar.showSidebar) {
        this.$sidebar.displaySidebar(false);
      }
    },
    profile(id) {
      axios
        .post(
          "http://127.0.0.1:8000/graphql",
          {
            query: `query profile($id:ID){
              me(id:$id){
                name
                email
              }
            }`,
            variables: {
              // eslint-disable-next-line
              /* eslint-disable */
              "id": id,
            },
          },
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
        .then((res) => {
          console.log(res.data.data.me);
          localStorage.setItem("me", res.data.data.me.name);
        })
        .catch((error) => {
          console.log(error);
        });
    }
  },
};
</script>
<style lang="scss"></style>
