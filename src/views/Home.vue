<template>
  <div class="home">
    <div class="hero bg-grey-light mb-24">
      <div class="container flex justify-between py-10">
        <div class="mt-10">
          <h1 class="w-3/4 mb-4">Book recommendation site built with GraphQL</h1>
          <p class="leading-normal w-3/4 mb-6">Built with Laravel (Lighthouse GraphQL), Vue.js (vue-apollo) and Tailwind CSS</p>
          <div class="flex items-center">
            <a href="#" class="bg-purple-dark text-white rounded px-4 py-4 mr-4 hover:bg-purple">View Books</a>
            <a href="#" class="border border-purple-dark border-solid rounded text-purple-dark px-4 py-4 hover:bg-purple hover:text-white">View Screencasts </a>
          </div>
        </div>
        <div>
          <img src="../assets/hero.svg" alt="hero">
        </div>
      </div>
    </div> <!-- end hero -->

    <div class="container">
      <div class="flex flex-wrap -mx-4">
        <div class="w-1/4 px-4 mb-12">
          <ApolloQuery :query="categoriesQuery">
            <template slot-scope="{ result: { data, loading }, isLoading }">
              <div v-if="isLoading">Loading...</div>
              <ul v-else class="list-reset text-lg">
                <li class="mb-6">
                  <a href="#" class="text-black hover:text-grey-darkest" @click.prevent="selectCategory('all')">All</a>
                </li>
                <li class="mb-6">
                  <a href="#" class="text-black hover:text-grey-darkest" @click.prevent="selectCategory('featured')">Featured</a>
                </li>
                <li v-for="category of data.categories" :key="category.id"
                     @click.prevent="selectCategory(category.id)" class="mb-6">
                  <a href="#" class="text-black hover:text-grey-darkest">{{ category.name }}</a>
                </li>
                <li class="mb-6">
                  <router-link to="/books/add" class="text-black hover:text-grey-darkest">Add a book</router-link>
                </li>
              </ul>
            </template>
          </ApolloQuery>
        </div>

        <div class="w-3/4 px-4 mb-12">
          <div>
            <ApolloQuery :query="query" v-if="selectedCategory === 'all'">
              <template slot-scope="{ result: { data, loading }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else class="flex flex-wrap">
                  <div v-for="book of data.books" :key="book.id" class="w-1/3 px-4 mb-12">
                    <book-listing :book="book"></book-listing>
                  </div>
                </div>
              </template>
            </ApolloQuery>

            <ApolloQuery :query="query" :variables="{ featured: true }" v-else-if="selectedCategory === 'featured'">
              <template slot-scope="{ result: { data, loading }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else class="flex flex-wrap">
                  <div v-for="book of data.booksByFeatured" :key="book.id" class="w-1/3 px-4 mb-12">
                    <book-listing :book="book"></book-listing>
                  </div>
                </div>
              </template>
            </ApolloQuery>

            <ApolloQuery :query="query" :variables="{ id: selectedCategory }" v-else>
              <template slot-scope="{ result: { data, loading }, isLoading }">
                <div v-if="isLoading">Loading...</div>
                <div v-else class="flex flex-wrap">
                  <div v-for="book of data.category.books" :key="book.id" class="w-1/3 px-4 mb-12">
                    <book-listing :book="book"></book-listing>
                  </div>
                </div>
              </template>
            </ApolloQuery>
          </div>
        </div>
      </div>
    </div>




    <div v-if="selectedCategory === 'all'">
      <ApolloQuery :query="query">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div :to="`/books/${book.id}`" v-for="book of data.books" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}. {{ book.title }}
              </router-link>
              <div>{{ book.author }}</div>
              <img :src="`http://lar-gql-books/img/${book.image}`" alt="cover image">
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>

    <div v-else-if="selectedCategory === 'featured'">
      <ApolloQuery :query="query" :variables="{ featured: true }">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div v-for="book of data.booksByFeatured" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}. {{ book.title }}
              </router-link>
              <div>{{ book.author }}</div>
              <img :src="`http://lar-gql-books/img/${book.image}`" alt="cover image">
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>

    <div v-else>
      <ApolloQuery :query="query" :variables="{ id: selectedCategory }">
        <template slot-scope="{ result: { data, loading }, isLoading }">
          <div v-if="isLoading">Loading...</div>
          <div v-else>
            <div v-for="book of data.category.books" :key="book.id">
              <router-link :to="`/books/${book.id}`">
                {{ book.id }}. {{ book.title }}
              </router-link>
              <div>{{ book.author }}</div>
              <img :src="`http://lar-gql-books/img/${book.image}`" alt="cover image">
            </div>
          </div>
        </template>
      </ApolloQuery>
    </div>
  </div>
</template>

<script>
// import gql from 'graphql-tag'
import categoryQuery from '@/graphql/queries/Category.gql'
import categoriesQuery from '@/graphql/queries/Categories.gql'
import booksQuery from '@/graphql/queries/Books.gql'
import booksFeaturedQuery from '@/graphql/queries/BooksFeatured.gql'
import bookListing from '@/components/BookListing.vue'

export default {
  name: 'home',
  components: {
    bookListing
  },
  data() {
    return {
      categoryQuery,
      categoriesQuery,
      booksQuery,
      booksFeaturedQuery,
      selectedCategory: 'all',
      query: booksQuery,
      categories: []
    }
  },
  methods: {
    selectCategory(category) {
      if (category === 'all') {
        this.query = booksQuery
      } else if (category === 'featured') {
        this.query = booksFeaturedQuery
      } else {
        this.query = categoryQuery
      }

      this.selectedCategory = category
    }
  }
}
</script>

<style>
  .link-margin {
    margin-right: 24px;

  }
</style>
