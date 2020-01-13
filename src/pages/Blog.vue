<template>
  <Layout >
    <template slot="additional-headings">
      <BackButton :text="'home'" :location="'/'" />
    </template>
    <h1 id="page-title">blog</h1>
    <Author :show-title="false" />
    <div class="posts">
      <PostCard v-for="edge in $page.posts.edges" :key="edge.node.id" :post="edge.node"/>
    </div>
  </Layout>
</template>

<page-query>
query {
  posts: allPost(filter: { published: { eq: true }}) {
    edges {
      node {
        id
        title
        date (format: "D. MMMM YYYY")
        timeToRead
        description
        cover_image (width: 770, height: 380, blur: 10)
        path
        tags {
          id
          title
          path
        }
      }
    }
  }
}
</page-query>

<script>
import Author from '~/components/Author.vue'
import PostCard from '~/components/PostCard.vue'
import BackButton from '~/components/NavBackButton.vue'

export default {
  components: {
    Author,
    PostCard,
    BackButton
  },
  metaInfo: {
    title: 'Blog'
  }
}
</script>
