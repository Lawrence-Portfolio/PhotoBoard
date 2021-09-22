<template>
    <div id="app">
        <div :class="{'panel-logo': !isSmallLogo, 'panel-logo-small': isSmallLogo }" class="panel-logo image"></div>
        <Home v-if="route == 0" @route="incrementRoute"></Home>
        <Choice v-else-if="route == 1" @route="incrementRoute"></Choice>
        <TakePhoto v-else-if="route == 2" @route="incrementRoute" @set-photo="setPhoto"></TakePhoto>
        <ReadyPhoto 
            v-else-if="route == 3" 
            @route="incrementRoute" 
            @ssp-enable="sspEnable" 
            @ssp-disable="sspDisable" 
            :photo="photo" 
            :ssp="ssp" 
            :deviceIsReady="deviceIsReady"
            :result="result"
            :message="message"
            :error="error"
        >
        </ReadyPhoto>
        <Sharing v-else-if="route == 4" @route="incrementRoute"></Sharing>
        <SendPhotoToEmail v-else-if="route == 5" @route="incrementRoute"></SendPhotoToEmail>
    </div>
</template>

<script>
    import SSP from './libs/ssp/index.js'

    import Home from './views/Home.vue'
    import Choice from './views/Choice.vue'
    import TakePhoto from './views/TakePhoto.vue'
    import ReadyPhoto from './views/ReadyPhoto.vue'
    import Sharing from './views/Sharing.vue'
    import SendPhotoToEmail from './views/SendPhotoToEmail.vue'
    

    export default {
        components: {
            Home,
            Choice,
            TakePhoto,
            ReadyPhoto,
            Sharing,
            SendPhotoToEmail
        },
        data: () => ({
            time: 0,
            interval: null,
            route: 0,
            photo: null,
            isSmallLogo: false,
            ssp: null,


            // BillAcceptor
            deviceIsReady: false,
            result: 0,
            message: '',
            error: ''
        }),
        methods: {
            noteParse(note) {
                return parseInt(note.substring(0, note.length - 3))
            },
            toggleTimer() {
                if (this.isRunning) {
                    clearInterval(this.interval);
                    console.log('timer stops');
                } else {
                    this.interval = setInterval(this.incrementTime, 1000);
                }
            },
            incrementTime() {
                this.time = parseInt(this.time) + 1;
            },
            incrementRoute(int) {
                this.route = int
            },
            setPhoto(image) {
                this.photo = image
            },
            billAcceptorInit() {
                let _self = this
                let notes = {
                    1:"20KGS",
                    2:"50KGS",
                    3:"100KGS",
                    4:"200KGS",
                    5:"500KGS",
                    6:"1000KGS",
                    7:"5000KGS",
                };

                this.ssp = new SSP({
                    device: 'COM4', //device address
                    type: "nv9usb", //device type
                    currencies:[1,1,1,1,1,1,1] //currencies types acceptable
                });

                this.ssp.init(true, function(){
                    _self.ssp.on('ready', function(){
                        _self.ssp.enable();
                        _self.deviceIsReady = true
                        console.log("Device is ready");
                    });
                    _self.ssp.on('read_note', function(note){
                        if(note > 0) {
                            console.log("GOT", notes[note]);
                            _self.result = _self.result + _self.noteParse(notes[note])
                            if(note != 3 || note != 4) {
                                _self.message = "Автомат принимает купюры номиналом в 100 KGS или 200 KGS"
                                _self.ssp.commands.exec("reject_banknote");
                            }
                        }
                    });
                    _self.ssp.on('disable', function(){
                        console.log("disabled");
                    });
                    _self.ssp.on('note_cleared_from_front', function(note){
                        _self.message = "note_cleared_from_front: " + JSON.stringify(note)
                        console.log("note_cleared_from_front", note);
                    });
                    _self.ssp.on('note_cleared_to_cashbox', function(note){
                        _self.message = "note_cleared_to_cashbox: " + JSON.stringify(note)
                        console.log("note_cleared_to_cashbox", note);
                    });
                    _self.ssp.on('credit_note', function(note){
                        _self.message = "CREDIT: " + JSON.stringify(notes[note])
                        console.log("CREDIT", notes[note]);
                    });
                    _self.ssp.on("safe_note_jam", function(note){
                        _self.message = "Jammed: " + JSON.stringify(note)
                        console.log("Jammed",note);
                        //TODO: some notifiaction, recording, etc.
                    });
                    _self.ssp.on("unsafe_note_jam", function(note){
                        _self.message = "Jammed inside: " + JSON.stringify(note)
                        console.log("Jammed inside",note);
                        //TODO: some notifiaction, recording, etc.
                    });
                    _self.ssp.on("fraud_attempt", function(note){
                        _self.message = "Fraud!: " + JSON.stringify(note)
                        console.log("Fraud!",note);
                        //TODO: some notifiaction, recording, etc.
                    });
                    _self.ssp.on("stacker_full", function(note){
                        _self.message = "I'm full, do something!: " + JSON.stringify(note)
                        console.log("I'm full, do something!", note);
                        _self.ssp.disable();
                        //TODO: some notifiaction, recording, etc.
                    });
                    _self.ssp.on("note_rejected", function(reason){
                        _self.message = 'Rejected: ' + JSON.stringify(reason)
                        console.log("Rejected!",reason);
                    });
                    _self.ssp.on("error", function(err){
                        console.log(err.code, err.message);
                        _self.error = { code: err.code ? err.code : '', message: err.message ? err.message : '' }
                    });
                });
            },
            sspEnable() {
                
                // this.ssp.enable()
                // console.log('enable')
            },
            sspDisable () {
                
                // this.ssp.disable()
                // console.log('disable')
            }
        },
        mounted() {
            this.billAcceptorInit()

            // this.toggleTimer()
            window.addEventListener('touchend', () => {
                this.time = 0
            })
            window.addEventListener('click', () => {
                this.time = 0
            })
            // this.route = 5
        },
        watch: {
            time(val) {
                console.log(val)
                if(val == 60 && this.route != 0) {
                    this.time = 0
                    this.route = 0
                }
            },
            route(val) {
                if(val == 2 || val == 3) {
                    this.isSmallLogo = true
                } else {
                    this.isSmallLogo = false
                }
            }
        }
    }
</script>