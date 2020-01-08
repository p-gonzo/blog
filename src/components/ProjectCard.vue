<template>
  <div class="project-card content-box">
    <div class="project-card__header">
      <g-image alt="Cover image" v-if="project.cover_image" class="project-card__image" :src="project.cover_image" />
    </div>
    <div class="project-card__content">
      <h2 class="project-card__title" v-html="project.title" />
      <p class="project-card__description" v-html="project.description" />
      <g-link class="project-card__link" :to="project.path">Link</g-link>
    </div>
  </div>
</template>

<script>

export default {
  props: ['project'],
}
</script>

<style lang="scss">
.project-card {
  margin-bottom: var(--space);
  position: relative;

  &__header {
    margin-left: calc(var(--space) * -1);
    margin-right: calc(var(--space) * -1);
    margin-bottom: calc(var(--space) / 2);
    margin-top: calc(var(--space) * -1);
    overflow: hidden;
    border-radius: var(--radius) var(--radius) 0 0;

    &:empty {
      display: none;
    }
  }

  &__image {
    min-width: 100%;
  }

  &__title {
    margin-top: 0;
  }

  &:hover {
    transform: translateY(-5px);
    box-shadow: 1px 10px 30px 0 rgba(0,0,0,.1);
  }

  &__tags {
    z-index: 1;
    position: relative;
  }

  &__link {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0.0;
    overflow: hidden;
    text-indent: -9999px;
    z-index: 0;
  }
}
</style>
