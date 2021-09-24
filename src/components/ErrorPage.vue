<template>
    <div class="container p-4">
        <h1>We are sorry! :(</h1>
        <p>We have encountered an error and currently trying to resolve it.</p>
        <p>Please try again later. Sorry for the inconvenience caused.</p>
        <button type="button" class="btn btn-secondary mt-4" @click="closeError">Go Back</button>
    </div>
</template>

<script>
export default {
    name: "ErrorPage",
    methods: {
        closeError: function() {
            this.$emit("go-to-home");
        }
    }
}
</script>

<style scoped>
h1 {
    margin-bottom: 1.5rem;
}

p {
    font-weight: 400;
    font-size: large;
}
</style>