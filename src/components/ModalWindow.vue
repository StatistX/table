<template>
  <div v-show="showModal" class="modal-overlay" @click="hideModal">
    <div @click.stop class="modal-content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    showModal: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    hideModal () {
      this.$emit('update:showModal', false)
    }
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  inset: 0;
  background-color: rgba(0, 0, 0, .5);
  display: flex;
}

.modal-content {
  margin: auto;
  background-color: rgba(255, 255, 255, 1);
  border-radius: 5px;
}
</style>
