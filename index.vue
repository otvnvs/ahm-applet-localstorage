<template>
  <div class="storage-container">
    <StorageHeader 
      :total-items="filteredItems.length"
      v-model:search-query="searchQuery"
      @add="openAddModal"
      @clear-all="clearAllStorage"
    />

    <StorageTable 
      :items="filteredItems"
      @edit="openEditModal"
      @delete="deleteKey"
    />

    <StorageModal 
      v-if="modalActive"
      :is-edit="isEditMode"
      :active-item="selectedItem"
      @close="modalActive = false"
      @save="commitStorageData"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import StorageHeader from './components/StorageHeader.vue';
import StorageTable from './components/StorageTable.vue';
import StorageModal from './components/StorageModal.vue';

// Local States
const rawStorageItems = ref([]);
const searchQuery = ref('');
const modalActive = ref(false);
const isEditMode = ref(false);
const selectedItem = ref({ key: '', value: '' });

// Reads local storage entries cleanly
function syncStorageData() {
  const itemsList = [];
  for (let i = 0; i < window.localStorage.length; i++) {
    const keyName = window.localStorage.key(i);
    const valueData = window.localStorage.getItem(keyName);
    
    // Check if the payload is structural JSON text
    let isJsonFormat = false;
    try {
      if (valueData && (valueData.startsWith('{') || valueData.startsWith('['))) {
        JSON.parse(valueData);
        isJsonFormat = true;
      }
    } catch (e) {
      // Disregard non-JSON formats safely
    }

    itemsList.push({
      key: keyName,
      value: valueData || '',
      isJson: isJsonFormat
    });
  }
  
  // Sort alphabetically by key identifier
  rawStorageItems.value = itemsList.sort((a, b) => a.key.localeCompare(b.key));
}

const filteredItems = computed(() => {
  const q = searchQuery.value.toLowerCase().trim();
  if (!q) return rawStorageItems.value;
  return rawStorageItems.value.filter(item => 
    item.key.toLowerCase().includes(q) || 
    item.value.toLowerCase().includes(q)
  );
});

function openAddModal() {
  isEditMode.value = false;
  selectedItem.value = { key: '', value: '' };
  modalActive.value = true;
}

function openEditModal(item) {
  isEditMode.value = true;
  selectedItem.value = { key: item.key, value: item.value };
  modalActive.value = true;
}

function commitStorageData(payload) {
  try {
    window.localStorage.setItem(payload.key, payload.value);
    modalActive.value = false;
    syncStorageData();
  } catch (error) {
    alert(`Failed to save data matrix parameters: ${error.message}`);
  }
}

function deleteKey(keyName) {
  window.localStorage.removeItem(keyName);
  syncStorageData();
}

function clearAllStorage() {
  window.localStorage.clear();
  syncStorageData();
}

onMounted(() => {
  syncStorageData();
});
</script>

<style scoped>
.storage-container {
  min-height: 100vh;
  background-color: #0b0b0c;
  color: #e3e3e6;
  font-family: -apple-system, BlinkMacSystemFont, sans-serif;
  padding: 16px;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}
</style>

