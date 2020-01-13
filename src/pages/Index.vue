<template>
  <Layout >
      <template slot="additional-headings">
        <NavLocations />
      </template>
      <Author :show-title="true" />
      <div class="index-features">
        <div class="index-half-column">
          <h4>most recent post:</h4>
          <!-- <ProjectCard :key="$page.project.edges[0].node.id" :project="$page.project.edges[0].node"/> -->
          <PostCard :key="$page.post.edges[0].node.id" :post="$page.post.edges[0].node"/>
        </div>
        <div class="index-half-column">
          <h4>featured project:</h4>
          <ProjectCard :key="$page.project.edges[0].node.id" :project="$page.project.edges[0].node"/>
        </div>
      </div>
      <div class="index-features">
        <g-link class="link-button" to="/blog">
          <span>all blog posts</span>
        </g-link>
        <g-link class="link-button" to="/projects">
          <span>all projects</span>
        </g-link>
      </div>
  </Layout>
</template>

<script>
import Author from '~/components/Author.vue'
import PostCard from '~/components/PostCard.vue'
import ProjectCard from '~/components/ProjectCard.vue'
import NavLocations from '~/components/NavLocations.vue'

export default {
  components: {
    Author,
    PostCard,
    NavLocations,
    ProjectCard
  },
}
</script>


<page-query>

query {
  post: allPost(limit: 1, filter: { published: { eq: true }}) {
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

  project: allProject {
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

<style lang="scss">
.index-features{
  display: flex;
  width: 100%;
  justify-content: space-around;
  flex-wrap: wrap;
}
.index-half-column {
  width: 40%;

  h4 {
    font-weight: lighter;
    text-align: center
  }

  .content-box {
    width: 100%;
  }

  @media screen and (max-width: 650px) {
    width: 90%;
  }
}

.link-button {
  text-decoration: none;
  color: var(--body-color)!important;
  font-size: 1.1em;
  padding: 0.5em;
  // border: 1px solid var(--body-color) !important;
  border-radius: var(--radius);
  background-color: var(--bg-content-color);
  transition: transform .3s, background-color .3s, box-shadow .6s !important;


  &:hover {
    transform: translateY(-2px);
    box-shadow: 1px 10px 30px 0 rgba(0,0,0,.1);
  }
}
</style>