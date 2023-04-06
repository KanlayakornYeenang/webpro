<template>
  <div class="container is-widescreen">
    <section class="hero">
      <div class="hero-body">
        <p class="title">{{ blog.title }}</p>
        <section class="section" id="app">
          <div class="content">
            <div class="card has-background-light">
              <div class="card-image pt-5">
                <div class="columns">
                  <div class="column" v-for="image in images" :key="image.id">
                    <figure class="image">
                      <img
                        :src="'http://localhost:3000/' + image.file_path"
                        alt="Placeholder image"
                      />
                    </figure>
                  </div>
                </div>
              </div>
              <div class="card-content">
                <div class="content">{{ blog.content }}</div>
                <div class="container">
                  <p class="subtitle">Comments</p>
                  <div
                    class="box"
                    v-for="comment in comments"
                    :key="comment.id"
                  >
                    <article class="media">
                      <div class="media-left">
                        <figure class="image is-64x64">
                          <img
                            :src="
                              comment.file_path
                                ? 'http://localhost:3000' + comment.file_path
                                : 'https://bulma.io/images/placeholders/128x128.png'
                            "
                            alt="Placeholder image"
                          />
                        </figure>
                      </div>
                      <div class="media-content">
                        <div class="content">
                          <p>{{ comment.comment }}</p>
                          <p class="is-size-7">{{ comment.comment_date }}</p>
                        </div>
                        <nav class="level is-mobile">
                          <div class="level-left">
                            <a class="level-item" aria-label="like">
                              <span class="icon is-small">
                                <i class="fas fa-heart" aria-hidden="true"></i>
                              </span>
                            </a>
                          </div>
                        </nav>
                      </div>
                    </article>
                  </div>
                  <div class="columns box">
                    <div class="column is-7">
                      <input
                        class="input"
                        type="text"
                        name="comment"
                        placeholder="Comment here..."
                        v-model="comment"
                      />
                    </div>
                    <div class="column is-3">
                      <div class="file">
                        <label class="file-label">
                          <input
                            type="file"
                            class="file-input"
                            name="comment_image"
                            ref="file"
                            id="file"
                            @change="handleFileUpload"
                          />
                          <span class="file-cta">
                            <span class="file-icon">
                              <i class="fas fa-upload"></i>
                            </span>
                            <span class="file-label"> Choose an image… </span>
                          </span>
                        </label>
                      </div>
                    </div>
                    <div class="column is-2">
                      <input
                        class="button is-primary"
                        type="button"
                        value="Submit"
                        @click="addComment()"
                      />
                    </div>
                  </div>
                </div>
              </div>
              <footer class="card-footer">
                <a class="card-footer-item" href="/">To Home Page</a>
              </footer>
            </div>
          </div>
        </section>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      blog: null,
      images: null,
      comments: null,
      comment: null,
      file: null,
    };
  },
  beforeCreate() {
    axios
      .get("http://localhost:3000/blogs/" + this.$route.path.split("/")[2])
      .then((response) => {
        this.blog = response.data.blog;
        this.images = response.data.images;
        this.comments = response.data.comments;
      })
      .catch((err) => {
        console.log(err);
      });
  },
  methods: {
    handleFileUpload() {
      this.file = this.$refs.file.files[0];
    },
    addComment() {
      var formData = new FormData();
      formData.append("comment_image", this.file);
      formData.append("comment", this.comment);
      axios
        .post(
          "http://localhost:3000/blogs/addcomment/" +
            this.$route.path.split("/")[2],
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          }
        )
        .then(() => {
          location.reload();
        })
        .catch((error) => {
          console.log(error.message);
        });
    },
  },
};
</script>