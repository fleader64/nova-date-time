<template>
    <default-field :field="field" :errors="errors" :show-help-text="field.helpText != null">
        <template slot="field">
            <div class="flex items-center">
                <form-nova-date-time-picker
                    class="w-full form-control form-input form-input-bordered"
                    ref="dateTimePicker"
                    :dusk="field.attribute"
                    :name="field.name"
                    :placeholder="placeholder"
                    :dateFormat="pickerFormat"
                    :alt-format="pickerDisplayFormat"
                    :value="localizedValue"
                    :twelve-hour-time="usesTwelveHourTime"
                    :first-day-of-week="firstDayOfWeek"
                    :default-hour="pickerDefaultHour"
                    :default-minute="pickerDefaultMinute"
                    :default-seconds="pickerDefaultSeconds"
                    :class="errorClasses"
                    @change="handleChange"
                    :disabled="isReadonly"
                />

                <a
                    v-if="field.nullable"
                    @click.prevent="$refs.dateTimePicker.clear()"
                    href="#"
                    :title="__('Clear value')"
                    tabindex="-1"
                    class="p-1 px-2 cursor-pointer leading-none focus:outline-none"
                    :class="{
            'text-50': !value.length,
            'text-black hover:text-danger': value.length,
          }"
                >
                    <icon type="x-circle" width="22" height="22" viewBox="0 0 22 22"/>
                </a>

                <span class="text-80 text-sm ml-2">({{ userTimezone }})</span>
            </div>
        </template>
    </default-field>
</template>

<script>
//import { FormField, HandlesValidationErrors } from 'laravel-nova'

import {
    FormField,
    HandlesValidationErrors,
    InteractsWithDates,
} from 'laravel-nova'

export default {

    mixins: [HandlesValidationErrors, FormField, InteractsWithDates],

    data: () => ({localizedValue: ''}),

    methods: {
        /*
         * Set the initial value for the field
         */
        setInitialValue() {
            // Set the initial value of the field
            this.value = this.field.value || ''

            // If the field has a value let's convert it from the app's timezone
            // into the user's local time to display in the field
            if (this.value !== '') {
                this.localizedValue = this.fromAppTimezone(this.value)
            }
        },

        /**
         * On save, populate our form data
         */
        fill(formData) {
            formData.append(this.field.attribute, this.value || '')
        },

        /**
         * Update the field's internal value when it's value changes
         */
        handleChange(value) {
            this.value = this.toAppTimezone(value)
        },
    },

    computed: {
        firstDayOfWeek() {
            return this.field.firstDayOfWeek || 0
        },

        format() {
            return this.field.format || 'YYYY-MM-DD HH:mm:ss'
        },

        placeholder() {
            return this.field.placeholder || moment().format(this.format)
        },

        pickerFormat() {
            return this.field.pickerFormat || 'Y-m-d H:i:S'
        },

        pickerDisplayFormat() {
            return this.field.pickerDisplayFormat || 'Y-m-d H:i:S'
        },

        pickerDefaultHour() {
            return this.field.pickerDefaultHour || 0
        },

        pickerDefaultMinute() {
            return this.field.pickerDefaultMinute || 0
        },

        pickerDefaultSeconds() {
            return this.field.pickerDefaultSeconds || 0
        }
    },
}
</script>
