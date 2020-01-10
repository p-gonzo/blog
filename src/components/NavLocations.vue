<template>
    <div>
      <g-link class="nav-button" to="/blog">
      <span>blog</span>
        </g-link>
      <g-link class="nav-button" to="/projects">
        <span>projects</span>
      </g-link>
      <g-link class="nav-button" to="/">
        <span>contact</span>
      </g-link>
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
