<template>
    <div>
        <v-text-field
                ref="text-field"
                v-if="field.type === 'text'"
                :name="field.name"
                v-validate="field.validation"
                :label="field.label"
                :value="value"
                :color="field.classes"
                @input="$emit('input', $event)"
        ></v-text-field>
        <div v-if="field.type === 'radio' && field.label">
            <label>{{ field.label }}</label>
        </div>
        <v-radio-group
                ref="radio-field"
                v-if="field.type === 'radio'"
                :name="field.name"
                v-validate="field.validation"
                :input-value="value"
                @change="$emit('input', $event)"
        >
            <v-radio v-for="v in field.values" :label="v.label" :value="v.value" :color="field.classes" :key="v.value"></v-radio>
        </v-radio-group>
        <v-select
                ref="select-field"
                v-if="field.type === 'select'"
                :items="field.values"
                :label="field.label"
                v-validate="field.validation"
                item-text="label"
                item-value="value"
                :name="field.name"
                :value="value"
                :color="field.classes"
                @input="$emit('input', $event)"
        ></v-select>
        <div v-if="field.type === 'checkbox'">
            <div v-if="field.label">
                <label>{{ field.label }}</label>
            </div>
            <template v-for="(v, index) in field.values" >
                <v-checkbox
                        ref="checkbox-field-first"
                        v-if="index === 0"
                        v-validate="field.validation"
                        :label="v.label"
                        :name="field.name"
                        :input-value="value"
                        @change="$emit('input', $event);"
                        :key="v.value"
                        :color="field.classes"
                        :value="v.value"
                ></v-checkbox>
                <v-checkbox
                        v-else
                        :label="v.label"
                        :input-value="value"
                        @change="$emit('input', $event);"
                        :name="field.name"
                        :key="v.value"
                        :value="v.value"
                ></v-checkbox>
            </template>

        </div>
    </div>
</template>

<script lang="ts">
    import Vue, { ComponentOptions } from 'vue';
    import {COMPONENTS_HELPER} from 'ts-vue-dom-helper'

    interface Value {
        value: any;
        label?: string;
        data?: any;
        classes?: string;
    }

    interface Field {
        classes?: string;
        name: string;
        validation?: string;
        label?: string;
        type: string;
        values?: Array<Value>;
    }

    interface VuetifyFieldComponent extends Vue{
        field: Field;
        value: any;
    }

    export {
        Field,
        Value
    }

    export default {
        props: {
            field: {
                type: Object,
                required: true
            },
            value: null
        },
        methods: {
            setErrors(errors : string[]){
                let $el : Vue = null;
                switch (this.field.type){
                    case 'text':
                        $el = <Vue>this.$refs['text-field'];
                        break;
                    case 'select':
                        $el = <Vue>this.$refs['select-field'];
                        break;
                    case 'radio':
                        $el = <Vue>this.$refs['radio-field'];
                        break;
                    case 'checkbox':
                        $el = <Vue>this.$refs['checkbox-field-first'][0];
                        break;
                    default:
                        console.trace();
                        throw 'VuetifyField unkown field'
                }

                COMPONENTS_HELPER.setComponentError($el, errors);
            }
        }
    } as ComponentOptions<VuetifyFieldComponent>;
</script>

