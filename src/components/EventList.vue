<script setup lang="ts">
import { computed, ref } from "vue";
import type { CampusEvent, Category } from "../types";
import EventCard from "./EventCard.vue";

const props = defineProps<{ events: CampusEvent[] }>();
const emit = defineEmits<{
  (e: "book", id: number): void;
}>();

const search = ref("");
const category = ref<Category | "all">("all");

const visible = computed(() => {
  const query = search.value.toLowerCase();
  return props.events.filter(event => {
    const matchesCategory =
      category.value === "all" || event.category === category.value;
    const matchesSearch =
      event.title.toLowerCase().includes(query) ||
      event.location.toLowerCase().includes(query);
    return matchesCategory && matchesSearch;
  });
});
</script>

<template>
  <section>
    <div class="filters">
      <input
        v-model="search"
        type="search"
        placeholder="Search title or location"
        aria-label="Search"
      />
      <select v-model="category" aria-label="Category">
        <option value="all">All categories</option>
        <option value="talk">Talks</option>
        <option value="workshop">Workshops</option>
        <option value="social">Social</option>
        <option value="sport">Sport</option>
      </select>
    </div>

    <p class="count">{{ visible.length }} of {{ props.events.length }} events</p>

    <div v-if="visible.length" class="grid">
      <EventCard
        v-for="event in visible"
        :key="event.id"
        :event="event"
        @book="emit('book', $event)"
      />
    </div>
    <p v-else class="empty">No events match your search.</p>
  </section>
</template>

<style scoped>
.filters { display: flex; gap: 8px; flex-wrap: wrap; }
.filters input { flex: 1; min-width: 200px; }
.count { color: #6b7280; }
.grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 16px; }
.empty { padding: 32px; text-align: center; background: white; border-radius: 10px; }
</style>
