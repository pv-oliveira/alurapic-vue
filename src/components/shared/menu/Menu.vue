<template>
  <nav>
    <ul>
      <li v-for="routa in rotas">
        <router-link :to="routa.path ? routa.path : '/'">
          {{ routa.titulo }}
        </router-link>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    rotas: {
      type: Array,
      required: true,
    }
  }
};

</script>

<style></style>
