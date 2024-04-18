<script lang="ts" setup>
import {
  VNode,
  getCurrentInstance,
  nextTick,
  onMounted,
  onUnmounted,
  ref,
} from "vue";
defineOptions({
  name: "QCol",
});
const props = defineProps({
  width: {
    type: Number,
    default: 300,
  },
  maxWidth: {
    type: Number,
    default: 400,
  },
  minWidth: {
    type: Number,
    default: 10,
  },
  showCollapse: {
    type: Boolean,
    default: true,
  },
});
const isLast = ref(false);
const col = ref<HTMLElement>();
const colResizeBar = ref<HTMLElement>();

const colArea = ref<HTMLElement>();
const isCollapse = ref<boolean>(false);
const handleViewMoreClick = async () => {
  isCollapse.value = !isCollapse.value;
  if (isCollapse.value) {
    col.value!.style.width = "10px";
    colResizeBar.value!.style.display = "none";
    colArea.value!.style.display = "none";
  } else {
    col.value!.style.width = "400px";
    colResizeBar.value!.style.display = "flex";
    colArea.value!.style.display = "flex";
  }
};

const handleDragResizeBar = () => {
  // colResizeBar.value!.onmousedown = () => {
  // 颜色提醒
  colResizeBar.value!.style.backgroundColor = "#5e7ce0";
  colResizeBar.value!.style.color = "#ffffff";

  // 鼠标拖动事件
  document.onmousemove = function (eventMove) {
    if (!isLast.value) {
      console.log(11);
      let width = eventMove.clientX - 30;
      if (width >= props.maxWidth) {
        width = props.maxWidth; // 设置最大拉伸宽度为800
      } else if (width <= props.minWidth) {
        // 当拉伸宽度为小于或等于23，最小拉伸宽度为23，同时是否收起图标向右
        width = props.minWidth;
        isCollapse.value = true;
      } else {
        // 当拉伸宽度为大于23且小于600，是否收起图标向左
        isCollapse.value = false;
      }
      col.value!.style.width = width + "px";
    } else {
      const root = instance.parent!.subTree.el as HTMLElement;
      let width = root.clientWidth - eventMove.clientX;
      if (width >= props.maxWidth) {
        width = props.maxWidth; // 设置最大拉伸宽度为800
      } else if (width <= props.minWidth) {
        width = props.minWidth;
        isCollapse.value = true;
      } else {
        isCollapse.value = false;
      }
      col.value!.style.width = width + "px";
    }
  };

  // 鼠标松开事件
  document.onmouseup = () => {
    colResizeBar.value!.style.backgroundColor = "#ffffff";
    colResizeBar.value!.style.color = "#40485c";

    document.onmousemove = null;
    document.onmouseup = null;
  };
  return false;
  // };
};
const instance = getCurrentInstance()!;
onMounted(() => {
  if (instance.parent && instance.parent.subTree) {
    const subTree = instance.parent.subTree as VNode;
    if (subTree.children) {
      const children = subTree.children as VNode[];
      const temp = children[0].children as VNode[];
      isLast.value = instance.uid == temp[temp.length - 1].component?.uid;
    }
  }
  nextTick(() => {
    colResizeBar.value!.addEventListener("mousedown", handleDragResizeBar);
  });
});
onUnmounted(() => {
  colResizeBar.value!.removeEventListener("mousedown", handleDragResizeBar);
});
</script>
<template>
  <div class="col" ref="col" v-if="!isLast">
    <div class="col-area" ref="colArea">
      <slot></slot>
    </div>
    <div class="col-resize-bar" ref="colResizeBar">⋮</div>
    <div
      class="col-view-more"
      @click="handleViewMoreClick"
      v-show="props.showCollapse"
    >
      <div class="col-view-more-content">
        <div
          :class="isCollapse ? 'col-view-more-true' : 'col-view-more-false'"
        ></div>
      </div>
    </div>
  </div>
  <div class="col" ref="col" v-else>
    <div
      class="col-view-more"
      @click="handleViewMoreClick"
      v-show="props.showCollapse"
    >
      <div class="col-view-more-content last">
        <div
          :class="isCollapse ? 'col-view-more-true' : 'col-view-more-false'"
        ></div>
      </div>
    </div>
    <div class="col-resize-bar" ref="colResizeBar">⋮</div>
    <div class="col-area" ref="colArea">
      <slot></slot>
    </div>
  </div>
</template>
<style lang="scss" scoped>
.col {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: row;

  height: 100%;
  .col-area {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    background-color: #f7f7f7;
    height: 100%;
  }

  .col-resize-bar {
    display: flex;
    align-items: center;
    width: 5px;
    height: 100%;
    background-color: rgb(255, 255, 255);
    cursor: col-resize;
    user-select: none;
    transition: all ease 0.3s;

    color: #40485c;
    &:hover {
      color: #fff !important;
      background-color: #5e7ce0 !important;
    }
  }
  .col-view-more {
    position: relative;
    width: 10px;
    height: 100%;

    .col-view-more-content {
      width: 100%;
      height: 30px;
      background-color: #ccc;
      border-bottom-right-radius: 4px;
      border-top-right-radius: 4px;
      position: absolute;
      display: block;
      margin: auto;
      bottom: 0;
      top: 0;
      cursor: pointer;
      z-index: 1;
      transition: all ease 0.3s;
      &:hover {
        background-color: #5e7ce0;
      }

      &.last {
        border-bottom-right-radius: 0;
        border-top-right-radius: 0;
        border-bottom-left-radius: 4px;
        border-top-left-radius: 4px;
      }

      .col-view-more-true {
        width: 100%;
        height: 10px;
        position: absolute;
        display: block;
        margin: auto;
        left: 0;
        top: 0;
        &::before {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          left: 0;
          top: 10px;
          background-color: #fff;
          transform: rotate(70deg);
        }

        &::after {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          left: 0;
          bottom: -10px;
          background-color: #fff;
          transform: rotate(-70deg);
        }
      }

      .col-view-more-false {
        width: 100%;
        height: 10px;
        position: absolute;
        display: block;
        margin: auto;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;

        &::before {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          left: 0;
          top: 0;
          background-color: #fff;
          transform: rotate(-70deg);
        }

        &::after {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          left: 0;
          bottom: 0;
          background-color: #fff;
          transform: rotate(70deg);
        }
      }
    }
  }
}
</style>
