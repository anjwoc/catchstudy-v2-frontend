<template>
  <v-container>
    <div v-if="post">
      <post-page :post="post" :me="me" :relatedPosts="relatedPosts"></post-page>
    </div>
  </v-container>
</template>

<script>
  import PostPage from "@/components/PostPage";
  import CommentForm from "@/components/CommentForm";
  import CommentContent from "@/components/CommentContent";
  import { mapState } from "vuex";
  export default {
    layout: "post",
    components: {
      PostPage,
      CommentForm,
      CommentContent,
    },
    middleware({ store, params }) {
      return Promise.all([
        store.dispatch("posts/loadPost", params.id),
        store.dispatch("posts/loadComments", parseInt(params.id)),
      ]);
    },
    computed: {
      ...mapState({
        post: state => state.posts.mainPosts[0],
        me: state => state.users.me,
        relatedPosts: state => state.posts.relatedPosts,
      }),
      parsedContent() {
        const content = this.post.content.replace(/(<([^>]+)>)/gi, "");
        const tags = this.post.hashtags.map(tag => `#${tag.name}`).join(" ");
        return content + " " + tags;
      },
    },
    head() {
      return {
        title: `${this.post.title}`,
        meta: [
          {
            hid: "description",
            name: "description",
            content: this.parsedContent,
          },
          {
            hid: "name",
            name: "name",
            content: this.post.title,
          },
          { hid: "ogtitle", property: "og:title", content: this.post.title },
          {
            hid: "ogdescription",
            property: "og:description",
            content: this.parsedContent,
          },
          { hid: "image", property: "og:image", content: this.post.coverImg },
          { hid: "ogimage", property: "og:image", content: this.post.coverImg },
          { hid: "ogimage", property: "og:image", content: this.post.coverImg },
          {
            hid: "ogurl",
            property: "og:url",
            content: `https://www.catchstudy.online/post/${this.post.id}`,
          },
        ],
      };
    },
  };
</script>

<style lang="scss" scoped></style>
