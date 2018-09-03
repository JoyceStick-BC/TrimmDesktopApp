<template lang="html">
    <div class="main">
        <button type="button" name="button" @click.prevent="logout">Sign out</button>
        <p>hello</p>
    </div>
</template>

<script>
export default {
    methods: {
        logout() {
            this.$router.push({
                path: 'landing-page'
            })
        }
    }
}
</script>

<style lang="css">
</style>
