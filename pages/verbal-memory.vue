<script setup lang="ts">
    import dictionary from "~/other/dictionary.txt?raw"
    const lists_of_words = dictionary.split("\n");

    let lists_of_seen_words: string[] = [];
    let current_word = ref();
    let attempts: Ref<number> = ref(3);
    let score: Ref<number> = ref(0);

    set_random_word();

    function set_random_word() {
        let random = Math.floor(Math.random() * 3);

        // We will choose at random if picking a random word or picking an already seen word
        if (random <= 1 || lists_of_seen_words.length == 0)
            current_word.value = lists_of_words[Math.floor(Math.random() * lists_of_words.length)];
        else
            current_word.value = lists_of_seen_words[Math.floor(Math.random() * lists_of_seen_words.length)];
    }

    function decrease_attempts(end_game_handler: any) {
        if (attempts.value == 1) {
            const old_score = score.value;

            // We have to reset the game in the case the user wants to try again
            attempts.value = 3;
            score.value = 0;
            lists_of_seen_words = [];

            end_game_handler(old_score);
        } else {
            attempts.value--;
        }
    }

    // TODO: The game could be optimized to not have loops because we already know if we
    // picked from the already seen word list or the dictionary, but we first must make it
    // impossibile for the same word to be extract twice from the dictionary

    function seen_word(end_game_handler: any) {
        for (const word of lists_of_seen_words) {
            if (current_word.value === word) {
                set_random_word();
                score.value++;
                return;
            }
        }

        set_random_word();
        decrease_attempts(end_game_handler);
    }

    function new_word(end_game_handler: any) {
        for (const word of lists_of_seen_words) {
            if (current_word.value === word) {
                set_random_word();
                decrease_attempts(end_game_handler);
                return;
            }
        }

        lists_of_seen_words.push(current_word.value);
        score.value++;
        set_random_word();
    }
</script>

<template>
    <Game
        name="Verbal Memory",
        tutorial="You will be shown an infinite amount of words, one by one, if you already saw the proposed word click 'seen' otherwise click 'new'. You can fail 3 times."
    >
        <template #default="{ end_game }">
            <p>Lives: {{ attempts }}</p>
            <p>Score: {{ score }}</p>

            <p>{{ current_word }}</p>

            <div id="buttons">
                <button @click="() => seen_word(end_game)" id="seen-btn">Seen</button>
                <button @click="() => new_word(end_game)">New</button>
            </div>
        </template>
    </Game>
</template>

<style scoped>
    button {
        width: 40%;
    }

    #seen-btn {
        margin-right: 10px;
    }
</style>