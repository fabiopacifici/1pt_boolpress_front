<script>
import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      base_api_url: 'http://127.0.0.1:8000',
      posts_endpoint: '/api/posts',
      posts: null,
      search_text: ''
    }
  },
  methods: {


    goTo(url) {
      console.log(url)
      this.callApi(url)
    },


    search() {
      const url = this.base_api_url + this.posts_endpoint + `?search=${this.search_text}`
      console.log(url)
      this.callApi(url)


    },

    callApi(url) {
      axios
        .get(url)
        .then(resp => {
          console.log(resp)
          this.posts = resp.data.results
        })
        .catch(err => {
          console.error(err)
        })
    }
  },
  mounted() {
    const url = this.base_api_url + this.posts_endpoint
    console.log(url)
    this.callApi(url)
  }
}
</script>

<template>

  <div class="p-5 mb-4 bg-light rounded-3">
    <div class="container py-5">
      <h1 class="display-5 fw-bold">Blog</h1>
      <p class="col-md-8 fs-4">
        Read our amazing blog
      </p>


      <form @submit.prevent="search()">
        <div class="input-group mb-3">
          <input type="search" class="form-control" placeholder="search..." v-model="search_text">
          <button class="btn btn-outline-secondary" type="submit">
            <i class="fas fa-search fa-lg fa-fw"></i>
          </button>
        </div>
      </form>


    </div>
  </div>



  <section class="posts py-5" v-if="posts">

    <div class="container">
      <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-4">
        <div class="col" v-for="post in posts.data">
          <div class="card">



            <div v-if="post.cover_image">

              <img class="card-img-top"
                :src="post.cover_image.startsWith('https://') ? post.cover_image : base_api_url + '/storage/' + post.cover_image"
                alt="">

            </div>

            <div v-else>

              <img src='https://picsum.photos/400/200' alt='' />

            </div>

            <div class="card-body">
              {{ post.title }}
            </div>

            <div class="card-footer">
              <!-- Modal trigger button -->
              <button type="button" class="btn btn-primary" data-bs-toggle="modal"
                :data-bs-target="`#post-${post.id}`">
                View
              </button>

              <!-- Modal Body -->
              <!-- if you want to close by clicking outside the modal, delete the last endpoint:data-bs-backdrop and data-bs-keyboard -->
              <div class="modal fade" :id="`post-${post.id}`" tabindex="-1" data-bs-backdrop="static"
                data-bs-keyboard="false" role="dialog" :aria-labelledby="`modal-title-${post.id}`" aria-hidden="true">
                <div class="modal-dialog modal-dialog-scrollable modal-dialog-centered modal-xl" role="document">
                  <div class="modal-content">
                    <div class="modal-header">
                      <h5 class="modal-title" :id="`modal-title-${post.id}`">
                        {{ post.title }}
                      </h5>
                      <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">

                      <div v-if="post.cover_image">

                        <img class="img-fluid w-100"
                          :src="post.cover_image.startsWith('https://') ? post.cover_image : base_api_url + '/storage/' + post.cover_image"
                          alt="">

                      </div>

                      <div v-else>

                        <img src='https://picsum.photos/400/200' alt='' />

                      </div>
                      {{ post.content }}

                    </div>
                    <div class="modal-footer">
                      <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
                        Close
                      </button>
                      <button type="button" class="btn btn-primary">Save</button>
                    </div>
                  </div>
                </div>
              </div>



            </div>
          </div>

        </div>
      </div>

      <nav aria-label="Page navigation" class="mt-4">
        <ul class="pagination">
          <li class="page-item" :class="{ 'disabled': !link.url, 'active': link.active }" v-for="link in posts.links">
            <button class="page-link" :href="link.url" type="button" @click="goTo(link.url)">

              <span v-html="link.label"></span>

            </button>
          </li>



          <!-- <li class="page-item active" aria-current="page">
              <a class="page-link" href="#">1</a>
            </li>
            <li class="page-item"><a class="page-link" href="#">2</a></li>
            <li class="page-item">
              <a class="page-link" href="#">3</a>
            </li>
            <li class="page-item">
              <a class="page-link" href="#" aria-label="Next">
                &raquo
              </a>
            </li> -->
        </ul>
      </nav>
    </div>




  </section>


</template>

<style></style>
