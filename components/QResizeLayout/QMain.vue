<script lang="ts" setup>
defineOptions({
  name: "QMain",
});
</script>
<template>
  <main class="Q-main">Main <slot></slot></main>
</template>
<style lang="scss" scoped>
.Q-main {
  width: 100%;
  height: 100%;
}
</style>
