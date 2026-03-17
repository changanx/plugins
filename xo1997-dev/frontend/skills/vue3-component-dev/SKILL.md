---
name: vue3-component-dev
description: Use when developing Vue3 components. Covers component design, Props/Emits, slots, and component testing.
---

# Vue3 Component Development

## Component Design Principles

1. **Single Responsibility** - One component, one purpose
2. **Props Down, Events Up** - Unidirectional data flow
3. **Composition over Inheritance** - Use composables
4. **Explicit over Implicit** - Clear props and events

## Component Template

```vue
<template>
  <div class="component-name">
    <!-- Template content -->
  </div>
</template>

<script setup>
// Imports
import { ref, computed, watch } from 'vue'

// Props
const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  disabled: {
    type: Boolean,
    default: false
  }
})

// Emits
const emit = defineEmits(['update:modelValue', 'change'])

// Reactive state
const localValue = ref(props.modelValue)

// Computed
const displayValue = computed(() => localValue.value.toUpperCase())

// Watch
watch(() => props.modelValue, (newVal) => {
  localValue.value = newVal
})

// Methods
function handleInput(event) {
  localValue.value = event.target.value
  emit('update:modelValue', localValue.value)
  emit('change', localValue.value)
}
</script>

<style scoped>
.component-name {
  /* Styles */
}
</style>
```

## Props Best Practices

```javascript
// Good: Explicit with type and default
const props = defineProps({
  items: {
    type: Array,
    default: () => []
  },
  loading: {
    type: Boolean,
    default: false
  }
})

// Avoid: Implicit any type
const props = defineProps(['items', 'loading'])
```

## Emits Best Practices

```javascript
// Good: Explicit event names
const emit = defineEmits(['update:modelValue', 'submit', 'cancel'])

// Event handlers
function handleSubmit() {
  emit('submit', { data: localData.value })
}
```

## v-model Implementation

```vue
<!-- CustomInput.vue -->
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>

<!-- Usage -->
<CustomInput v-model="text" />
```

## Slot Patterns

```vue
<!-- Component with slots -->
<template>
  <div class="card">
    <header class="card-header">
      <slot name="header">
        <h3>Default Title</h3>
      </slot>
    </header>
    <main class="card-body">
      <slot />
    </main>
    <footer class="card-footer">
      <slot name="footer" :actions="{ close, submit }">
        <button @click="close">Close</button>
      </slot>
    </footer>
  </div>
</template>

<!-- Usage with scoped slots -->
<Card>
  <template #header>Custom Title</template>
  <p>Body content</p>
  <template #footer="{ actions }">
    <button @click="actions.submit">Save</button>
  </template>
</Card>
```

## Component Testing

```javascript
// Component.spec.js
import { mount } from '@vue/test-utils'
import Component from './Component.vue'

describe('Component', () => {
  it('renders props correctly', () => {
    const wrapper = mount(Component, {
      props: { title: 'Test' }
    })
    expect(wrapper.text()).toContain('Test')
  })

  it('emits event on click', async () => {
    const wrapper = mount(Component)
    await wrapper.find('button').trigger('click')
    expect(wrapper.emitted('click')).toBeTruthy()
  })
})
```