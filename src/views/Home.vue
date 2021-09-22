<template>
    <div class="main-panel-warpper image">
        <div class="main-panel image"></div>
        <div class="main-panel-text image"></div>
        <a href="#" @click.prevent="route(1)" class="main-panel-link">
            <span class="main-panel-link-text image"></span>
        </a>
    </div>
</template>

<script>
export default {
    name: 'Home',
    components: {},
    methods: {
        route(int) {
            this.$emit('route', int)
        }
    }
}
</script>
