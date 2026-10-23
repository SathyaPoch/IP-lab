<script setup lang="ts">
import { computed, ref, watch } from "vue";
import EventList from "./components/EventList.vue";
import { initialEvents } from "./data/events";
import type { CampusEvent } from "./types";

const events = ref<CampusEvent[]>(initialEvents.map(e => ({ ...e })));

function readSavedBookings(): number[] {
  const saved = localStorage.getItem("myBookings");
  if (saved === null) return [];

  let parsed: unknown;
  try {
    parsed = JSON.parse(saved);
  } catch (error) {
    console.error("Could not parse saved bookings from localStorage.", error);
    return [];
  }

  if (
    !Array.isArray(parsed) ||
    !parsed.every(id => typeof id === "number" && Number.isInteger(id))
  ) {
    console.error("Saved bookings in localStorage have an invalid format.");
    return [];
  }
  return parsed;
}

const myBookings = ref<number[]>([]);
for (const id of readSavedBookings()) {
  const event = events.value.find(item => item.id === id);
  if (event && event.booked < event.capacity) {
    event.booked += 1;
    myBookings.value.push(id);
  }
}

const bookingCount = computed(() => myBookings.value.length);

watch(
  myBookings,
  bookings => localStorage.setItem("myBookings", JSON.stringify(bookings)),
  { deep: true, immediate: true },
);

function onBook(id: number) {
  const event = events.value.find(item => item.id === id);
  if (!event || event.booked >= event.capacity) return;

  event.booked += 1;
  myBookings.value.push(id);
}
</script>

<template>
  <main>
    <header>
      <h1>Campus Events</h1>
      <p class="mine">
        You have booked {{ bookingCount }}
        {{ bookingCount === 1 ? "seat" : "seats" }}.
      </p>
    </header>
    <EventList :events="events" @book="onBook" />
  </main>
</template>

<style scoped>
header { margin-bottom: 16px; }
.mine { color: #6b7280; margin: 0; }
</style>
