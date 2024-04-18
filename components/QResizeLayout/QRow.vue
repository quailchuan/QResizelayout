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
  name: "QRow",
});
const props = defineProps({
  height: {
    type: Number,
    default: 300,
  },
  maxHeight: {
    type: Number,
    default: 400,
  },
  minHeight: {
    type: Number,
    default: 10,
  },
  showCollapse: {
    type: Boolean,
    default: true,
  },
});
const isLast = ref(false);
const row = ref<HTMLElement>();
const rowResizeBar = ref<HTMLElement>();
const rowArea = ref<HTMLElement>();
const isCollapse = ref<boolean>(false);

const handleViewMoreClick = async () => {
  isCollapse.value = !isCollapse.value;
  if (isCollapse.value) {
    row.value!.style.height = "10px";
    rowResizeBar.value!.style.display = "none";
    rowArea.value!.style.display = "none";
  } else {
    row.value!.style.height = "400px";
    rowResizeBar.value!.style.display = "block";
    rowArea.value!.style.display = "flex";
  }
};

const handleDragResizeBar = () => {
  // colResizeBar.value!.onmousedown = () => {
  // 颜色提醒
  rowResizeBar.value!.style.backgroundColor = "#5e7ce0";
  rowResizeBar.value!.style.color = "#ffffff";

  // 鼠标拖动事件
  document.onmousemove = function (eventMove) {
    if (!isLast.value) {
      let height = eventMove.clientY - 50;
      if (height >= props.maxHeight) {
        height = props.maxHeight; // 设置最大拉伸宽度为800
      } else if (height <= props.minHeight) {
        // 当拉伸宽度为小于或等于23，最小拉伸宽度为23，同时是否收起图标向右
        height = props.minHeight;
        isCollapse.value = true;
      } else {
        // 当拉伸宽度为大于23且小于600，是否收起图标向左
        isCollapse.value = false;
      }
      row.value!.style.height = height + "px";
    } else {
      const root = instance.parent!.subTree.el as HTMLElement;
      let height = root.clientHeight - eventMove.clientY;
      if (height >= props.maxHeight) {
        height = props.maxHeight; // 设置最大拉伸宽度为800
      } else if (height <= props.minHeight) {
        height = props.minHeight;
        isCollapse.value = true;
      } else {
        isCollapse.value = false;
      }
      row.value!.style.height = height + "px";
    }
  };

  // 鼠标松开事件
  document.onmouseup = () => {
    rowResizeBar.value!.style.backgroundColor = "#ffffff";
    rowResizeBar.value!.style.color = "#40485c";

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
    rowResizeBar.value!.addEventListener("mousedown", handleDragResizeBar);
  });
});
onUnmounted(() => {
  rowResizeBar.value!.removeEventListener("mousedown", handleDragResizeBar);
});
</script>
<template>
  <div class="row" ref="row" v-if="!isLast">
    <div class="row-area" ref="rowArea">
      <slot></slot>
    </div>
    <div class="row-resize-bar" ref="rowResizeBar">&hellip;</div>
    <div
      class="row-view-more"
      @click="handleViewMoreClick"
      v-show="props.showCollapse"
    >
      <div class="row-view-more-content">
        <div
          :class="isCollapse ? 'row-view-more-true' : 'row-view-more-false'"
        ></div>
      </div>
    </div>
  </div>
  <div class="row" ref="row" v-else>
    <div
      class="row-view-more"
      @click="handleViewMoreClick"
      v-show="props.showCollapse"
    >
      <div class="row-view-more-content last">
        <div
          :class="isCollapse ? 'row-view-more-true' : 'row-view-more-false'"
        ></div>
      </div>
    </div>
    <div class="row-resize-bar" ref="rowResizeBar">&hellip;</div>
    <div class="row-area" ref="rowArea">
      <slot></slot>
    </div>
  </div>
</template>
<style lang="scss" scoped>
.row {
  height: 400px;
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;

  border-right: 1px solid #dcdfe6;
  width: 100%;
  .row-area {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    background-color: #f7f7f7;
    width: 100%;
    height: 100%;
  }
  .row-resize-bar {
    height: 10px;
    width: 100%;
    background-color: rgb(255, 255, 255);
    cursor: row-resize;
    user-select: none;
    transition: all ease 0.3s;
    line-height: 0;
    text-align: center;

    color: #40485c;
    &:hover {
      color: #fff !important;
      background-color: #5e7ce0 !important;
    }
  }

  .row-view-more {
    position: relative;
    width: 100%;
    height: 10px;

    .row-view-more-content {
      width: 20px;
      height: 100%;
      background-color: #ccc;

      position: absolute;
      display: block;
      margin: auto;
      left: 0;
      right: 0;
      border-bottom-left-radius: 4px;
      border-bottom-right-radius: 4px;
      cursor: pointer;
      z-index: 1;
      transition: all ease 0.3s;
      &:hover {
        background-color: #5e7ce0;
      }

      &.last {
        border-bottom-left-radius: 0;
        border-bottom-right-radius: 0;
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
      }

      .row-view-more-true {
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
          left: 2px;
          bottom: 5px;
          background-color: #fff;
          transform: rotate(45deg);
        }

        &::after {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          right: 2px;
          top: 3px;
          background-color: #fff;
          transform: rotate(-45deg);
        }
      }

      .row-view-more-false {
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
          right: 2px;
          top: 2px;

          background-color: #fff;
          transform: rotate(45deg);
        }

        &::after {
          display: block;
          height: 2px;
          width: 10px;
          content: "";
          position: absolute;
          bottom: 6px;

          left: 3px;
          background-color: #fff;
          transform: rotate(-45deg);
        }
      }
    }
  }
}
</style>
