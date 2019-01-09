<template>
  <div class="home">
    <ApolloQuery :query="require('@/graphql/queries/Categories.gql')">
      <template slot-scope="{ result: { data, loading }, isLoading }">
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <a href="#" v-for="category of data.categories" :key="category.id" class="link-margin">
            {{ category.id }}. {{ category.name }}
          </a>
        </div>
      </template>
    </ApolloQuery>
    <ApolloQuery :query="require('@/graphql/queries/Books.gql')">
      <template slot-scope="{ result: { data, loading }, isLoading }">
        <div v-if="isLoading">Loading...</div>
        <div v-else>
          <div v-for="book of data.books" :key="book.id">
            {{ book.id }}. {{ book.title }}
          </div>
        </div>
      </template>
    </ApolloQuery>
  </div>
</template>

<script>

import gql from 'graphql-tag'

export default {
  name: 'home',
  components: {
  },
  data() {
    return {
      categories: []
    }
  },
  apollo: {
    categories: gql`{
      categories {
        id
        name
      }
    }`,
  },
}
</script>

<style>
  .link-margin {
    margin-right: 24px;

  }
</style>
