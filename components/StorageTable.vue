<template>
  <div class="table-container">
    <div v-if="items.length === 0" class="empty-state">No matching storage values.</div>
    
    <div v-else class="storage-list">
      <div 
        v-for="item in items" 
        :key="item.key" 
        class="storage-row"
        @click="$emit('edit', item)"
      >
        <!-- Left: Square colored badge accent and Text -->
        <div class="left-section">
          <div class="meta-square" :class="{ 'json-type': item.isJson }"></div>
          <div class="text-group">
            <span class="item-key">{{ item.key }}</span>
            <span class="item-type-label">{{ item.isJson ? 'JSON' : 'String' }}</span>
          </div>
        </div>

        <!-- Right: Inline value excerpt preview and interactive thin actions -->
        <div class="right-section">
          <span class="value-preview">{{ truncate(item.value) }}</span>
          <button class="delete-btn" @click.stop="confirmDelete(item.key)">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polyline points="3 6 5 6 21 6"></polyline>
              <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  items: { type: Array, required: true }
});
const emit = defineEmits(['edit', 'delete']);

function truncate(str) {
  if (!str) return 'empty';
  return str.length > 18 ? str.substring(0, 16) + '...' : str;
}

function confirmDelete(key) {
  if (confirm(`Delete key "${key}"?`)) {
    emit('delete', key);
  }
}
</script>

<style scoped>
.table-container {
  flex: 1;
  overflow-y: auto;
}
.empty-state {
  text-align: center;
  padding: 32px;
  color: #52525b;
  font-size: 13px;
}
.storage-list {
  display: flex;
  flex-direction: column;
}
.storage-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 8px;
  border-bottom: 1px solid #1a1a1c;
  cursor: pointer;
}
.storage-row:active {
  background-color: #121214;
}
.left-section {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 0;
  flex: 1;
}

/* Matches the distinct little colored square tokens from your image */
.meta-square {
  width: 8px;
  height: 8px;
  background-color: #a855f7; /* Muted violet default */
  border-radius: 1px;
  flex-shrink: 0;
}
.meta-square.json-type {
  background-color: #3b82f6; /* Blue indicator variant for nested objects */
}

.text-group {
  display: flex;
  align-items: center;
  gap: 8px;
  min-width: 0;
}
.item-key {
  font-size: 14px;
  color: #e3e3e6;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.item-type-label {
  font-size: 13px;
  color: #55555a; /* Inline muted context label from image template */
}

.right-section {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-shrink: 0;
}
.value-preview {
  font-size: 13px;
  color: #7c7c82;
  font-family: monospace;
}
.delete-btn {
  background: none;
  border: none;
  padding: 4px;
  color: #55555a;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.delete-btn svg {
  width: 14px;
  height: 14px;
}
.delete-btn:active svg {
  color: #ef4444;
}
</style>

