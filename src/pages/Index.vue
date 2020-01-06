<template>
  <Layout >
      <template slot="additional-headings">
        <NavLocations />
      </template>
    <Author :show-title="true" />
  </Layout>
</template>

<script>
import Author from '~/components/Author.vue'
import PostCard from '~/components/PostCard.vue'
import NavLocations from '~/components/NavLocations.vue'

export default {
  components: {
    Author,
    PostCard,
    NavLocations
  },
  metaInfo: {
    title: 'Philip Gonzalez'
  }
}
</script>
