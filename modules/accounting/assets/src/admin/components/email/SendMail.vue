<template>
    <modal
        @close="closeModal"
        :title="'Send Mail'"
        :footer="true"
        :hasForm="true"
        :header="true"
    >
        <template slot="body">
            <div class="wperp-form-group wperp-row">
                <div class="wperp-col-sm-3 wperp-col-xs-12">
                    <label>{{ __('To', 'erp') }} <span class="wperp-required-sign">*</span></label>
                </div>
                <input-tag :placeholder="__('Add Emails', 'erp')" v-model="emails" validate="email"></input-tag>
            </div>
            <div class="wperp-form-group wperp-row">
                <div class="wperp-col-sm-3 wperp-col-xs-12">
                    <label>{{ __('Subject', 'erp') }}</label>
                </div>
                <div class="wperp-col-sm-9 wperp-col-xs-12">
                    <input type="text" v-model="subject" class="wperp-form-field" :placeholder="__('Enter Subject Here', 'erp')" />
                </div>
            </div>
            <div class="wperp-form-group wperp-row">
                <div class="wperp-col-sm-3 wperp-col-xs-12">
                    <label>{{ __('Message', 'erp') }}</label>
                </div>
                <div class="wperp-col-sm-9 wperp-col-xs-12">
                    <textarea v-model="message" class="wperp-form-field" :placeholder="__('Enter Your Message Here', 'erp')" rows="4"></textarea>
                </div>
            </div>
            <div class="wperp-row">
                <div class="wperp-col-sm-3 wperp-col-xs-12">
                    <label>{{ __('Attachment', 'erp') }} <span class="wperp-required-sign">*</span></label>
                </div>
                <div class="wperp-col-sm-9 wperp-col-xs-12">
                    <div class="form-check">
                        <label class="form-check-label mb-0">
                            <input class="form-check-input" v-model="attachment" type="checkbox">
                            <span class="form-check-sign"></span> <span class="field-label">{{ __('Attach as PDF', 'erp') }}</span>
                        </label>
                    </div>
                </div>
            </div>
        </template>
        <template slot="footer">
            <div class="buttons-wrapper text-right">
                <button class="wperp-btn btn--default" @click="closeModal">{{ __('Cancel', 'erp') }}</button>
                <button class="wperp-btn btn--primary" type="submit" @click.prevent="sendAsMail">{{ __('Send', 'erp') }}</button>
            </div>
        </template>
    </modal>
</template>

<script>
import HTTP from 'admin/http';
import Modal from 'admin/components/modal/Modal.vue';
import InputTag from 'vue-input-tag';

export default {
    name: 'SendMail',

    components: {
        Modal,
        InputTag
    },

    props: {
        data: Object,
        type: String,
        userid: [Number, String]
    },

    data() {
        return {
            options: [],
            emails: [],
            subject: '',
            message: '',
            attachment: ''
        };
    },

    created() {
        HTTP.get(`people/${this.userid}`).then(response => {
            this.emails.push(response.data.email);
        });
    },

    methods: {
        closeModal() {
            this.$root.$emit('close');
        },

        addEmail(newEmail) {
            const email = {
                name: newEmail,
                code: newEmail.substring(0, 2) + Math.floor((Math.random() * 10000000))
            };
            this.emails.push(email);
        },

        sendAsMail() {
            HTTP.post(`/transactions/send-pdf/${this.$route.params.id}`, {
                trn_data: this.data,
                type: this.type,
                receiver: this.emails,
                subject: this.subject,
                message: this.message,
                attachment: this.attachment
            }).then(() => {
                this.showAlert('success', 'Mail Sent!');
                this.$store.dispatch('spinner/setSpinner', false);
            }).catch(error => {
                this.$store.dispatch('spinner/setSpinner', false);
                throw error;
            });
        }
    }
};
</script>

<style>
    .vue-input-tag-wrapper .input-tag {
        border-radius: 2px;
        display: inline-block;
        font-size: 13px;
        font-weight: 400;
        margin-bottom: 4px;
        margin-right: 4px;
        text-align: left;
    }
</style>
