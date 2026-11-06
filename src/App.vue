<script setup lang="ts">
// Hier importieren wir unsere neue Komponente aus dem Ordner
import UserSearchWidget from './components/UserSearchWidget.vue'

function handleUserSelected(user: any) {
alert(`Ausgewählt: ${user.name}`)
}
</script>

<template>
<main style="padding: 40px;">
<!-- Hier wird das Widget aufgerufen -->
<UserSearchWidget
title="Mitarbeiter-Verzeichnis"
@select-user="handleUserSelected"
/>
</main>
</template>