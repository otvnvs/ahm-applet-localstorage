<template>
  <div class="modal-backdrop" @click.self="$emit('close')">
    <div class="modal-box">
      <h3>{{ isEdit ? 'Edit Storage Value' : 'Create Storage Key' }}</h3>
      
      <div class="form-item">
        <label>Key Name</label>
        <input type="text" v-model="localKey" :disabled="isEdit" placeholder="Key name identifier" />
      </div>

      <div class="form-item">
        <label>Value Payload Data</label>
        <textarea v-model="localValue" placeholder="Plain text string or stringified {JSON} object" rows="5"></textarea>
      </div>

      <div class="modal-footer">
        <button class="btn btn-cancel" @click="$emit('close')">Cancel</button>
        <button class="btn btn-save" @click="saveEntry">Save Changes</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  activeItem: { type: Object, default: () => ({ key: '', value: '' }) },
  isEdit: { type: Boolean, default: false }
});
const emit = defineEmits(['close', 'save']);

const localKey = ref(props.activeItem.key);
const localValue = ref(props.activeItem.value);

function saveEntry() {
  if (!localKey.value.trim()) return;
  emit('save', { key: localKey.value.trim(), value: localValue.value });
}
</script>

<style scoped>
.modal-backdrop { position: fixed; inset: 0; background-color: rgba(7, 7, 8, 0.85); display: flex; align-items: center; justify-content: center; z-index: 999; padding: 16px; }
.modal-box { width: 100%; max-width: 380px; background-color: #141416; border: 1px solid #212124; border-radius: 6px; padding: 16px; box-shadow: 0 8px 24px rgba(0,0,0,0.6); }
h3 { margin: 0 0 14px 0; font-size: 15px; font-weight: 600; color: #ffffff; }
.form-item { display: flex; flex-direction: column; gap: 4px; margin-bottom: 12px; }
label { font-size: 11px; color: #7c7c82; font-weight: 500; }
input, textarea { width: 100%; background-color: #1a1a1c; border: 1px solid #242427; border-radius: 4px; padding: 8px; color: #ffffff; font-size: 13px; outline: none; box-sizing: border-box; }
textarea { font-family: monospace; resize: none; }
input:disabled { background-color: #0e0e10; color: #55555a; border-color: #18181a; }
.modal-footer { display: flex; justify-content: flex-end; gap: 8px; margin-top: 16px; }
.btn { border: none; padding: 8px 14px; border-radius: 4px; font-size: 12px; font-weight: 600; cursor: pointer; }
.btn-cancel { background-color: #212124; color: #a1a1aa; }
.btn-save { background-color: #212124; border: 1px solid #2e2e33; color: #ffffff; }
.btn-save:active { background-color: #2a2a2e; }
</style>

