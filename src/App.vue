<script>
import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      base_api_url: 'http://127.0.0.1:8000',
      posts_endpoint: '/api/posts',
      posts: null,
      search_text: '',
      name: '',
      email: '',
      message: '',
      success: false,
      errors: false,
      loading: false

    }
  },
  methods: {



    /* Contact form */
    submitMessage() {
      this.loading = true;
      // create the payload for the post request
      const payload = {
        name: this.name,
        email: this.email,
        message: this.message
      }

      console.log(payload);
      // send a post request 
      axios.post('http://127.0.0.1:8000/api/contacts', payload)
        .then(response => {

          console.log(response);
          this.loading = false;

          if (response.data.success) {
            this.success = 'Thanks for your message'
            this.errors = false
            this.name = ''
            this.email = ''
            this.message = ''

          } else {
            // you have validation errors to handle
            console.log(response);
            this.errors = response.data.errors
            this.success = false
          }


        })
        .catch(err => {
          console.log(err);
        })
      // handle the response

    },

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

  <button class="btn btn-link position-fixed end-0" type="button" data-bs-toggle="offcanvas"
    data-bs-target="#contactForm" aria-controls="contactForm">
    Contacts
  </button>

  <div class="offcanvas offcanvas-end" data-bs-backdrop="static" tabindex="-1" id="contactForm"
    aria-labelledby="staticBackdropLabel">
    <div class="offcanvas-header">
      <h5 class="offcanvas-title" id="staticBackdropLabel">
        Get in Touch
      </h5>
      <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
    </div>
    <div class="offcanvas-body">
      <div>

        <div class="alert alert-success" role="alert" v-if="success">
          <strong>Success</strong> {{ success }}
        </div>


        <div class="alert alert-danger" role="alert" v-if="errors">
          <strong>There are erros!</strong>
          <ul>
            <li v-for="error in errors">
              {{ error[0] }}
            </li>
          </ul>
        </div>



        <p class="lead">
          Contact me I'll get back to you in a jiffy
        </p>

        <form @submit.prevent="submitMessage()">


          <div class="mb-3">
            <label for="name" class="form-label">name</label>
            <input type="text" class="form-control" name="name" id="name" aria-describedby="nameHelper"
              placeholder="Luke skywalker" v-model="name" />
            <small id="nameHelper" class="form-text text-muted">Type your full name </small>
          </div>


          <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <input type="email" class="form-control" name="email" id="email" aria-describedby="emailHelper"
              placeholder="abc@mail.com" v-model="email" />
            <small id="emailHelper" class="form-text text-muted">Type your full email addrees </small>
          </div>

          <div class="mb-3">
            <label for="message" class="form-label">Message</label>
            <textarea class="form-control" name="message" id="message" rows="5" v-model="message"></textarea>
          </div>


          <button type="submit" class="btn btn-dark" :disabled="loading">
            {{loading ? 'Sending... 📨' : 'Send'}}
          </button>



        </form>

      </div>
    </div>
  </div>


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
              <button type="button" class="btn btn-primary" data-bs-toggle="modal" :data-bs-target="`#post-${post.id}`">
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
