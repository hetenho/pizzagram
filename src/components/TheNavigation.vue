<template>
  <nav
    v-if="$store.state.auth.isAuthenticated"
    class="navigation"
  >
    <div class="navigation__items">
      <NavItem
        :to="{ name: 'home' }"
        class="navigation__item"
        exact
        icon="home"
      >
        Home
      </NavItem>
      <TheCamera class="navigation__item" />
      <NavItem
        :to="{ name: 'profile', params: { username: username || null } }"
        class="navigation__item"
        icon="user"
      >
        Profile
      </NavItem>
    </div>
  </nav>
</template>

<script>
import NavItem from "./NavItem";
import TheCamera from "./TheCamera";

export default {
  components: {
    NavItem,
    TheCamera
  },
  computed: {
    username() {
      return this.$store.state.auth.username;
    }
  }
};
</script>

<style lang="scss" scoped>
.navigation {
  padding-bottom: env(safe-area-inset-bottom);
  width: 100%;

  @media (min-width: 640px) {
    padding-bottom: 0;
  }

  &__items {
    align-items: center;
    color: var(--color-purple);
    display: flex;
    height: 3.5rem;
    justify-content: space-around;
  }

  &__item:not(:last-child) {
    @media (min-width: 640px) {
      margin-right: 2rem;
    }
  }
}
</style>
