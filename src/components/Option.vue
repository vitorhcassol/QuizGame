<template>
    <section class="col-b">
        <button
        v-for="(item, index) in this.answers" :key="index"
        class="option"
        :id="item"
        @click="$emit('sendAnswer', item)"
        >
            {{ item }}
        </button>
    </section>
</template>

<script>

export default {
    props: ['answers'],
    emits: ['sendAnswer']
}

</script>

<style scoped>

    .col-b {
        display: flex;
        flex-direction: column;

        justify-content: center;
        align-items: center;

        grid-area: B;

        padding: 3rem 0rem;

        gap: 1.5rem;
    }

    .option {
        background-color: var(--white);
        color: var(--black);
        opacity: 0.8;
        border-radius: 1rem;
        border: none;

        padding: 1rem;

        min-width: 20rem;

        text-align: center;
        font-weight: bold;

        transition: opacity 0.16s, background-color 0.1, color 0.1;
    }

    .option:hover {
        cursor: pointer;
        opacity: 1;
        box-shadow: 0rem 0.5rem 0.5rem var(--black-opacity);
    }

    .option:active {
        background-color: var(--black);
        color: var(--white);
    }

    .flash {
        animation-name: flash;
        animation-duration: 0.5s;
        animation-iteration-count: 3;

        background-color: var(--success);
        color: var(--white);
    }

    .error {
        background-color: var(--error);
        color: var(--white)
    }

    @keyframes flash {
        0% {
            background-color: var(--success);
            color: var(--white);
        }
        50% {
            background-color: var(--white);
            color: var(--black);
        }
        100% {
            background-color: var(--success);
            color: var(--white);
        }
    }

</style>