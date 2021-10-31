<template>
    <div v-if="load">
        <v-progress-circular
        class="d-flex justify-center"
        :size="70"
        :width="7"
        color="grey"
        indeterminate
        ></v-progress-circular>
    </div>
</template>

<script>
export default {
    props: ['loading'],
    computed: {
        load() {
            if(!this.loading) return null;
            const loading = this.loading;
            return loading;
        }
    }
}
</script>