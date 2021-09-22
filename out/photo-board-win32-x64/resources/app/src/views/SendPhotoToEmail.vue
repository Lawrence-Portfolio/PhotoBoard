<template>
    <div class="ready-photo-panel">
        <div class="ready-photo-panel-wrap image">
            <div class="body-panel">
                <div class="send-photo-to-email-wrap">
                    <div class="enter-your-details-text image"></div>
                    <div class="line"></div>
                    <form class="send-photo-form">
                        <div class="inputs-group">
                            <div class="custom-input">
                                <input type="text" placeholder="ваше имя">
                            </div>
                            <div v-for="(item, index) in form.arrEmails" class="custom-input" :key="index">
                                <input v-model="item.email" type="email" placeholder="ваш email">
                                <div>{{ item.email }}</div>
                            </div>
                        </div>
                        <button type="submit">Отправить</button>
                    </form>
                </div>
            </div>
            <div class="footer-panel">
                <div class="footer-panel-wrap">
                    <div class="photo-zone-bishkek-text image"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { required, email, maxLength } from 'vuelidate/lib/validators'

export default {
    data: () => ({
        form: {
            name: '',
            arrEmails: [
                {
                    email: ''
                },
                {
                    email: ''
                }
            ]
        }
    }),
    methods: {
        route(int) {
            this.$emit('route', int)
        }
    },
    validations: {
        form: {
            name: {
                required
            },
            arrEmails:{ 
                required,
                maxLength: maxLength(3),
                $each: {
                    email: {
                        required, email
                    }
                }
            }
        }
    }
}
</script>