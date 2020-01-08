<template>
  <Layout>
    <template slot="additional-headings">
      <BackButton :text="'projects'" :location="'/projects'" />
    </template>
    <div class="project-title">
      <h1 class="project-title__text">
        {{ $page.project.title }}
      </h1>
    </div>

    <div class="project content-box">
      <div class="project__header">
        <g-image alt="Cover image" v-if="$page.project.cover_image" :src="$page.project.cover_image" />
      </div>
      <div class="project__content" v-html="$page.project.content" />
    </div>

    <Author class="project-author" />
  </Layout>
</template>

<script>
import Author from '~/components/Author.vue'
import BackButton from '~/components/NavBackButton.vue'

export default {
  components: {
    Author,
    BackButton
  },
  metaInfo () {
    return {
      title: this.$page.project.title,
      meta: [
        {
          name: 'description',
          content: this.$page.project.description
        }
      ]
    }
  }
}
</script>

<page-query>
query Project ($id: ID!) {
  project: project (id: $id) {
    title
    path
    description
    content
    cover_image (width: 860, blur: 10)
  }
}
</page-query>

<style lang="scss">
.project-title {
  padding: calc(var(--space) / 2) 0 calc(var(--space) / 2);
  text-align: center;
}

.project {

  &__header {
    width: calc(100% + var(--space) * 2);
    margin-left: calc(var(--space) * -1);
    margin-top: calc(var(--space) * -1);
    margin-bottom: calc(var(--space) / 2);
    overflow: hidden;
    border-radius: var(--radius) var(--radius) 0 0;

    img {
      width: 100%;
    }

    &:empty {
      display: none;
    }
  }

  &__content {
    h2:first-child {
      margin-top: 0;
    }

    p:first-of-type {
      font-size: 1.2em;
      color: var(--title-color);
    }

    img {
      width: calc(100% + var(--space) * 2);
      margin-left: calc(var(--space) * -1);
      display: block;
      max-width: none;
    }
  }
}

.project-comments {
  padding: calc(var(--space) / 2);

  &:empty {
    display: none;
  }
}

.project-author {
  margin-top: calc(var(--space) / 2);
}
</style>
