<template>
  <div class="storage-container">
    <StorageHeader 
      v-model:search-query="searchQuery"
      @add="openAddModal"
      @clear-all="clearAllStorage"
    />

    <StorageTable 
      :items="paginatedVisibleItems"
      :has-more="hasMorePages"
      @edit="openEditModal"
      @delete="deleteKey"
      @load-more="appendNextPageBlock"
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
import { ref, computed, watch, onMounted } from 'vue';
import StorageHeader from './components/StorageHeader.vue';
import StorageTable from './components/StorageTable.vue';
import StorageModal from './components/StorageModal.vue';

// Configuration Bounds
const PAGE_CHUNK_SIZE = 25; // Number of items loaded per scroll interval
const MAX_PREVIEW_CHARS = 24; // String truncation length for the list view

// Base Memory States
const rawStorageItems = ref([]);
const searchQuery = ref('');
const visibleLimitCount = ref(PAGE_CHUNK_SIZE);
const modalActive = ref(false);
const isEditMode = ref(false);
const selectedItem = ref({ key: '', value: '' });

/**
 * Calculates human-readable string data weights efficiently.
 */
function getStorageItemWeight(byteLength) {
  if (byteLength === 0) return '0 B';
  const units = ['B', 'KB', 'MB', 'GB'];
  const i = Math.floor(Math.log(byteLength) / Math.log(1024));
  return `${parseFloat((byteLength / Math.pow(1024, i)).toFixed(1))} ${units[i]}`;
}

/**
 * Syncs the component state with localStorage efficiently.
 * Extracts tiny text fragments and exact sizes instead of loading whole payloads into memory.
 */
function syncStorageData() {
  const metaList = [];
  const totalLength = window.localStorage.length;

  for (let i = 0; i < totalLength; i++) {
    const keyName = window.localStorage.key(i);
    const rawVal = window.localStorage.getItem(keyName) || '';
    
    // Calculate memory allocations instantly
    const byteWeight = rawVal.length; 
    const sizeLabel = getStorageItemWeight(byteWeight);

    // Truncate strings immediately to minimize memory overhead

    metaList.push({
      key: keyName,
      sizeText: sizeLabel
    });
  }

  // Alphabetical registry sorting matching your file workspace hierarchy
  rawStorageItems.value = metaList.sort((a, b) => a.key.localeCompare(b.key));
}

// Reset page counts when active search strings change
watch(searchQuery, () => {
  visibleLimitCount.value = PAGE_CHUNK_SIZE;
});

// Filters data efficiently based on key strings
const processedFilteredCollection = computed(() => {
  const queryToken = searchQuery.value.toLowerCase().trim();
  if (!queryToken) return rawStorageItems.value;
  return rawStorageItems.value.filter(item => 
    item.key.toLowerCase().includes(queryToken)
  );
});

// Returns only the current slice of items for the active view viewport area
const paginatedVisibleItems = computed(() => {
  return processedFilteredCollection.value.slice(0, visibleLimitCount.value);
});

const hasMorePages = computed(() => {
  return visibleLimitCount.value < processedFilteredCollection.value.length;
});

function appendNextPageBlock() {
  if (hasMorePages.value) {
    visibleLimitCount.value += PAGE_CHUNK_SIZE;
  }
}

function openAddModal() {
  isEditMode.value = false;
  selectedItem.value = { key: '', value: '' };
  modalActive.value = true;
}

/**
 * Fetches the full payload only when explicitly requested for editing.
 */
function openEditModal(item) {
  isEditMode.value = true;
  const fullValue = window.localStorage.getItem(item.key) || '';
  selectedItem.value = { key: item.key, value: fullValue };
  modalActive.value = true;
}

function commitStorageData(payload) {
  window.localStorage.setItem(payload.key, payload.value);
  modalActive.value = false;
  syncStorageData();
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
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, sans-serif;
  padding: 12px 14px;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}
</style>

