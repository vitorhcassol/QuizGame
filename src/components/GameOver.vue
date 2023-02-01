<template>
    <div class="modal-mask">
        <div class="modal-wrapper">
            <div class="modal-container"
            :class="result">

                <section>
                    <h4>YOU {{ result }}</h4>
                    <span> hit {{ hit }} - {{ miss }} miss </span>
                </section>

                <button
                id="modal-button-try-again"
                @click="$emit('close'),
                $emit('tryAgain')"> try again </button>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: ['result', 'hit', 'miss'],
}
</script>

<style scoped>

.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: table;
    transition: opacity 0.5s ease;
}

.modal-wrapper {
    display: table-cell;
    vertical-align: middle;
}

.modal-container {
    display: flex;
    flex-direction: column;

    margin: 0 auto;

    align-items: center;

    gap: 9rem;

    width: 75vw;
    max-width: 80rem;
    padding: 3rem 1.5rem;
    border-radius: 0.8rem;

    font-family: 'Newake', sans-serif;
    font-size: 8.4rem;

    box-shadow: 0 0.4rem 0.8rem rgba(0, 0, 0, 0.25);

    transition: all 0.3s ease;
}
.WIN {
    background-color: var(--success);
}
.LOSE {
    background-color: var(--error);
}


section {
    display: flex;
    flex-direction: column;

    gap: 3rem;

    align-items: center;

    text-align: center;
}

span {
    color: var(--white);
    font-size: 2.1rem;
}

#modal-button-try-again {
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

    box-shadow: 0 0.4rem 0.4rem rgba(0, 0, 0, 0.25);
}

#modal-button-try-again:hover {
    cursor: pointer;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */


.modal-enter-from, .modal-leave-to {
    opacity: 0;
}
.modal-enter-active .modal-container,
.modal-leave-active .modal-container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
}

</style>