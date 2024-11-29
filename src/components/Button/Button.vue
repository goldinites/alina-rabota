<template>
  <button class="button" :class="{'button--small': small}" @click.stop="$emit('click')">
    <slot/>
  </button>
</template>

<script setup>
defineProps({
  small: {
    type: Boolean,
  }
})
</script>

<style scoped lang="scss">
.button {
  display: inline-flex;
  cursor: pointer;
  padding: 6px 12px;
  background-color: #0B6BCB;
  color: #fff;
  min-height: 2.25rem;
  border-radius: 6px;
  font-size: 0.875rem;
  line-height: 1.5;
  font-weight: 600;
  user-select: none;

  &--small {
    min-height: auto;
    font-size: 10px;
    line-height: 1;
    padding: 5px;
  }
}
</style>