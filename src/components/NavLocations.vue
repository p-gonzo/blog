<template>
    <div>
      <g-link class="nav-button" to="/blog">
      <span>blog</span>
        </g-link>
      <g-link class="nav-button" to="/projects">
        <span>projects</span>
      </g-link>
      <a class="nav-button" href="mailto:pgonzalez115@gmail.com">
        <span>email</span>
      </a>
    </div>
</template>

<style lang="scss">
.nav-button {
  text-decoration: none;
  color: var(--body-color)!important;
  font-size: .9em;
  padding-right: 1em;
}
</style>
