<template>
  <header class="storage-header">
    <div class="title-row">
      <div class="title-group">
        <h1>Local Storage</h1>
        <span class="badge">{{ totalItems }} Items</span>
      </div>
      <div class="actions">
        <button class="btn btn-primary" @click="$emit('add')">＋ Add Key</button>
        <button class="btn btn-danger" @click="confirmClearAll">⚠️ Clear All</button>
      </div>
    </div>

    <div class="search-bar">
      <svg class="search-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
      <input 
        type="text" 
        :value="searchQuery" 
        @input="$emit('update:searchQuery', $event.target.value)"
        placeholder="Filter by key or value text..."
      />
    </div>
  </header>
</template>

<script setup>
defineProps({
  totalItems: { type: Number, required: true },
  searchQuery: { type: String, required: true }
});
const emit = defineEmits(['add', 'clearAll', 'update:searchQuery']);

function confirmClearAll() {
  if (confirm("Are you absolutely sure you want to purge ALL local storage data? This can break your apps.")) {
    emit('clearAll');
  }
}
</script>

<style scoped>
.storage-header { margin-bottom: 16px; }
.title-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; }
.title-group { display: flex; align-items: center; gap: 12px; }
h1 { font-size: 22px; font-weight: 600; margin: 0; color: #ffffff; }
.badge { font-size: 12px; padding: 4px 8px; border-radius: 12px; background-color: #18181b; border: 1px solid #27272a; color: #a1a1aa; }
.actions { display: flex; gap: 8px; }
.btn { border: none; padding: 8px 14px; border-radius: 6px; font-size: 13px; font-weight: 500; cursor: pointer; }
.btn-primary { background-color: #22c55e; color: #000000; font-weight: 600; }
.btn-primary:active { background-color: #16a34a; }
.btn-danger { background-color: #27272a; border: 1px solid #dc2626; color: #ef4444; }
.btn-danger:active { background-color: #450a0a; }
.search-bar { position: relative; display: flex; align-items: center; }
.search-icon { position: absolute; left: 12px; width: 16px; height: 16px; color: #52525b; pointer-events: none; }
input { width: 100%; background-color: #18181b; border: 1px solid #27272a; border-radius: 8px; padding: 10px 12px 10px 38px; color: #ffffff; font-size: 14px; outline: none; }
input:focus { border-color: #3f3f46; }
</style>

