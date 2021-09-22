<template>
    <div class="loader-panel">
        <div class="loader-top-bg">
            <div class="loader-popcorn"></div>
        </div>
        <div class="loader-body">
            <div class="load-strip">
                <div class="loading" :style="{ width: loadingPercentage + '%' }"></div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data: () => ({
        loadingPercentage: 0
    }),
    props: {
        seconds: {
            type: Number,
            default: 1
        }
    },
    methods: {
        loadingStart() {
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
            this.$router.push('/take-photo')
        }
    },
    mounted() {
        setTimeout(() => {
            this.loadingStart()
        }, 500)
    }
}
</script>
