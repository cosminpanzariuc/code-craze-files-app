<template>
  <div class="table-wrapper">
    <div class="table-wrapper__upper-section">
      <div class="tri-state-checkbox" @click="toggleAll($event)">
        <input type="checkbox" :indeterminate="indeterminate" v-model="checked" />
      </div>

      <span class="table-wrapper__upper-section__summary">
        <span v-if="selectedFiles.length">Selected </span>
        <span class="count-label">
          {{ selectedFiles.length || 'None Selected' }}
        </span>
      </span>

      <span class="table-wrapper__upper-section__action">
        <span class="table-wrapper__upper-section__action__label" @click="showModal"
          >Download Selected</span
        >
      </span>
    </div>
    <table class="data-table" aria-describedby="items-array">
      <thead>
        <tr>
          <th></th>
          <th>Name</th>
          <th>Device</th>
          <th>Path</th>
          <th>Status</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="(item, idx) in itemsArray" :key="idx" :class="{ selected: item.checked }">
          <td>
            <div class="item-checkbox">
              <input
                v-model="item.checked"
                type="checkbox"
                :id="item.name"
                :name="item.name"
                :disabled="item.status !== 'available'"
                :class="{ checkbox: true, disabled: item.status !== 'available' }"
                @click="toggleItem(idx)"
              />
            </div>
          </td>
          <td>{{ item.name }}</td>
          <td>{{ item.device }}</td>
          <td>{{ item.path }}</td>
          <td>
            <span :class="{ available: item.status === 'available' }" class="status-dot"></span>
            <span>{{ item.status.charAt(0).toUpperCase() + item.status.slice(1) }}</span>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <SelectedItemsModal
    :show="modalVisibility"
    :items="selectedFiles"
    @close="modalVisibility = false"
  />
</template>

<script setup>
import { ref, computed, watchEffect } from 'vue';
import SelectedItemsModal from '@/components/SelectedItemsModal.vue';

const checked = ref(false);
const indeterminate = ref(false);
const modalVisibility = ref(false);

// Determine whether to check or uncheck all checkboxes
const toggleAll = (event) => {
  if (
    indeterminate.value &&
    selectedFiles.value.length !== itemsArray.value.filter((el) => el.status === 'available').length
  ) {
    event.preventDefault();
  } else {
    if (
      selectedFiles.value.length ===
      itemsArray.value.filter((el) => el.status === 'available').length
    ) {
      checked.value = false;
      for (let item of itemsArray.value) {
        setTimeout(() => {
          item.checked = false;
        });
      }
    }
  }

  const checkAll = !checked.value;
  for (let item of itemsArray.value) {
    if (item.status === 'available') {
      item.checked = checkAll;
    }
  }
  updateCheckedState();
};

const updateCheckedState = () => {
  const count = selectedFiles.value.length;
  // Some but not all items selected
  if (count && count !== itemsArray.value.length) {
    checked.value = false;
    indeterminate.value = true;
    // All items selected
  } else if (count === itemsArray.value.length) {
    checked.value = true;
    indeterminate.value = false;
  } else {
    // No items selected
    checked.value = false;
    indeterminate.value = false;
  }
};

const originalDataArray = ref([
  {
    name: 'smss.exe',
    device: 'Stark',
    path: '\\Device\\HarddiskVolume2\\Windows\\System32\\smss.exe',
    status: 'scheduled'
  },
  {
    name: 'netsh.exe',
    device: 'Targaryen',
    path: '\\Device\\HarddiskVolume2\\Windows\\System32\\netsh.exe',
    status: 'available'
  },
  {
    name: 'uxtheme.dll',
    device: 'Lanniester',
    path: '\\Device\\HarddiskVolume1\\Windows\\System32\\uxtheme.dll',
    status: 'available'
  },
  {
    name: 'cryptbase.dll',
    device: 'Martell',
    path: '\\Device\\HarddiskVolume1\\Windows\\System32\\cryptbase.dll',
    status: 'scheduled'
  },
  {
    name: '7za.exe',
    device: 'Baratheon',
    path: '\\Device\\HarddiskVolume1\\temp\\7za.exe',
    status: 'scheduled'
  }
]);

//Create a new data array based on originalDataArray but which will contain the "checked" attribute
const itemsArray = ref(originalDataArray.value.map((item) => ({ ...item, checked: false })));

const selectedFiles = computed(() => itemsArray.value.filter((el) => el.checked));

const toggleItem = (index) => {
  itemsArray.value[index].checked = !itemsArray.value[index].checked;
};

const showModal = () => {
  if (selectedFiles.value.length) {
    modalVisibility.value = true;
  } else {
    alert('No files selected');
  }
};

watchEffect(() => {
  updateCheckedState();
});
</script>

<style scoped lang="scss">
.table-wrapper {
  &__upper-section {
    position: relative;
    padding: 10px 0;

    &__summary {
      font-size: 18px;
    }

    &__action {
      position: absolute;
      background: url('../assets/icons/download.svg') no-repeat left;
      left: 245px;
      cursor: pointer;

      &__label {
        font-size: 18px;
        margin-left: 25px;
      }
    }

    .count-label {
      display: inline-block;
    }

    .tri-state-checkbox {
      display: inline-block;
      position: relative;
      margin-left: 30px;
      margin-right: 20px;
      cursor: pointer;

      input {
        cursor: pointer;
      }

      input[type='checkbox'] {
        transform: scale(1.3);
      }
    }
  }

  .data-table {
    width: 100%;
    border-collapse: collapse;
    background: white;
    box-shadow:
      0 3px 6px rgba(0, 0, 0, 0.16),
      0 3px 6px rgba(0, 0, 0, 0.23);

    th,
    tr {
      border: 1px solid #e9ecef;
      padding: 15px 10px;
      text-align: left;
      font-size: 13px;
    }

    th {
      background: white;
      border: none;
      font-size: 16px;

      &:last-child {
        padding-left: 35px !important;
      }
    }

    tr:hover {
      background: #f8f8f8;
    }

    td {
      padding: 10px;
    }

    .selected {
      background: lightgray !important;
    }

    .item-checkbox {
      display: flex;
      justify-content: center;

      .checkbox {
        width: 15px;
        height: 15px;

        &:not(.disabled) {
          cursor: pointer;
        }
      }
    }

    td:last-child {
      display: flex;
      align-items: center;

      .status-dot {
        width: 15px;
        height: 15px;
        background: transparent;
        border-radius: 50%;
        margin-right: 10px;
      }

      .available {
        background: green;
      }
    }
  }
}
</style>
