<template>
  <Layout >
    <template slot="additional-headings">
      <BackButton :text="'home'" :location="'/'" />
    </template>
    <Author :show-title="true" />
    <div class="projects">
      <ProjectCard v-for="edge in $page.projects.edges" :key="edge.node.id" :project="edge.node"/>
    </div>
  </Layout>
</template>

<page-query>
query {
projects: allProject {
    edges {
      node {
        id
        title
        description
        cover_image (width: 770, height: 380, blur: 10)
        path
      }
    }
  }
}
</page-query>

<script>
import Author from '~/components/Author.vue'
import ProjectCard from '~/components/ProjectCard.vue'
import BackButton from '~/components/NavBackButton.vue'

export default {
  components: {
    Author,
    ProjectCard,
    BackButton
  },
  metaInfo: {
    title: 'Projects'
  }
}
</script>
