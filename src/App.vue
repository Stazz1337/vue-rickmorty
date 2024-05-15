<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';

const characters = ref([]);
const totalPages = ref(1);
const currentPage = ref(1);
const filterName = ref('');
const statusFilter = ref('');

const fetchCharacters = async () => {
    try {
        let url = `https://rickandmortyapi.com/api/character/?page=${currentPage.value}`;
        if (filterName.value) {
            url += `&name=${filterName.value}`; 
        }
        if (statusFilter.value) {
            url += `&status=${statusFilter.value}`;
        }
        const response = await axios.get(url);
        characters.value = response.data.results;
        totalPages.value = response.data.info.pages;
    } catch (error) {
        console.error(error);
    }
};

const applyFilters = () => {
    currentPage.value = 1;
    fetchCharacters();
};

const nextPage = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value++;
        fetchCharacters();
    }
};

const previousPage = () => {
    if (currentPage.value > 1) {
        currentPage.value--;
        fetchCharacters();
    }
};

onMounted(fetchCharacters);
</script>

<template>
    <h1 class="title">The Rick and Morty API</h1>

    <div class="filters">
        <input type="text" v-model="filterName" placeholder="Search by name" />
        <select v-model="statusFilter">
            <option value="">All</option>
            <option value="alive">alive</option>
            <option value="dead">dead</option>
        </select>
        <button @click="applyFilters">Apply</button>
    </div>

    <div class="gallery">
        <div v-for="character in characters" :key="character.id" class="gallery__wrapper">
            <img :src="character.image" :alt="character.name" class="gallery__image" />

            <div class="gallery__info">
                <h2 class="gallery__title">{{ character.name }}</h2>
                <p class="gallery__status">Status: {{ character.status }}</p>
                <p class="gallery__location">Location: {{ character.location.name }}</p>
            </div>
        </div>
    </div>

    <div v-if="totalPages > 1" class="pagination">
        <button @click="previousPage" :disabled="currentPage === 1">Previous</button>
        <span>Page {{ currentPage }} / {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
</template>

<style scoped lang="scss">
.title {
    text-align: center;
}

.filters,
.pagination {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin: 40px;
}

.gallery {
    margin: 0 auto;
    max-width: 1440px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(450px, 1fr));
    justify-items: center;
    gap: 10px;

    &__wrapper {
        display: flex;
        width: 450px;
    }

    &__info {
        padding: 10px;
        background: rgba(0, 0, 0, 0.5);
        color: white;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        width: 100%;
    }

    &__title,
    &__status,
    &__location {
        margin: 0;
    }

    &__image {
        object-fit: cover;
        width: 250px;
        height: 250px;
    }
}
</style>
