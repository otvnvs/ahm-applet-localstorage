<template>
  <header class="storage-header">
    <!-- Path Status Line -->
    <div class="path-display">
      <svg class="icon-folder" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path>
      </svg>
      <span class="path-text">/root/local_storage</span>
    </div>

    <!-- Arcade Action Grid Buttons row -->
    <div class="action-grid-buttons">
      <button class="action-btn" @click="$emit('add')">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path>
          <polyline points="14 2 14 8 20 8"></polyline>
          <line x1="12" y1="18" x2="12" y2="12"></line>
          <line x1="9" y1="15" x2="15" y2="15"></line>
        </svg>
        New Key
      </button>
      <button class="action-btn" @click="confirmClearAll">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <polyline points="3 6 5 6 21 6"></polyline>
          <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
        </svg>
        Wipe All
      </button>
    </div>

    <!-- Dense Search Entry Field -->
    <div class="search-wrapper">
      <input 
        type="text" 
        :value="searchQuery" 
        @input="$emit('update:searchQuery', $event.target.value)"
        placeholder="Filter current registry..."
      />
    </div>
  </header>
</template>

<script setup>
defineProps({
  searchQuery: { type: String, required: true }
});
const emit = defineEmits(['add', 'clearAll', 'update:searchQuery']);

function confirmClearAll() {
  if (confirm("Purge ALL storage key rows?")) {
    emit('clearAll');
  }
}
</script>

<style scoped>
.storage-header {
  margin-bottom: 12px;
}
.path-display {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 4px 8px;
  margin-bottom: 16px;
}
.icon-folder {
  width: 16px;
  height: 16px;
  color: #7c7c82;
}
.path-text {
  font-family: monospace;
  font-size: 14px;
  color: #7c7c82;
}
.action-grid-buttons {
  display: flex;
  gap: 12px;
  margin-bottom: 14px;
}
.action-btn {
  flex: 1;
  background-color: #212124;
  border: 1px solid #2d2d31;
  border-radius: 4px;
  color: #e3e3e6;
  font-size: 13px;
  padding: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}
.action-btn svg {
  width: 14px;
  height: 14px;
  color: #a1a1aa;
}
.action-btn:active {
  background-color: #2a2a2e;
}
.search-wrapper {
  padding: 0 2px;
}
input {
  width: 100%;
  background-color: #141416;
  border: 1px solid #242427;
  border-radius: 4px;
  padding: 8px 10px;
  color: #ffffff;
  font-size: 13px;
  box-sizing: border-box;
  outline: none;
}
input:focus {
  border-color: #38383c;
}
</style>

