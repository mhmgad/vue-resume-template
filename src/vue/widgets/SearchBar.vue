<template>
  <div class="search-bar-container">
    <img src="/images/icons/gemini-logo.png" alt="Gemini Logo" class="gemini-logo" />
    <input
      placeholder="Ask AI about Mohamed!" 
      id="searchWidgetTrigger"
      :class="['search-bar', { 'search-bar-large': large }]"
      @click="openWidget"
      readonly
    />
  </div>
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
.search-bar-container {
  display: flex;
  align-items: left;
  width: 100%;
  max-width: 90vw;
  margin: 0 auto;
}
.gemini-logo {
  width: 5%;
  margin-right: 18px;
  flex-shrink: 0;
}
.search-bar {
  padding: 18px 16px;
  border-radius: 8px;
  border: 1.5px solid #ccc;
  font-size: 1.5rem;
  width: 100%;
  box-shadow: 0 4px 16px rgba(0,0,0,0.10);
  outline: none;
  transition: box-shadow 0.2s;
}
.search-bar:focus {
  box-shadow: 0 8px 32px rgba(0,0,0,0.16);
}
.search-bar-large {
  /* The parent .cover-search-bar will control most large styles, but you can add more here if needed */
}
</style> 