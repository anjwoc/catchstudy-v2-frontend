<template>
  <div>
    <!-- <div>
      <v-carousel
        cycle
        height="300"
        hide-delimiter-background
        show-arrows-on-hover
      >
        <v-carousel-item v-for="(slide, i) in slides" :key="i">
          <v-sheet :color="colors[i]" height="100%">
            <v-row class="fill-height" align="center" justify="center">
              <div class="display-3">{{ slide }} Slide</div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </div> -->

    <v-container>
      <div class="text-center justify-center mt-6 mb-6">
        <div class="text-h5 font-weight-bold">최근 게시글</div>
        <div>
          <v-btn icon @click="$router.push('/recruit')">
            <div class="caption font-weight-bold pink--text">
              전체 게시글 보기
            </div>
            <v-icon class="pink--text">mdi-send</v-icon>
          </v-btn>
        </div>
      </div>
      <v-row>
        <v-col
          v-for="post in mainPosts"
          :key="post.id"
          lg="3"
          md="4"
          sm="6"
          xs="12"
        >
          <v-hover
            v-slot:default="{ hover }"
            :open-delay="openDelay"
            :close-delay="closeDelay"
            :disabled="disabled"
            :value="value"
          >
            <post-card :hover="hover" :post="post" height="200"></post-card>
          </v-hover>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
  import PostCard from "@/components/PostCard";
  import { mapState } from "vuex";
  export default {
    layout: "main",
    middleware({ store, redirect }) {
      return store.dispatch("posts/loadPosts", { reset: true });
    },
    components: {
      PostCard,
    },
    data() {
      return {
        disabled: false,
        openDelay: "0",
        closeDelay: "0",
        value: false,
        colors: [
          "indigo",
          "warning",
          "pink darken-2",
          "red lighten-1",
          "deep-purple accent-4",
        ],
        slides: ["First", "Second", "Third", "Fourth", "Fifth"],
      };
    },
    methods: {
      onScroll() {
        if (
          window.scrollY + document.documentElement.clientHeight >
            document.documentElement.scrollHeight - 300 &&
          this.hasMorePost
        ) {
          this.$store.dispatch("posts/loadPosts");
        }
      },
    },
    computed: {
      ...mapState({
        me: state => state.users.me,
        hasMorePost: state => state.posts.hasMorePost,
        mainPosts: state => state.posts.mainPosts,
      }),
    },
    mounted() {
      window.addEventListener("scroll", this.onScroll);
    },
    beforeDestroy() {
      window.removeEventListener("scroll", this.onScroll);
    },
    head() {
      return {
        metaInfo: {
          title: "전체 게시글",
          titleTemplate: "%s",
          // <html> 요소의 속성 설정
          htmlAttrs: {
            // 주 언어 명시
            lang: "ko-KR",
            dir: "ltr",
          },
          meta: [
            { charset: "utf-8" },
            { hid: "description", name: "description", content: "전체 게시글" },
          ],
        },
      };
    },
  };
</script>
<style scoped></style>
