<template>
    <div>
        <h1>Тест купюроприемника</h1>
        <h2>{{ deviceIsReady ? 'Устройство готово к работе' : '' }}</h2>
        <p>{{ result === 0 ? 'Пожалуйста внесите купюру в купюроприемник' : 'Внесено: ' + result + 'KZT' }}</p>
        <p>{{ message }}</p>
        <p>Ошибка: <span class="error">[{{ error.code }}] {{ error.message }}</span></p>
    </div>
</template>

<script>
import SSP from '../libs/ssp/index.js'

console.log(SSP)



export default {
    data: () => ({
        deviceIsReady: false,
        result: 0,
        message: '',
        error: ''
    }),
    methods: {
        noteParse(note) {
            return parseInt(note.substring(0, note.length - 3))
        }
    },
    mounted() {
        const _self = this
        let ssp = new SSP({
            device: 'COM4', //device address
            type: "nv9usb", //device type
            currencies:[1,1,0,0,0,0,0] //currencies types acceptable. Here all but 200KZT
        });

        let notes = {
            1:"20KZT",
            2:"50KZT",
            3:"100KZT",
            4:"200KZT",
            5:"500KZT",
            6:"1000KZT",
            6:"5000KZT"
        };

        ssp.init(function(){
            console.log('init')
            ssp.on('ready', function(){
                ssp.enable();
                _self.deviceIsReady = true
                console.log("Device is ready");
            });
            ssp.on('read_note', function(note){
                if(note > 0) {
                    console.log("GOT",notes[note]);
                    _self.result = _self.result + _self.noteParse(notes[note])
                    if(note > 2) {
                        _self.message = "Автомт не принимает купюры номиналом больше 100"
                        // suddenly we decided that we don't need 1000 KZT
                        ssp.commands.exec("reject_banknote");
                    }
                }
            });
            ssp.on('disable', function(){
                console.log("disabled");
            });
            ssp.on('note_cleared_from_front', function(note){
                _self.message = "note_cleared_from_front: " + JSON.stringify(note)
                console.log("note_cleared_from_front", note);
            });
            ssp.on('note_cleared_to_cashbox', function(note){
                _self.message = "note_cleared_to_cashbox: " + JSON.stringify(note)
                console.log("note_cleared_to_cashbox", note);
            });
            ssp.on('credit_note', function(note){
                _self.message = "CREDIT: " + JSON.stringify(notes[note])
                console.log("CREDIT",notes[note]);
            });
            ssp.on("safe_note_jam", function(note){
                _self.message = "Jammed: " + JSON.stringify(note)
                console.log("Jammed",note);
                //TODO: some notifiaction, recording, etc.
            });
            ssp.on("unsafe_note_jam", function(note){
                _self.message = "Jammed inside: " + JSON.stringify(note)
                console.log("Jammed inside",note);
                //TODO: some notifiaction, recording, etc.
            });
            ssp.on("fraud_attempt", function(note){
                _self.message = "Fraud!: " + JSON.stringify(note)
                console.log("Fraud!",note);
                //TODO: some notifiaction, recording, etc.
            });
            ssp.on("stacker_full", function(note){
                _self.message = "I'm full, do something!: " + JSON.stringify(note)
                console.log("I'm full, do something!", note);
                ssp.disable();
                //TODO: some notifiaction, recording, etc.
            });
            ssp.on("note_rejected", function(reason){
                _self.message = 'Rejected: ' + JSON.stringify(reason)
                console.log("Rejected!",reason);
            });
            ssp.on("error", function(err){
                _self.error = { code: err.code ? err.code : '', message: err.message ? err.message : '' }
                console.log(err.code, err.message);
            });
        });
    }
}
</script>