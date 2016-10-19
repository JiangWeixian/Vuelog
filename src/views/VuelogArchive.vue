<template>
  <div class="archive">
    <div v-if="displayType === 'archive-category'" class="archive-body">
      <h2>Posts in category <q v-text="archive.title"></q>:</h2>
      <ul>
        <li v-for="post in archive.posts">
          <router-link :to="{name: 'post', params: {category: post.category, slug: post.slug, year: post.year}}" v-text="post.title"></router-link>
          <span v-text="' ( ' + post.date + ' )'"></span>
        </li>
        <li v-if="archive.posts.length === 0">No posts found.</li>
      </ul>
    </div>

    <div v-if="displayType === 'archive-year'" class="archive-body">
      <h2 v-text="'Posts in year ' + archive.year + ':'"></h2>
      <ul>
        <li v-for="post in archive.posts">
          <router-link :to="{name: 'post', params: {category: post.category, slug: post.slug, year: archive.year}}" v-text="post.title"></router-link>
          <span> ( </span>
          <router-link :to="{name: 'category', params: {category: post.category}}" v-text="post.categoryTitle"></router-link>
          <span> )</span>
        </li>
        <li v-if="archive.posts.length === 0">No posts found.</li>
      </ul>
    </div>

    <div v-if="displayType === 'archive'" class="archive-body">
      <h2>Posts by Category:</h2>
      <ul>
        <li v-for="category in archive.postsByCategory">
          <h4>
            <router-link :to="{name: 'archive-category', params: {category: category.slug}}" v-text="category.title"></router-link>
            <span v-text="' (' + category.posts.length + ')'"></span>
          </h4>
          <ul>
            <li v-for="post in category.posts">
              <router-link :to="{name: 'post', params: {category: post.category, slug: post.slug, year: post.year}}" v-text="post.title"></router-link>
              <span v-text="' ( ' + post.date + ' )'"></span>
            </li>
          </ul>
        </li>
      </ul>

      <h2>Posts by Year:</h2>
      <ul>
        <li v-for="year in archive.postsByYear">
          <h4>
            <router-link :to="{name: 'archive-year', params: {year: year.year}}" v-text="year.year"></router-link>
            <span v-text="' (' + year.posts.length + ')'"></span>
          </h4>
          <ul>
            <li v-for="post in year.posts">
              <router-link :to="{name: 'post', params: {category: post.category, slug: post.slug, year: post.year}}" v-text="post.title"></router-link>
              <span> ( </span>
              <router-link :to="{name: 'category', params: {category: post.category}}" v-text="post.categoryTitle"></router-link>
              <span> )</span>
            </li>
          </ul>
        </li>
      </ul>

      <h2>Pages:</h2>
      <ul>
        <li v-for="page in archive.pages">
          <router-link :to="{name: 'page', params: {page: page.slug}}" v-text="page.title"></router-link>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'vuelog-archive-view',

    computed: {
      displayType () {
        return this.$route.name
      },

      postsByCategory () {
        return this.$store.getters.postsByCategory
      },

      postsByYear () {
        return this.$store.getters.postsByYear
      },

      pages () {
        return this.$store.getters.pages
      },

      archive () {
        switch (this.displayType) {
          case 'archive-category':
            return this.getPostsInCategory(this.$route.params.category)
          case 'archive-year':
            return this.getPostsInYear(+this.$route.params.year)
          case 'archive':
            return this.getAllPostsAndPages()
        }
      }
    },

    methods: {
      oops () {
        this.$router.replace('/oops')
      },

      getPostsInCategory (slug) {
        for (var i = 0; i < this.postsByCategory.length; i++) {
          if (this.postsByCategory[i].slug === slug) {
            return this.postsByCategory[i]
          }
        }
        this.oops()
      },

      getPostsInYear (year) {
        if (Number.isNaN(year)) {
          this.oops()
          return
        }
        for (var i = 0; i < this.postsByYear.length; i++) {
          if (this.postsByYear[i].year === year) {
            return this.postsByYear[i]
          }
        }
        return {
          year,
          posts: []
        }
      },

      getAllPostsAndPages () {
        return {
          postsByCategory: this.postsByCategory,
          postsByYear: this.postsByYear,
          pages: this.pages
        }
      }
    },

    created () {
      switch (this.displayType) {
        case 'archive-category':
          this.$store.dispatch('DOCUMENT_TITLE', 'Archive | ' + this.archive.title)
          return
        case 'archive-year':
          this.$store.dispatch('DOCUMENT_TITLE', 'Archive | ' + this.archive.year)
          return
        case 'archive':
          this.$store.dispatch('DOCUMENT_TITLE', 'Archive')
          return
      }
    }
  }
</script>

<style lang="stylus" scoped>
  .archive-enter-active
  .archive-leave-active
    transition opacity .3s ease

  .archive-enter
  .archive-leave-active
    opacity 0

  ul
    line-height 1.6em
    padding-left 1.6em

  h4
  span
    color #7f8c8d
</style>