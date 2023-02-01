<template>
    <div>
    <!-- main Atropos container (required), add your custom class here -->
        <atropos class="my-atropos">
            <!-- scale container (required) -->
            <div class="atropos-scale">
                <!-- rotate container (required) -->
                <div class="atropos-rotate">
                    <!-- inner container (required) -->
                    <div class="atropos-inner">
                        <div class="wrapper" data-atropos-offset="5">
                            <h4>Quiz Game</h4>
                            <button id="start-button"
                            @click="$emit('startGame')">
                            Start
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </atropos>
    </div>
</template>

<script>

import Atropos from 'atropos/vue';

export default {
    components: {
        Atropos,
    }
}

</script>

<style scoped>

.my-atropos {
    width: 50vw;
    height: 50vh;
    
    color: var(--secondary-color);
    font-size: 6.8rem;
}

.atropos-rotate {
    background-color: var(--primary-color);
    border-radius: 2.5rem;
}

.atropos-inner {
    border-radius: 2.5rem;
}

.wrapper {
    width: 100%;
    height: 100%;

    padding: 1.5rem;

    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-around;

    text-align: center;
}

#start-button {
    width: 20vw;
    min-width: 15rem;
    max-width: 20rem;

    padding: 1.5rem 1.5rem;
    color: var(--black);

    border: none;
    background-color: var(--white);
    border-radius: 1rem;

    font-size: 1.6rem;
    font-family: 'Newake', sans-serif;
}

#start-button:hover {
    cursor: pointer;
    background-color: rgba(255, 255, 255, 0.6);
}

@media (max-width: 425px) {
    h4 {
        font-size: 5rem;
    }
}

</style>