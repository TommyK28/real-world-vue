<script setup>
import EventCard from "../components/EventCart.vue";
import { ref, onMounted, computed, watchEffect, watch } from "vue";
import EventService from "../services/EventServices";

const props = defineProps(["page"]);
const events = ref(null);
const totalEvents = ref(0);
const page = computed(() => props.page);
const eventsLimit = 2;

const hasNextPage = computed(() => {
    const totalPages = Math.ceil(totalEvents.value / eventsLimit);
    return page.value < totalPages;
});

onMounted(() => {
    watchEffect(() => {
        EventService.getEvents(eventsLimit, page.value)
            .then((response) => {
                events.value = response.data;
                totalEvents.value = response.headers["x-total-count"];
            })
            .catch((error) => {
                console.log(error);
            });
    });
});
</script>

<template>
    <h1>Events For Good</h1>
    <div class="events">
        <EventCard
            v-for="event in events"
            :key="event.id"
            :event="event"
        ></EventCard>

        <div class="pagination">
            <router-link
                class="page-prev"
                :to="{ name: 'event-list', query: { page: page - 1 } }"
                rel="prev"
                v-if="page != 1"
            >
                Prev page
            </router-link>

            <router-link
                class="page-next"
                :to="{ name: 'event-list', query: { page: page + 1 } }"
                rel="next"
                v-if="hasNextPage"
            >
                Next page
            </router-link>
        </div>
    </div>
</template>

<style scoped>
.events {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.pagination {
    display: flex;
    width: 290px;
}
.pagination a {
    flex: 1;
    text-decoration: solid underline transparent;
    color: #2c3e50;
    transition: 0.3s ease-in;
}

.pagination a:hover {
    text-decoration: underline;
}

.page-prev {
    text-align: left;
}

.page-next {
    text-align: right;
}
</style>
