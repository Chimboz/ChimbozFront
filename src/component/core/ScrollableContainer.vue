<template>
  <div
    class="scrollable"
    @scroll.passive="onScroll"
    :style="{ maxHeight: this.maxHeight + 'px' }"
  >
    <slot></slot>
    <div v-show="isLoading" class="spinner-loading">
      <img
        src="@/asset/img/loading.svg"
        alt="Loading spinner"
        draggable="false"
        width="200"
        height="200"
        @contextmenu.prevent
      />
    </div>
  </div>
</template>

<script>
// @vuese
// @group Core
// Generic scrollable container for infinite scroll
export default {
  name: "ScrollableContainer",
  data() {
    return {
      page: 0,
      isLoading: false,
    };
  },
  props: {
    route: {
      type: String,
      required: true,
    },
    maxHeight: {
      type: Number,
      required: false,
      default: 200,
    },
  },
  methods: {
    onScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (scrollTop + clientHeight >= scrollHeight && !this.hasEnded) {
        this.isLoading = true;
        this.api.get(`/api/${this.route}/${++this.page}.json`).then(
          (res) => {
            this.$emit("scrollData", res.data);
            this.isLoading = false;
          },
          () => {
            this.isLoading = false;
          }
        );
      }
    },
  },
};
</script>
<style style="scss" scoped>
.scrollable {
  overflow-y: auto;
  overflow-x: hidden;
  transition: 0.3s;
}

.spinner-loading {
  background: var(--dark);
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 0;
  left: 0;
  border-radius: var(--border-radius);
}
</style>
