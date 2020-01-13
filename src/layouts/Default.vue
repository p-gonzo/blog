<template>
  <div id="app">

    <header ref="navbar" class="header">
      <div class="header__left">
        <slot name="additional-headings" />
      </div>

      <div v-if="authorInNav" class="header__center">
        <g-link class="nav-button" to="/">
          <span>Philip Gonzalez</span>
        </g-link>
      </div>
      
      <div class="header__right">        
        <ToggleTheme />
      </div>
    </header>

    <main class="main">
      <slot/>
    </main>

    <footer class="footer">
      <span class="footer__copyright">© 2019 - {{ new Date().getFullYear() }} Philip Gonzalez | </span>
      <span class="footer__links">Source available on <a href="https://github.com/p-gonzo/blog"> Github</a> | </span>
      <span class="footer__links">Powered by <a href="//gridsome.org"> Gridsome </a></span>
    </footer>

  </div>
</template>

<script>
import ToggleTheme from '~/components/ToggleTheme.vue'

export default {
  components: {
    ToggleTheme
  },
  props: ['authorInNav'],
  methods: {
    toggleBoxShadowOnNav() {
      if (window.pageYOffset < 60) {
        this.$refs.navbar.style.boxShadow = 'none';
      } else {
        this.$refs.navbar.style.boxShadow = '0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23)';
      }
    },
    toggNav(prevScrollpos, currentScrollPos) {
      if (prevScrollpos > currentScrollPos) {
        this.$refs.navbar.style.top = '0';
      } else {
        this.$refs.navbar.style.top = '-60px';
        this.$refs.navbar.style.boxShadow = 'none';
      }
    }
  },
  mounted() {
    let prevScrollpos = window.pageYOffset;
    window.onscroll = () => {
      let currentScrollPos = window.pageYOffset;
      this.toggleBoxShadowOnNav();
      this.toggNav(prevScrollpos, currentScrollPos);
      prevScrollpos = currentScrollPos;
    }
  }
}
</script>

<style lang="scss">
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-height: var(--header-height);
  padding: 0 calc(var(--space) / 2);
  position: sticky;
  top:0;
  z-index: 10;
  width: 100%;
  background-color: var(--bg-color);
  transition: 0.3s;

  &__left,
  &__right,
  &__center{
    display: flex;
    width: 33%
  }

  &__center {
    justify-content: space-around;
  }
  &__right {
    flex-direction: row-reverse;
  }
}

.main {
  margin: 0 auto;
  padding: 1.5vw 15px 0;
}

.footer {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: calc(var(--space) / 2);
  text-align: center;
  font-size: .8em;

  > span {
    margin: 0 .35em;
  }

  a {
    color: currentColor;
  }
}
</style>
