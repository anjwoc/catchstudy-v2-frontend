<template>
  <v-container class="title_form">
    <div class="title_box">
      <h3>스터디 등록</h3>
      <div class="steps">
        <li class="pink--text font-weight-bold">
          <b>01</b>
          기본정보
        </li>
        <img src="/icon_next.png" alt="" />
      </div>
    </div>
    <v-form ref="form" v-model="valid" @submit.prevent="onSubmitForm">
      <div class="box">
        <div class="box-title">
          <span>스터디분야</span>
          <b class="pink--text font-weight-black">*</b>
        </div>
        <div class="content">
          <v-select
            v-model="category"
            full-wdith
            hide-details
            :items="categorys"
            outlined
          ></v-select>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>스터디등록지역</span>
          <b class="pink--text font-weight-black">*</b>
        </div>
        <div class="content">
          <v-select
            v-model="location"
            full-wdith
            hide-details
            :items="locations"
            outlined
          ></v-select>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>스터디 방식</span>
          <b class="pink--text font-weight-black">*</b>
        </div>
        <div class="content">
          <v-radio-group v-model="studyType" row>
            <v-radio label="온라인" value="online"></v-radio>
            <v-radio label="오프라인" value="offline"></v-radio>
          </v-radio-group>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>스터디 참여 인원</span>
          <b class="pink--text font-weight-black">*</b>
        </div>
        <div class="content">
          <v-select
            v-model="minPeople"
            hide-details
            label="최소"
            :items="Array.from({ length: 20 }, (_, i) => i + 1)"
            outlined
          ></v-select>
          <v-select
            v-model="maxPeople"
            hide-details
            label="최대"
            :items="Array.from({ length: 20 }, (_, i) => i + 1)"
            outlined
          >
          </v-select>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>스터디 제목</span>
          <b class="pink--text font-weight-black">*</b>
        </div>

        <div class="content">
          <v-text-field
            v-model="title"
            class="my-1"
            hide-details
            dense
            outlined
          ></v-text-field>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>게시글 본문</span>
          <b class="pink--text font-weight-black">*</b>
        </div>

        <div>
          <ClientOnly>
            <tiptap-vuetify
              v-model="content"
              :extensions="extensions"
              style="width: 100%"
            />
            <template #placeholder>
              <v-progress-circular
                indeterminate
                color="main"
              ></v-progress-circular>
            </template>
          </ClientOnly>
        </div>
      </div>
      <div class="box">
        <div class="box-title">
          <span>커버 이미지</span>
        </div>

        <div class="content">
          <v-file-input
            v-model="files"
            @change="uploadCoverImage"
            color="deep-purple accent-4"
            counter
            label="File input"
            placeholder="Select your files"
            prepend-icon="mdi-paperclip"
            outlined
            accept="image/png, image/jpeg, image/jpg"
            :show-size="1000"
          >
          </v-file-input>
          <div class="ml-9 details caption pink--text">
            커버 이미지를 등록하지 않으면 기본 이미지로 적용 됩니다.
          </div>
          <div>
            {{ coverImg }}
          </div>
        </div>
      </div>

      <div class="box">
        <div class="box-title">
          <span>해시태그</span>
        </div>

        <div class="content vertical-center">
          <vue-input-tag v-model="hashtags" class="hashtag" />
        </div>
      </div>
    </v-form>

    <div class="pa-6 mt-10">
      <v-row justify="center">
        <v-btn
          class="pl-10 pr-10 mr-4 grey darken-2 white--text"
          x-large
          to="/"
        >
          돌아가기
        </v-btn>
        <v-btn
          class="pl-10 pr-10 pink white--text"
          x-large
          @click="onSubmitForm"
        >
          수정하기
        </v-btn>
      </v-row>
    </div>
    <div style="height: 400px"></div>
  </v-container>
</template>

<script>
  import {
    TiptapVuetify,
    Heading,
    Bold,
    Italic,
    Strike,
    Underline,
    Code,
    Paragraph,
    BulletList,
    OrderedList,
    ListItem,
    Link,
    Blockquote,
    HardBreak,
    HorizontalRule,
    History,
    Image,
  } from 'tiptap-vuetify';
  export default {
    components: {
      TiptapVuetify,
    },
    middleware({ store, dispatch, params }) {
      return store.dispatch('posts/loadUpdatePost', params.id);
    },
    data() {
      return {
        valid: false,
        content: '',
        category: '',
        coverImagePath: '',
        location: '',
        studyType: null,
        minPeople: '',
        maxPeople: '',
        title: '',
        hashtags: [],
        files: null,
        categorys: ['어학', '취업', '고시', '자격증', '프로그래밍', '기타'],
        locations: [
          '서울',
          '수원',
          '인천',
          '부산',
          '강원',
          '천안',
          '울산',
          '광주',
          '제주',
          '기타',
        ],
        extensions: [
          History,
          Blockquote,
          Link,
          Underline,
          Strike,
          Italic,
          ListItem,
          BulletList,
          OrderedList,
          [
            Heading,
            {
              options: {
                levels: [1, 2, 3],
              },
            },
          ],
          Bold,
          Link,
          Code,
          HorizontalRule,
          Paragraph,
          HardBreak,
          Image,
        ],
      };
    },
    methods: {
      uploadCoverImage() {
        if (this.files.length === 0) return;
        const formData = new FormData();
        // formData.append('postId', postId);
        formData.append('image', this.files[0]);

        this.$axios
          .post(`/post/thumbnail`, formData, {
            withCredentials: true,
          })
          .then(path => {
            this.coverImagePath = path.data;
          })
          .catch(err => {
            console.error(err);
          });
      },
      onSubmitForm() {
        if (this.$refs.form.validate()) {
          this.$store
            .dispatch('posts/update', {
              id: this.post.id,
              title: this.title,
              content: this.content,
              hashtags: this.hashtags,
              coverImg: this.coverImg,
              numPeople: this.numPeople,
              hashtags: this.hashtags,
              type: this.studyType,
              location: this.location,
              category: this.category,
            })
            .then(postId => {
              this.content = '';
              this.$router.push({ path: `/post/${postId}` });
            })
            .catch(err => {
              console.error(err);
            });
        }
      },
    },
    computed: {
      numPeople() {
        return this.minPeople + '-' + this.maxPeople;
      },
      post() {
        return this.$store.state.posts.mainPosts.find(
          v => v.id === parseInt(this.$route.params.id, 10),
        );
      },
      coverImg() {
        return this.coverImagePath !== ''
          ? this.coverImagePath
          : process.env.default_cover;
      },
    },
    created() {
      const tags = this.post.hashtags.map(h => h.name);
      const [minNum, maxNum] = this.post.numPeople.split('-');
      this.hashtags = tags;
      this.tagHistory = tags;
      this.content = this.post.content;
      this.title = this.post.title;
      this.category = this.post.category;
      this.location = this.post.location;
      this.minPeople = minNum;
      this.maxPeople = maxNum;
    },
  };
</script>

<style lang="scss" scoped>
  .hashtag {
    width: 100%;
    border-radius: 5px;
    border-color: rgba(0, 0, 0, 0.3);
    &:hover {
      border-color: rgba(0, 0, 0, 0.87);
    }
  }
</style>
