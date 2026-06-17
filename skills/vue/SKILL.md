---
name: vue
description: Builds and maintains Vue.js applications. Use when working with Vue components, Composition API, Pinia, Vue Router, or Nuxt. Use when the user mentions Vue, Composition API, Options API, defineComponent, Pinia, or Nuxt.
---

# Vue.js

## Overview

Vue is a progressive framework that scales from a single `<script>` include to a full SPA or SSR app via Nuxt. Prefer the **Composition API with `<script setup>`** for all new components — it offers better TypeScript support, tree-shaking, and code organization than Options API.

**Current stable version:** Vue 3.x  
**Docs:** https://vuejs.org  
**Nuxt docs:** https://nuxt.com  
**Pinia docs:** https://pinia.vuejs.org

## When to Use

- Building or maintaining Vue 3 applications
- Migrating Vue 2 to Vue 3 or Options API to Composition API
- Working with Nuxt (SSR/SSG)
- State management with Pinia
- Vue Router setup and navigation guards

## Single File Components

### `<script setup>` (preferred)

```vue
<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { useUserStore } from '@/stores/user'

// Props
const props = defineProps<{
  userId: string
  label?: string
}>()

// Emits
const emit = defineEmits<{
  select: [user: User]
  close: []
}>()

// Reactive state
const loading = ref(false)
const user = ref<User | null>(null)

// Computed
const displayName = computed(() => user.value?.name ?? 'Unknown')

// Watchers
watch(() => props.userId, async (id) => {
  await fetchUser(id)
}, { immediate: true })

// Lifecycle
onMounted(() => {
  console.log('Mounted')
})

// Methods
async function fetchUser(id: string) {
  loading.value = true
  try {
    const store = useUserStore()
    user.value = await store.fetchById(id)
  } finally {
    loading.value = false
  }
}

function handleSelect() {
  if (user.value) emit('select', user.value)
}
</script>

<template>
  <div class="user-card">
    <p v-if="loading">Loading…</p>
    <template v-else-if="user">
      <h2>{{ displayName }}</h2>
      <button type="button" @click="handleSelect">
        {{ label ?? 'Select' }}
      </button>
    </template>
    <p v-else>User not found.</p>
  </div>
</template>
```

## Composables

Extract reusable stateful logic into composables (files in `composables/`):

```ts
// composables/useFetch.ts
import { ref, type Ref } from 'vue'

export function useFetch<T>(url: string) {
  const data: Ref<T | null> = ref(null)
  const error = ref<Error | null>(null)
  const loading = ref(false)

  async function execute() {
    loading.value = true
    error.value = null
    try {
      const res = await fetch(url)
      if (!res.ok) throw new Error(`HTTP ${res.status}`)
      data.value = await res.json()
    } catch (e) {
      error.value = e instanceof Error ? e : new Error(String(e))
    } finally {
      loading.value = false
    }
  }

  return { data, error, loading, execute }
}
```

Composable naming convention: always prefix with `use`.

## Pinia (State Management)

```ts
// stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  // State
  const users = ref<User[]>([])
  const currentUser = ref<User | null>(null)

  // Getters
  const userCount = computed(() => users.value.length)
  const isAuthenticated = computed(() => currentUser.value !== null)

  // Actions
  async function fetchById(id: string): Promise<User> {
    const res = await fetch(`/api/users/${id}`)
    if (!res.ok) throw new Error(`HTTP ${res.status}`)
    const user: User = await res.json()
    const idx = users.value.findIndex(u => u.id === id)
    if (idx >= 0) users.value[idx] = user
    else users.value.push(user)
    return user
  }

  function setCurrentUser(user: User | null) {
    currentUser.value = user
  }

  return { users, currentUser, userCount, isAuthenticated, fetchById, setCurrentUser }
})
```

```vue
<script setup>
import { useUserStore } from '@/stores/user'
import { storeToRefs } from 'pinia'

const store = useUserStore()
// storeToRefs preserves reactivity when destructuring
const { currentUser, isAuthenticated } = storeToRefs(store)
</script>
```

## Vue Router

