<template>
  <div class="modal" v-if="isOpen">
    <div class="modal-content">
      <span class="close" @click="closeModal">&times;</span>
      <h3>Selected files - {{ items.length }} item<span v-if="items.length !== 1">s</span></h3>
      <ul>
        <li v-for="(item, idx) in items" :key="idx">{{ item.path }} - {{ item.device }}</li>
      </ul>
      <button class="close-btn" @click="closeModal">Close</button>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['close']);
const props = defineProps({
  show: Boolean,
  items: Array
});

import { ref, watch } from 'vue';

const isOpen = ref(false);

const closeModal = () => {
  emit('close');
};

watch(
  () => props.show,
  (value) => {
    isOpen.value = value;
  }
);
</script>

<style scoped lang="scss">
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);

  .modal-content {
    position: relative;
    background-color: #fefefe;
    margin: 15% auto;
    padding: 5px 20px 30px;
    border: 1px solid #888;
    width: 80%;
    max-width: 650px;
    min-height: 250px;

    .close {
      color: grey;
      float: right;
      font-size: 25px;
      font-weight: bold;

      &:hover,
      &:focus {
        color: black;
        text-decoration: none;
        cursor: pointer;
      }
    }

    .close-btn {
      position: absolute;
      bottom: 30px;
      right: 50px;
      padding: 8px 12px;
      cursor: pointer;
      color: black;
      border: 1px solid lightgray;
      border-radius: 3px;
      font-weight: 500;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    }

    h3 {
      margin-top: 10px;
    }

    ul {
      margin-top: 15px;

      li {
        margin-bottom: 3px;
      }
    }
  }
}
</style>
