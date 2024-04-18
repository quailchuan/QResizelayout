<script lang="ts" setup>
import { Component, VNode, computed, useSlots } from "vue";

defineOptions({
  name: "QLayout",
});
const slots = useSlots();
const isVertical = computed(() => {
  if (props.direction === "vertical") return true;
  if (props.direction === "horizontal") return false;
  if (slots && slots.default) {
    const vNodes: VNode[] = slots.default();
    return vNodes.some((vNode) => {
      const tag = (vNode.type as Component).name;
      return tag === "QRow";
    });
  } else {
    return false;
  }
});
const props = defineProps({
  /**
   * @description layout direction for child elements
   */
  direction: {
    type: String,
  },
});
</script>

<template>
  <section :class="isVertical ? ['Q-layout', 'vertical'] : 'Q-layout'">
    <slot />
  </section>
</template>
<style lang="scss" scoped>
.Q-layout {
  display: flex;

  width: 100%;
  height: 100%;
  overflow: hidden;
  background-color: #f7f7f7;
  &.vertical {
    flex-direction: column;
  }
}
</style>
