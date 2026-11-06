<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

// ==========================================
// 1. TYPESCRIPT INTERFACES (Typisierung)
// ==========================================
interface User {
id: number
name: string
email: string
role: string
}

// ==========================================
// 2. PROPS & EMITS (Komponenten-Kommunikation)
// ==========================================
withDefaults(defineProps<{
title?: string
maxResults?: number
}>(), {
title: 'Benutzerverwaltung',
maxResults: 5
})

const emit = defineEmits<{
(e: 'select-user', user: User): void
}>()

// ==========================================
// 3. REAKTIVER STATE & LOGIK
// ==========================================
const users = ref<User[]>([])
const searchQuery = ref('')
const isLoading = ref(false)
const errorMessage = ref<string | null>(null)

// Lifecycle Hook: Daten beim Laden von einer API abrufen
onMounted(async () => {
isLoading.value = true
try {
const response = await fetch('https://jsonplaceholder.typicode.com/users')
if (!response.ok) throw new Error('Fehler beim Laden der Benutzer!')
users.value = await response.json()
} catch (err: any) {
errorMessage.value = err.message || 'Unbekannter Fehler'
} finally {
isLoading.value = false
}
})

// Computed Property: Live-Filter
const filteredUsers = computed(() => {
return users.value
.filter(user => user.name.toLowerCase().includes(searchQuery.value.toLowerCase()))
})

function handleSelect(user: User) {
emit('select-user', user)
}
</script>

<template>
<!-- Scoped Styles schützen vor globalem CSS-Chaos -->
<div class="user-search-widget">

<div class="widget-header">
<h2>{{ title }}</h2>
<span v-if="isLoading" class="badge">Lade Daten...</span>
</div>

<div v-if="errorMessage" class="error-box">
⚠️ {{ errorMessage }}
</div>

<div v-else class="search-controls">
<input
type="text"
v-model="searchQuery"
placeholder="Nach Namen suchen..."
class="search-input"
/>
</div>

<!-- Named Slot für flexiblen Zusatzinhalt -->
<div class="custom-banner">
<slot name="banner">
<p class="hint">💡 Tipp: Klicke auf einen Benutzer.</p>
</slot>
</div>

<ul class="user-list">
<li
v-for="user in filteredUsers"
:key="user.id"
class="user-item"
@click="handleSelect(user)"
>
<div class="user-info">
<strong>{{ user.name }}</strong>
<small>{{ user.email }}</small>
</div>
<span class="role-tag">Mitglied</span>
</li>
</ul>

</div>
</template>

<style scoped>
.user-search-widget {
max-width: 450px;
margin: 0 auto;
padding: 20px;
background: #f8fafc;
border-radius: 12px;
box-shadow: 0 4px 12px rgba(0,0,0,0.08);
font-family: system-ui, sans-serif;
}

.widget-header {
display: flex;
justify-content: space-between;
align-items: center;
margin-bottom: 16px;
}

.search-input {
width: 100%;
padding: 10px 14px;
border: 1px solid #cbd5e1;
border-radius: 8px;
font-size: 14px;
margin-bottom: 14px;
box-sizing: border-box;
}

.user-list {
list-style: none;
padding: 0;
margin: 0;
}

.user-item {
display: flex;
justify-content: space-between;
align-items: center;
padding: 10px 12px;
background: white;
border: 1px solid #e2e8f0;
border-radius: 6px;
margin-bottom: 8px;
cursor: pointer;
transition: background 0.2s;
}

.user-item:hover {
background: #f1f5f9;
}

.user-info {
display: flex;
flex-direction: column;
}

.user-info small {
color: #64748b;
}

.role-tag {
background: #e0f2fe;
color: #0369a1;
padding: 2px 8px;
border-radius: 12px;
font-size: 12px;
font-weight: 500;
}

.error-box {
background: #fee2e2;
color: #991b1b;
padding: 10px;
border-radius: 6px;
margin-bottom: 14px;
}
</style>