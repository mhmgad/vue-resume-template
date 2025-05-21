<template>
  <input
    placeholder="Ask AI about Mohamed!" 
    id="searchWidgetTrigger"
    :class="['search-bar', { 'search-bar-large': large }]"
    @click="openWidget"
    readonly
  />
</template>

<script setup>
const props = defineProps({
  large: Boolean
})
function openWidget() {
  // Open the Google Gen App Builder widget
  const trigger = document.getElementById('searchWidgetTrigger');
  if (trigger) {
    trigger.click(); // This will trigger the widget if configured
  }
  // If a custom event is needed, emit here
}
</script>

<style scoped>
.search-bar {
  padding: 8px;
  border-radius: 4px;
  border: 1px solid #ccc;
  font-size: 1.1rem;
  width: 300px;
  max-width: 90vw;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  outline: none;
  transition: box-shadow 0.2s;
}
.search-bar:focus {
  box-shadow: 0 4px 16px rgba(0,0,0,0.12);
}
.search-bar-large {
  /* The parent .cover-search-bar will control most large styles, but you can add more here if needed */
}
</style> 