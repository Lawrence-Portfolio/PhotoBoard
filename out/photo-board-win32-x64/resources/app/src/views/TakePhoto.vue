<template>
    <div class="take-photo-panel">
        <div class="interactive-panel">
            <img src="../assets/img/man.png" style="display: none;" alt="">
            <div class="scene">
                <!-- <div class="man image"></div> -->
                <video id="web-camera" autoplay></video>
                <video v-if="isUserReady && isUserOnPosititon" id="background" muted autoplay>
                    <source src="https://biz-mark.ru/storage/app/media/test.webm" type="video/webm">
                </video>
                <canvas id="photo-result"></canvas>
            </div>
            <div v-show="!isStartСountdown" class="take-photo-message-group">
                <div v-show="!isUserReady" class="take-photo-message-1 bottom image"></div>
                <div v-show="isUserReady && !isUserOnPosititon" class="take-photo-message-2 bottom image"></div>
                <div v-show="isUserReady && isUserOnPosititon && !isStartСountdown" class="take-photo-message-3 top image"></div>
            </div>
        </div>
        <div v-show="isStartСountdown" class="countdown-panel">
            <div class="countdown">{{ countdown }}</div>
        </div>
    </div>
</template>
<script>

export default {
    data: () => ({
        isUserReady: false,
        isUserOnPosititon: false,
        isStartСountdown: false,

        countdown: 3,
        countdownInterval: null,

        webcamWidth: 0,
        webcamHeight: 0

    }),
    methods: {
        webCameraInit() {
            let video = document.querySelector('#web-camera')
            this.webcamWidth = video.offsetHeight
            this.webcamHeight = video.offsetWidth
            navigator.mediaDevices.getUserMedia({ video: true, audio: false })
            .then((stream) => {
                video.srcObject = stream
                video.play()
            })
            .catch((err) => {
                console.log(err)
            })
        },
        startCountdown() {
            this.countdownInterval = setInterval(() => {
                this.countdown--
                if(this.countdown == 0) {
                    clearInterval(this.countdownInterval)
                    this.createPhoto()
                }
            }, 1000)
        },
        createPhoto() {
            let canvas = document.querySelector('#photo-result'),
                ctx = canvas.getContext("2d"),
                background = document.querySelector('#background'),
                webCam = document.querySelector('#web-camera');

                canvas.width  = 1350
                canvas.height = 1350

                ctx.translate(0, 1350)
                
                ctx.rotate(-1.5708)
                ctx.drawImage(webCam, 0, 0, 1350, 1350, 0, 0, 3600, 3600);
                ctx.rotate(1.5708)
                ctx.translate(-0, -1350)
                ctx.drawImage(background, -300, 200, 1550, 900);
                

            let image = canvas.toDataURL("image/png").replace("image/png", "image/octet-stream")
            this.setPhoto(image)
            this.route(3)
        },
        route(int) {
            this.$emit('route', int)
        },
        setPhoto(image) {
            this.$emit('set-photo', image)
        }
    },
    mounted() {
        this.webCameraInit()
        setTimeout(() => {
            this.isUserReady = true
            setTimeout(() => {
                this.isUserOnPosititon = true
                setTimeout(() => {
                    this.isStartСountdown = true
                    document.querySelector('#background').pause()
                    this.startCountdown()
                }, 3000)
            }, 3000)
        }, 3000)
    }
}
</script>
