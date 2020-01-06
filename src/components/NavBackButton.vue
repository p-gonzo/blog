<template>
	<g-link class="nav-button" :to="location">
	  <span>
	    &larr; {{ this.text }}
	  </span>
	</g-link>
</template>

<script>
export default {
	props: ['text', 'location']
}
</script>

<style lang="scss">
.nav-button {
  text-decoration: none;
  color: var(--body-color)!important;
  font-size: .9em;
}
</style>
