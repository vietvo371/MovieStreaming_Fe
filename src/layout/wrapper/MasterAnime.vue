<template>
    <div id="preloder" v-if="isLoading">
        <div class="loader"></div>
    </div>
    <MenuAnime></MenuAnime>
    <div :class="isMobile < 768 ? 'container-fluid' : ''" style="margin-top: 69px;">
        <router-view></router-view>
    </div>
    <FooterAnime></FooterAnime>
</template>
<script>
import FooterAnime from '../components/FooterAnime.vue';
import MenuAnime from '../components/MenuAnime.vue';
// import '../../assets/assets_Anime/js/jquery-3.3.1.min.js';
// import '../../assets/assets_Anime/js/bootstrap.min.js';
// import '../../assets/assets_Anime/js/player.js';
// import '../../assets/assets_Anime/js/jquery.nice-select.min.js';
// import '../../assets/assets_Anime/js/mixitup.min.js';
// import '../../assets/assets_Anime/js/jquery.slicknav.js';
// import '../../assets/assets_Anime/js/owl.carousel.min.js';
// import '../../assets/assets_Anime/js/main.js';
// import '../../assets/assets_Rocker/js/bootstrap.bundle.min.js';
import { mapState } from 'vuex'; // Thêm import này
import * as bootstrap from 'bootstrap';

export default {
    name: "app",
    components: {
        MenuAnime,
        FooterAnime
    },
    data() {
        return {
            isMobile: window.innerWidth,
        }
    },
    computed: {
        ...mapState(['isLoading']), // Lấy isLoading từ store
    },
    mounted() {
        // Khởi tạo tất cả các dropdown
        document.querySelectorAll('[data-bs-toggle="dropdown"]').forEach(element => {
            new bootstrap.Dropdown(element);
        });

        // Khởi tạo tất cả các modal
        document.querySelectorAll('[data-bs-toggle="modal"]').forEach(element => {
            new bootstrap.Modal(element);
        });
    },
    created() {
        window.addEventListener('resize', this.handleResize);

        this.$router.beforeEach((to, from, next) => {
            this.$store.dispatch('showLoader'); // Hiển thị loader khi chuyển trang
            next();
        });

        this.$router.afterEach(() => {
            this.$store.dispatch('hideLoader'); // Ẩn loader sau khi trang đã tải
        });
        this.$nextTick(() => {
            this.initializeBootstrapComponents();
        });

    },
    methods: {
        handleResize() {
            this.isMobile = window.innerWidth;
        },
        loadScripts() {
            const scripts = [
                'https://code.jquery.com/jquery-3.3.1.min.js',
                '/assets/assets_Anime/js/bootstrap.min.js',
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
                '/assets/assets_Anime/js/player.js',
                '/assets/assets_Anime/js/jquery.nice-select.min.js',
                '/assets/assets_Anime/js/mixitup.min.js',
                '/assets/assets_Anime/js/jquery.slicknav.js',
                '/assets/assets_Anime/js/owl.carousel.min.js',
                '/assets/assets_Anime/js/main.js'
            ];
            
            secondaryScripts.forEach(src => {
                const script = document.createElement('script');
                script.src = src;
                script.async = true;
                document.head.appendChild(script);
            });

            // Initialize Bootstrap components
            this.$nextTick(() => {
                this.initializeBootstrapComponents();
            });
        },

        initializeBootstrapComponents() {
            // Ensure Bootstrap is loaded
            if (typeof bootstrap !== 'undefined') {
                // Initialize all dropdowns
                document.querySelectorAll('.dropdown-toggle').forEach(dropdownToggle => {
                    new bootstrap.Dropdown(dropdownToggle, {
                        autoClose: true
                    });
                });
            } else {
                console.warn('Bootstrap is not loaded');
                // Retry after a short delay
                setTimeout(() => this.initializeBootstrapComponents(), 500);
            }
        }
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.handleResize);
    }
}
</script>
<style>
/* @import '../../assets/assets_Rocker/css/bootstrap.min.css'; */


@import 'bootstrap/dist/css/bootstrap.min.css';
@import '../../assets/assets_Anime/css/bootstrap.min.css';
@import '../../assets/assets_Anime/css/font-awesome.min.css';
@import '../../assets/assets_Anime/css/elegant-icons.css';
@import '../../assets/assets_Anime/css/plyr.css';
@import '../../assets/assets_Anime/css/nice-select.css';
@import '../../assets/assets_Anime/css/owl.carousel.min.css';
@import '../../assets/assets_Anime/css/slicknav.min.css';
@import '../../assets/assets_Anime/css/style.css';
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300;400;500;600;700&display=swap');
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css');
/* @import url('https://cdnjs.cloudflare.com/ajax/libs/cloudinary-core/2.11.0/cloudinary-core-shrinkwrap.min.js'); */
</style>
