<template>
  <div id="app">
    <!-- post스타일 -->
    <link
      rel="stylesheet"
      href="https://cdn.rawgit.com/jgthms/bulma/9e1752b5/css/bulma.css"
    />
    <link
      rel="stylesheet"
      href="https://use.fontawesome.com/releases/v5.0.13/css/all.css"
    />
    <link
      rel="stylesheet"
      href="https://cssgram-cssgram.netdna-ssl.com/cssgram.min.css"
    />
    <div class="app-phone">
      <!-- PHONE-HEADER -->
      <div class="phone-header">
        <img
          src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/1211695/vue_gram_logo_cp.png"
        />

        <a class="cancel-cta" v-if="step === 2 || step === 3" @click="goToHome"
          >cancel</a
        >
        <a class="next-cta" v-if="step === 2" @click="step++">Next</a>
        <a class="next-cta" v-if="step === 3" @click="sharePost">share1</a>
      </div>
      <!-- PHONE-BODY -->
      <div class="phone-body">
        <div v-if="step === 1" class="feed">
          <vuegram-post
            v-for="post in Posts"
            :post="post"
            :key="Posts.indexOf(post)"
          />
        </div>
        <!-- step 2 -->
        <div v-if="step === 2">
          <div
            class="selected-image"
            :class="selectedFilter"
            :style="{ backgroundImage: 'url(' + image + ')' }"
          ></div>
          <div class="filter-container">filter-container</div>
        </div>
        <!-- step 3 -->
        <div v-if="step === 3">
          <div
            class="selected-image"
            :class="selectedFilter"
            :style="{ backgroundImage: 'url(' + image + ')' }"
          ></div>
          <div class="caption-container">
            <textarea
              type="text"
              placeholder="write a caption"
              class="caption-input"
              v-model="caption"
            ></textarea>
          </div>
        </div>
      </div>
      <!-- PHONE-FOOTER -->
      <div class="phone-footer">
        <div class="home-cta"><i class="fas fa-home fa-lg"></i></div>
        <div class="upload-cta">
          <input
            type="file"
            name="file"
            id="file"
            class="inputfile"
            @change="uploadImage"
            :disabled="step !== 1"
          />
          <label for="file"><i class="far fa-plus-square fa-lg"></i> </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Posts from "./data/posts";
import Filters from "./data/filters";

import VuegramPost from "./components/VuegramPost.vue";
import EventBus from "./event-bus";

export default {
  name: "App",
  props: {
    post: Object,
  },
  data() {
    return {
      step: 1,
      Posts,
      Filters,
      image: "",
      selectedFilter: "",
      caption: "",
    };
  },
  created() {
    EventBus.$on("filter-selected", (evt) => {
      this.selectedFilter = evt.filter;
    });
    // alert(1);
  },
  methods: {
    uploadImage(evt) {
      const files = evt.target.files;
      if (!files.length) return;

      const reader = new FileReader();
      reader.readAsDataURL(files[0]);
      reader.onload = (evt) => {
        this.image = evt.target.result;
        this.step = 2;
      };

      // To enable reuploading of same files in Chrome
      document.querySelector("#file").value = "";
    },

    sharePost() {
      const post = {
        username: "fullstack_vue",
        userImage:
          "https://s3-us-west-2.amazonaws.com/s.cdpn.io/1211695/vue_lg_bg.png",
        postImage: this.image,
        likes: 0,
        caption: this.caption,
        filter: this.filterType,
      };
      this.Posts.unshift(post);
      this.goToHome();
      console.log(this.image);
    },
    goToHome() {
      this.image = "";
      this.selectedFilter = "";
      this.caption = "";
      this.step = 1;
    },
    like() {
      this.post.hasBeenLiked ? this.post.likes-- : this.post.likes++;
      this.post.hasBeenLiked = !this.post.hasBeenLiked;
    },
  },
  components: {
    "vuegram-post": VuegramPost,
  },
};
</script>

<style src="./assets/app.css"></style>
