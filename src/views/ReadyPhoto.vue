<template>
    <div class="ready-photo-panel">
        <div class="ready-photo-panel-wrap image">
            <div class="header-panel">
                <div class="you-photo-ready-text image"></div>
            </div>
            <div class="body-panel">
                <div class="user-photo-wrapper">
                    <div class="user-photo" :style="{ backgroundImage: `url(${photo})` }"></div>
                    <div class="user-photo-text image"></div>
                    <div class="debug">
                        <div v-if="deviceIsReady" class="message"> {{ result === 0 ? 'внесите оплату в  купюроприёмник' : 'Внесено: ' + result + ' KGS' }}</div>
                        <div class="message" v-if="message != ''">{{ message }}</div>
                        <div class="message" v-if="error != ''">Ошибка [{{ error.code }}] {{ error.message }}</div>
                    </div>
                    
                </div>
            </div>
            <div class="footer-panel">
                <div class="footer-panel-wrap">
                    <div class="buttons">
                        <button @click.prevent="route(4)">
                            Отправить фото<br> 
                            в социальной сети
                        </button>
                        <button @click.prevent="route(4)">
                            Отправить фото <br>
                            на Email
                        </button>
                    </div>
                    <button class="take-over" @click.prevent="route(0)">
                        Начать заново
                    </button>
                    <div class="photo-zone-bishkek-text image"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
// import SSP from '../libs/ssp/index.js'
export default {
    props: ['photo', 'ssp', 'deviceIsReady', 'message', 'result', 'error'],
    methods: {
        route(int) {
            this.$emit('route', int)
        },
        sspEnable() {
            this.$emit('ssp-enable')
        },
        sspDisable () {
            this.$emit('ssp-disable')
        }
    },
    mounted() {
        this.sspEnable()
    },
    beforeDestroy() {
        this.sspDisable()
    }
}
</script>
