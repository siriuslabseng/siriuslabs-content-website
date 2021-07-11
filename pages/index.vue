<template>
  <div>
    <nav class="navbar navbar-light bg-light">
      <div class="container-fluid">
        <a 
          class="navbar-brand" 
          href="#">SLC</a>
      </div>
    </nav>
    <section class="container-md">
      <h1 class="display-4 mt-5 mb-5">SiriusLabs Content</h1>

      <div class="actions mb-5">
        <form class="d-flex">
          <input 
            v-model="search" 
            class="form-control me-2"
            type="text" 
            placeholder="Search"
            aria-label="Search">
          <button 
            class="search-btn btn btn-outline-primary" 
            type="submit"
            @click="clearSearch()">Clear</button>
        </form>
        <button 
          class="btn btn-outline-primary push" 
          @click="sort('date')">Sort By Date</button>
        <button 
          class="btn btn-outline-primary" 
          @click="sort('title')">Sort By Alphabet</button>
      </div>
      <div class="posts-container row row-cols-1 row-cols-md-3 g-4">
        <div  
          v-for="post of filteredPosts" 
          :key="post.id" 
          class="col post mb-5">
          <div class="card">
            <img 
              :src="post.og.image"
              class="card-img-top" 
              alt="">
            <div class="card-body">
              <h5 class="card-title">{{ post.og.title }}</h5>
              <p class="card-text">{{ post.og.description }}</p>
              <a 
                :href="post.message"
                target="_blank"
                class="btn btn-outline-primary">Link</a>
            </div>
            <div class="card-footer">
              <small class="text-muted">{{ post.author }}</small> <br>
              <!-- <small class="text-muted">{{ $moment(post.date).format('dddd, MMMM Do YYYY') }}</small> <br> -->
              <!-- <small class="text-muted ml-4">{{ $moment(post.date).format('h:mm:ss a') }}</small> <br> -->
              <small class="text-muted type-tag">{{ post.og.site_name }} – {{ post.og.type }} </small>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import posts from '~/static/siriuslabs-data.json'


export default {
  components: {
  }, 
  
  data() {
    return {
      posts:[],
      currentSort:'date',
      currentSortDir:'desc',
      search: ''
    }
  },
  asyncData ({ params }) {
    return { posts }
  },
  computed: {
    filteredPosts() {
      let tempPosts = this.posts; 
      // process search input
      if (this.search !== '' && this.search) {
        tempPosts = tempPosts.filter((post) => {
          if (post.og.title) {
            return post.og.title.toUpperCase().includes(this.search.toUpperCase())
          }
        })
      }

      tempPosts = tempPosts.sort((a,b) => {
        let modifier = 1;
        if(this.currentSortDir === 'desc') modifier = -1;
        if ( this.currentSort !== 'title' ) {
          if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
          if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        } else {
          if(a.og.title < b.og.title) return -1 * modifier;
          if(a.og.title > b.og.title) return 1 * modifier;
        }
        return 0;
      });
      return tempPosts;
    },
  }, 
  methods: {
    clearSearch() {
      this.search = ''; 
    },
    updateArray() {
      this.sortedPosts = this.filteredPosts
    },
    sort(sortType) {
      //if sortType == current sort, reverse
      if (sortType === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc'?'desc':'asc';
      }
      this.currentSort = sortType;
      console.log('should work ', this.posts[0][this.currentSort])
    }
  },
  }
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.actions {
  display: flex;
  width: 100%; 
  justify-content: space-between;
}

.actions * {
  height: 40px; 
}

.actions .search-btn {
  border-radius: 0 0.25rem 0.25rem 0;
}

.actions input {
  border-radius: 0.25rem 0 0 0.25rem;
}

.actions .push {
  margin-left: auto;
  margin-right: 16px;
}

.type-tag {
  text-transform: uppercase;
  font-weight: 900;
  opacity: 0.75;
}

</style>

// for sorting and searching
// https://codepen.io/thaekeh/pen/PoGJRKQ