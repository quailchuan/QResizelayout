<script lang="ts" setup>
import QLayout from "./components/QResizeLayout/QLayout.vue";
import QCol from "./components/QResizeLayout/QCol.vue";
import QMain from "./components/QResizeLayout/QMain.vue";
import QRow from "./components/QResizeLayout/QRow.vue";
</script>
<template>
  <QLayout>
    <QCol
      :width="200"
      :max-width="300"
      :min-width="10"
      :show-collapse="true"
    ></QCol>
    <QLayout>
      <QRow :showCollapse="false"></QRow>
      <QMain></QMain>
      <QRow></QRow>
    </QLayout>
    <QCol></QCol>
  </QLayout>
</template>
<style lang="scss">
html {
  width: 100%;
  height: 100%;
  body {
    width: 100%;

    height: 100%;
    #app {
      width: 100%;
      height: 100%;
      padding: 0;
    }
  }
}
</style>
