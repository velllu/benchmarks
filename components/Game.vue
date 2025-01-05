<script setup lang="ts">
    defineProps<{
        tutorial: string,
        name: string
    }>();

    enum State {
        Tutorial,
        InProgress,
        Done
    }
    
    let state = ref(State.Tutorial);
    let score = 0;

    function end_game(new_score: number) {
        state.value = State.Done;
        score = new_score;
    }
</script>

<template>
    <a href="/"><button>← Back</button></a>
    <h1>{{ name }}</h1>

    <div id="container">
        <main>
            <!-- Tutorial explaining how the game works before actually playing -->
            <div id="tutorial" v-if="state == State.Tutorial">
                <p>{{ tutorial }}</p>
                <button @click="state = State.InProgress">Got it!</button>
            </div>

            <!-- The actual game is specified by the user -->
            <div id="in-progress" v-else-if="state == State.InProgress">
                <slot :end_game="end_game"></slot>
            </div>

            <!-- When the game is finished -->
            <div id="done" v-else-if="state == State.Done">
                <p>Game ended, you scored {{ score }}!!</p>
                <button @click="state = State.Tutorial">Play again</button>
            </div>
        </main>
    </div>
</template>

<style scoped>
    #container {
        background-color: rgb(57, 123, 237);
        display: flex;
        justify-content: center;
        align-items: center;
        height: 20em;
    }

    main {
        width: 40%;
    }

    #tutorial p {
        text-align: justify;
    }
</style>