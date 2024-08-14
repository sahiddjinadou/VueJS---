<template>
    <div class="poche">
        <button class="fancy-btn">
            <slot text="je suis du text " count = "5644">
                contenu par defaut
            </slot> <!-- emplacement du slot -->
        </button>
        <BaseLayout :test="test" >
            <template #header >
                tête
            </template>
        </BaseLayout>
    </div>
</template>

<script setup lang='ts'>
import BaseLayout from './BaseLayout.vue';
defineProps({
    test:String
})
</script>

<style scoped>
.fancy-btn{
    padding: 10px;
    border: none;
    background-color: goldenrod;
}
.poche{
    border: 1px solid;
    background-color: #015235;
    color: aliceblue;
}

</style>