<template>
  <div class="modal-backdrop" @click.self="$emit('close')">
    <div class="modal-box">
      <h2>{{ isEdit ? 'Modify Storage Key' : 'Create Storage Key' }}</h2>
      
      <div class="form-group">
        <label>Key Name</label>
        <input 
          type="text" 
          v-model="localKey" 
          :disabled="isEdit"
          placeholder="e.g. app_config"
          ref="keyInput"
        />
      </div>

      <div class="form-group">
        <div class="label-row">
          <label>Value Data</label>
          <button class="format-btn" v-if="hasValue" @click="togglePrettyJson">
            {{ isPretty ? 'Flatten Text' : 'Prettify JSON' }}
          </button>
        </div>
        <textarea 
          v-model="localValue" 
          placeholder='e.g. {"theme": "dark"} or pure string content'
          rows="8"
        ></textarea>
      </div>

      <p v-if="errorMsg" class="error-msg">❌ {{ errorMsg }}</p>

      <div class="modal-footer">
        <button class="btn btn-secondary" @click="$emit('close')">Cancel</button>
        <button class="btn btn-save" @click="saveEntry">Save Changes</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const props = defineProps({
  activeItem: { type: Object, default: () => ({ key: '', value: '' }) },
  isEdit: { type: Boolean, default: false }
});
const emit = defineEmits(['close', 'save']);

const localKey = ref(props.activeItem.key);
const localValue = ref(props.activeItem.value);
const errorMsg = ref('');
const isPretty = ref(false);
const keyInput = ref(null);

const hasValue = computed(() => localValue.value && localValue.value.trim().length > 0);

function togglePrettyJson() {
  try {
    if (isPretty.value) {
      // Flatten down
      const parsed = JSON.parse(localValue.value);
      localValue.value = JSON.stringify(parsed);
      isPretty.value = false;
    } else {
      // Prettify out
      const parsed = JSON.parse(localValue.value);
      localValue.value = JSON.stringify(parsed, null, 2);
      isPretty.value = true;
    }
    errorMsg.value = '';
  } catch (e) {
    errorMsg.value = 'Value is not valid structural JSON text.';
  }
}

function saveEntry() {
  if (!localKey.value || localKey.value.trim().length === 0) {
    errorMsg.value = 'Key identifier cannot be empty.';
    return;
  }
  emit('save', { key: localKey.value.trim(), value: localValue.value });
}

onMounted(() => {
  if (!props.isEdit && keyInput.value) {
    keyInput.value.focus();
  }
});
</script>

<style scoped>
.modal-backdrop { position: fixed; inset: 0; background-color: rgba(7, 7, 8, 0.8); backdrop-filter: blur(4px); display: flex; align-items: center; justify-content: center; z-index: 999; padding: 16px; }
.modal-box { width: 100%; max-width: 460px; background-color: #141416; border: 1px solid #27272a; border-radius: 16px; padding: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
h2 { margin: 0 0 16px 0; font-size: 18px; font-weight: 600; color: #ffffff; }
.form-group { display: flex; flex-direction: column; gap: 6px; margin-bottom: 14px; }
.label-row { display: flex; justify-content: space-between; align-items: center; }
label { font-size: 12px; font-weight: 500; color: #a1a1aa; }
.format-btn { background: none; border: none; color: #60a5fa; font-size: 11px; cursor: pointer; padding: 0; }
input, textarea { width: 100%; background-color: #1c1c1e; border: 1px solid #27272a; border-radius: 8px; padding: 10px; color: #ffffff; font-size: 14px; outline: none; box-sizing: border-box; }
textarea { font-family: monospace; resize: none; font-size: 12px; }
input:focus, textarea:focus { border-color: #44444f; }
input:disabled { background-color: #111113; color: #71717a; border-color: #1c1c1e; }
.error-msg { font-size: 12px; color: #ef4444; margin: -4px 0 12px 0; }
.modal-footer { display: flex; justify-content: flex-end; gap: 10px; margin-top: 20px; }
.btn { border: none; padding: 10px 16px; border-radius: 8px; font-size: 13px; font-weight: 600; cursor: pointer; }
.btn-secondary { background-color: #27272a; color: #ffffff; }
.btn-secondary:active { background-color: #3f3f46; }
.btn-save { background-color: #22c55e; color: #000000; }
.btn-save:active { background-color: #16a34a; }
</style>