```ts
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'
import { useAuthStore } from '@/stores/auth'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', component: () => import('@/views/HomeView.vue') },
    {
      path: '/dashboard',
      component: () => import('@/views/DashboardView.vue'),
      meta: { requiresAuth: true },
    },
    { path: '/:pathMatch(.*)*', component: () => import('@/views/NotFoundView.vue') },
  ],
})

router.beforeEach(to => {
  const auth = useAuthStore()
  if (to.meta.requiresAuth && !auth.isAuthenticated) {
    return { path: '/login', query: { redirect: to.fullPath } }
  }
})

export default router
```

## Template Directives

```html
<!-- Conditional -->
<p v-if="show">Shown when true</p>
<p v-else-if="other">Alternative</p>
<p v-else>Fallback</p>

<!-- v-show toggles display:none — use when toggling frequently -->
<p v-show="visible">Toggle often</p>

<!-- Loop — always use :key with a stable unique id -->
<ul>
  <li v-for="item in items" :key="item.id">{{ item.name }}</li>
</ul>

<!-- Events -->
<button @click="handleClick">Click</button>
<input @input.debounce="onSearch">

<!-- Modifiers -->
<form @submit.prevent="onSubmit">
<a @click.stop="handleAnchor">

<!-- Two-way binding -->
<input v-model="searchQuery">

<!-- v-model on components -->
<MyInput v-model="value" />
<!-- Equivalent to: -->
<MyInput :modelValue="value" @update:modelValue="value = $event" />
```

## Props and Emits Patterns

```ts
// Typed props with defaults
const props = withDefaults(defineProps<{
  title: string
  count?: number
  variant?: 'primary' | 'secondary'
}>(), {
  count: 0,
  variant: 'primary',
})

// Typed emits
const emit = defineEmits<{
  change: [value: string]
  'update:modelValue': [value: string]
}>()

// v-model support
const props = defineProps<{ modelValue: string }>()
const emit = defineEmits<{ 'update:modelValue': [value: string] }>()
const value = computed({
  get: () => props.modelValue,
  set: v => emit('update:modelValue', v),
})
```

## Performance

```vue
<!-- v-memo: skip re-render if deps unchanged -->
<div v-memo="[item.id, item.selected]">
  <ExpensiveChild :item="item" />
</div>
```

```ts
// Lazy-load heavy components
const HeavyChart = defineAsyncComponent(() =>
  import('./HeavyChart.vue')
)
```

- Use `shallowRef` for large objects you mutate manually (avoids deep reactive tracking)
- Use `markRaw` on non-reactive data (class instances, library objects) passed into reactive state
- Virtualize long lists — Vue doesn't do this automatically

## Nuxt-Specific Patterns

```ts
// Auto-imported composables in Nuxt — no explicit import needed
const { data, error } = await useFetch('/api/users')
const user = useState('user', () => null)
const config = useRuntimeConfig()
const route = useRoute()
const router = useRouter()
```

```vue
<!-- Nuxt data fetching in components -->
<script setup>
const { data: posts, status } = await useFetch('/api/posts', {
  lazy: true,
})
</script>

<template>
  <div v-if="status === 'pending'">Loading…</div>
  <ul v-else>
    <li v-for="post in posts" :key="post.id">{{ post.title }}</li>
  </ul>
</template>
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| Mutating props directly | Emit an event or use `v-model` pattern |
| Destructuring reactive objects without `storeToRefs` | Use `storeToRefs` or `toRefs` |
| `v-for` without `:key` | Always `:key` with stable unique value |
| Using index as `:key` in dynamic lists | Use item id |
| Watching a reactive object without `{ deep: true }` | Add `{ deep: true }` or watch specific property |
| Mixing Options API and Composition API in same component | Pick one per component |

## Checklist

- [ ] All new components use `<script setup>` with TypeScript
- [ ] Props typed via `defineProps<{}>()` — no untyped props
- [ ] `v-for` always has `:key` with a stable unique id
- [ ] State management uses Pinia, not `provide/inject` for global state
- [ ] Routes lazy-loaded with dynamic `import()`
- [ ] Navigation guards handle auth redirects
- [ ] No direct prop mutation — events or v-model used instead
- [ ] Composables extracted for logic reused across more than one component
