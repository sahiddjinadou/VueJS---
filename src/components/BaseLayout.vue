<template>
    <div class="container">
        <header>
            <!-- Nous voulons le contenu du header ici -->
            <slot name="header" text="dutext" count="48"></slot>
        </header>
        <main>
            <!-- Nous voulons le contenu principal ici -->
            <slot>
                {{ test }} au petit fils // {{ el }}
            </slot>

        </main>
        <footer>
            <!-- Nous voulons le contenu du footer ici -->
            <slot name="footer"></slot>
        </footer>
    </div>
</template>

<script setup lang='ts'>
import { inject } from 'vue';

defineProps({
    test:String
})
const el = inject('test')
</script>

<style scoped>
.container {
    background-color: #ffd;
    margin-block: 15px;
}

header {
    padding: 10px;
    background-color: #bbb;
}

main {
    padding: 10px;
    background-color: #aaa;
}

footer {
    padding: 10px;
    background-color: #cccd;
}
</style>