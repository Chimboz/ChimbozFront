<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="100%"
    height="100%"
    style="overflow: visible"
  >
    <text
      stroke-linecap="round"
      :x="justified ? '0%' : '50%'"
      y="75%"
      paint-order="stroke"
      :text-anchor="justified ? 'left' : 'middle'"
    >
      <slot></slot>
    </text>
  </svg>
</template>
<script>
// @vuese
// @group Core
export default {
  name: "StrokeText",
  props: {
    justified: {
      required: false,
      type: Boolean,
      default: false
    }
  }
};
</script>
