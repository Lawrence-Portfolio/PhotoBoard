<template>
    <div class="loader-panel">
        <div class="loader-top-bg">
            <div class="loader-popcorn image"></div>
        </div>
        <div class="loader-body">
            <div class="load-body-wrap">
                <div class="load-strip">
                    <div class="loading" :style="{ width: loadingPercentage + '%' }"></div>
                </div>
                <div class="loading-text image"></div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data: () => ({
        loadingPercentage: 0
    }),
    methods: {
        loadingStart() {
            console.log('loading-start')
            let end = 100
            let start = 1
            let loadingInterval = setInterval(() => {
                if(start <= end) {
                    this.loadingPercentage = start
                    start++
                } else {
                    clearInterval(loadingInterval)
                    this.loadingEnd()
                }
            }, 40)
        },
        loadingEnd() {
            console.log('loading-end')
            this.route(2)
        },
        route(int) {
            this.$emit('route', int)
        }
    },
    mounted() {
        setTimeout(() => {
            this.loadingStart()
        }, 500)
    }
}
</script>