<template>
  <div class="wrapper">
    <div class="header-wrapper">
        <MenuRocker></MenuRocker>
         <TopRocker></TopRocker>
    </div>
    <div class="page-wrapper">
      <div class="page-content">
        <router-view></router-view>
      </div>
    </div>
  </div>

  <FooterRocker></FooterRocker>
</template>
<script>
import FooterRocker from "../components/FooterRocker.vue";
import MenuRocker from "../components/MenuRocker.vue";
import TopRocker from "../components/TopRocker.vue";

export default {
  name: "app",
  components: {
    FooterRocker,
    MenuRocker,
    TopRocker,
  },
  mounted() {
    this.$nextTick(() => {
      this.initializeBootstrapComponents();
    });
  },
  created() {
    this.loadScripts();
  },
  methods: {
    loadScripts() {
      const scripts = [
        'https://code.jquery.com/jquery-3.3.1.min.js',
        '/assets/assets_Rocker/js/bootstrap.bundle.min.js'
      ];
      
      let loadedCount = 0;
      const totalScripts = scripts.length;
      
      const loadScript = (index) => {
        if (index >= scripts.length) {
          this.loadSecondaryScripts();
          return;
        }

        const script = document.createElement('script');
        script.src = scripts[index];
        
        script.onload = () => {
          loadedCount++;
          if (loadedCount === totalScripts) {
            this.loadSecondaryScripts();
          }
          loadScript(index + 1);
        };
        
        document.head.appendChild(script);
      };

      loadScript(0);
    },
    
    loadSecondaryScripts() {
      const secondaryScripts = [
        '/assets/assets_Rocker/plugins/simplebar/js/simplebar.min.js',
        '/assets/assets_Rocker/plugins/metismenu/js/metisMenu.min.js',
        '/assets/assets_Rocker/plugins/perfect-scrollbar/js/perfect-scrollbar.js',
        '/assets/assets_Rocker/js/index.js',
        '/assets/assets_Rocker/js/app.js'
      ];
      
      secondaryScripts.forEach(src => {
        const script = document.createElement('script');
        script.src = src;
        script.async = true;
        document.head.appendChild(script);
      });

      this.$nextTick(() => {
        this.initializeBootstrapComponents();
      });
    },

    initializeBootstrapComponents() {
      if (typeof bootstrap !== 'undefined') {
        // Khởi tạo dropdowns
        document.querySelectorAll('.dropdown-toggle').forEach(dropdownToggle => {
          new bootstrap.Dropdown(dropdownToggle, {
            autoClose: true
          });
        });

        // Khởi tạo tooltips
        document.querySelectorAll('[data-bs-toggle="tooltip"]').forEach(tooltip => {
          new bootstrap.Tooltip(tooltip);
        });
      } else {
        console.warn('Bootstrap is not loaded');
        setTimeout(() => this.initializeBootstrapComponents(), 500);
      }
    }
  }
};
</script>
<style>
@import "../../assets/assets_Rocker/plugins/simplebar/css/simplebar.css";
@import "../../assets/assets_Rocker/plugins/perfect-scrollbar/css/perfect-scrollbar.css";
@import "../../assets/assets_Rocker/css/bootstrap.min.css";
@import "../../assets/assets_Rocker/css/bootstrap-extended.css";
@import "../../assets/assets_Rocker/css/app.css";
@import "../../assets/assets_Rocker/css/icons.css";
@import "../../assets/assets_Rocker/css/dark-theme.css";
@import "../../assets/assets_Rocker/css/semi-dark.css";
@import "../../assets/assets_Rocker/css/header-colors.css";
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;500&display=swap");
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css");
</style>