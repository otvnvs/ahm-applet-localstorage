<template>
  <div class="table-container">
    <div v-if="items.length === 0" class="empty-state">
      <p>No storage keys matched your active criteria layout.</p>
    </div>
    
    <div v-else class="storage-list">
      <div v-for="item in items" :key="item.key" class="storage-item">
        <div class="item-info">
          <div class="key-name">{{ item.key }}</div>
          <div class="value-preview" :class="{ 'json-format': item.isJson }">
            {{ truncate(item.value) }}
          </div>
        </div>
        <div class="item-actions">
          <button class="action-btn edit" @click="$emit('edit', item)" title="Edit entry">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 1 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
          </button>
          <button class="action-btn delete" @click="confirmDelete(item.key)" title="Delete entry">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg>
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

function truncate(str, limit = 80) {
  if (!str) return '""';
  return str.length > limit ? str.substring(0, limit) + '...' : str;
}

function confirmDelete(key) {
  if (confirm(`Remove "${key}" key?`)) {
    emit('delete', key);
  }
}
</script>

<style scoped>
.table-container { flex: 1; overflow-y: auto; background-color: #111113; border: 1px solid #27272a; border-radius: 12px; padding: 4px; }
.empty-state { text-align: center; padding: 48px 16px; color: #71717a; font-size: 14px; }
.storage-list { display: flex; flex-direction: column; }
.storage-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 16px; border-bottom: 1px solid #1c1c1e; gap: 16px; }
.storage-item:last-child { border-bottom: none; }
.item-info { flex: 1; min-width: 0; display: flex; flex-direction: column; gap: 4px; }
.key-name { font-size: 14px; font-weight: 600; color: #ffffff; word-break: break-all; }
.value-preview { font-size: 12px; font-family: monospace; color: #a1a1aa; word-break: break-all; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.json-format { color: #60a5fa; }
.item-actions { display: flex; gap: 6px; flex-shrink: 0; }
.action-btn { width: 32px; height: 32px; border-radius: 6px; border: 1px solid #27272a; background-color: #18181b; color: #a1a1aa; display: flex; align-items: center; justify-content: center; cursor: pointer; }
.action-btn svg { width: 14px; height: 14px; }
.action-btn.edit:active { background-color: #27272a; color: #ffffff; }
.action-btn.delete:active { background-color: #450a0a; color: #f87171; border-color: #7f1d1d; }
</style>

