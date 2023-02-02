<template>
    <div class="wrapper">
        <div v-for="(item, index) in score"
        :key="index"
        :class="item">
        </div>        
    </div>
</template>

<script>

export default {
    props: ['score']
}
</script>

<style scoped>
.miss, .hit {
    height: 2rem;
    width: 2rem;
    border-radius: 50%;
}

.miss {
    background-color: var(--error);
}

.hit {
    background-color: var(--success);
}

.wrapper {
    display: flex;

    gap: 1.5rem;
}
</style>