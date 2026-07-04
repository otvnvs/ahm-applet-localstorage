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
        <!-- Left Section: Square badge tag and Key identifier details -->
        <div class="left-section">
          <div class="meta-square"></div>
          <div class="text-group">
            <span class="item-key">{{ item.key }}</span>
          </div>
        </div>

        <!-- Right Section: Fast flat preview string fragment and interactive thin actions -->
        <div class="right-section">
            <span class="item-size-label">{{ item.sizeText }}</span>
          <button class="delete-btn" @click.stop="confirmDelete(item.key)">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polyline points="3 6 5 6 21 6"></polyline>
              <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
            </svg>
          </button>
        </div>
      </div>

      <!-- Infinite Pagination Stream Anchor Point -->
      <div v-if="hasMore" id="scroll-anchor" class="loading-anchor">
        <span class="pulse-dot"></span> Loading more entries...
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, onUnmounted, watch, nextTick } from 'vue';

const props = defineProps({
  items: { type: Array, required: true },
  hasMore: { type: Boolean, required: true }
});
const emit = defineEmits(['edit', 'delete', 'loadMore']);

let observer = null;

function confirmDelete(key) {
  if (confirm(`Delete key "${key}"?`)) {
    emit('delete', key);
  }
}

// Sets up a lightweight intersection observer to monitor scroll depths
function setupObserver() {
  if (observer) observer.disconnect();

  observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting && props.hasMore) {
      emit('loadMore');
    }
  }, { threshold: 0.1 });

  nextTick(() => {
    const anchor = document.getElementById('scroll-anchor');
    if (anchor) observer.observe(anchor);
  });
}

onMounted(() => {
  setupObserver();
});

// Re-evaluate target observer bindings whenever lists expand or filter down
watch(() => props.items.length, () => {
  setupObserver();
});

onUnmounted(() => {
  if (observer) observer.disconnect();
});
</script>

<style scoped>
.table-container { flex: 1; overflow-y: auto; }
.empty-state { text-align: center; padding: 32px; color: #52525b; font-size: 13px; }
.storage-list { display: flex; flex-direction: column; }
.storage-row { display: flex; justify-content: space-between; align-items: center; padding: 14px 8px; border-bottom: 1px solid #1a1a1c; cursor: pointer; }
.storage-row:active { background-color: #121214; }
.left-section { display: flex; align-items: center; gap: 12px; min-width: 0; flex: 1; }
.meta-square { width: 8px; height: 8px; background-color: #a855f7; border-radius: 1px; flex-shrink: 0; }
.text-group { display: flex; align-items: center; gap: 8px; min-width: 0; }
.item-key { font-size: 14px; color: #e3e3e6; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

/* Memory Footprint Metrics Labels */
.item-size-label { font-size: 12px; font-family: monospace; color: #55555a; background-color: #111113; padding: 1px 4px; border-radius: 3px; flex-shrink: 0; }

.right-section { display: flex; align-items: center; gap: 16px; flex-shrink: 0; min-width: 0; max-width: 50%; }
.value-preview { font-size: 13px; color: #7c7c82; font-family: monospace; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.delete-btn { background: none; border: none; padding: 4px; color: #55555a; display: flex; align-items: center; justify-content: center; cursor: pointer; }
.delete-btn svg { width: 14px; height: 14px; }
.delete-btn:active svg { color: #ef4444; }

/* Stream Anchor Animation Styles */
.loading-anchor { display: flex; align-items: center; justify-content: center; gap: 8px; padding: 16px; font-size: 12px; color: #52525b; font-family: monospace; }
.pulse-dot { width: 6px; height: 6px; background-color: #22c55e; border-radius: 50%; animation: pulse 1.2s infinite; }
@keyframes pulse { 0% { opacity: 0.3; } 50% { opacity: 1; } 100% { opacity: 0.3; } }
</style>

