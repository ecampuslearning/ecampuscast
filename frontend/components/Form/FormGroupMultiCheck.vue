<template>
    <form-group
        v-bind="$attrs"
        :id="id"
    >
        <template
            v-if="label || slots.label"
            #label="slotProps"
        >
            <form-label
                :is-required="isRequired"
                :advanced="props.advanced"
                :high-cpu="props.highCpu"
            >
                <slot
                    name="label"
                    v-bind="slotProps"
                >
                    {{ label }}
                </slot>
            </form-label>
        </template>

        <template #default>
            <slot
                name="default"
                v-bind="{ id, field, model }"
            >
                <form-multi-check
                    :id="id"
                    v-model="model"
                    :name="name || id"
                    :options="options"
                    :radio="radio"
                    :stacked="stacked"
                >
                    <template
                        v-for="(_, slot) of useSlotsExcept(['default', 'label', 'description'])"
                        #[slot]="scope"
                    >
                        <slot
                            :name="slot"
                            v-bind="scope"
                        />
                    </template>
                </form-multi-check>
            </slot>

            <vuelidate-error
                v-if="isVuelidateField"
                :field="field"
            />
        </template>

        <template
            v-if="description || slots.description"
            #description="slotProps"
        >
            <slot
                v-bind="slotProps"
                name="description"
            >
                {{ description }}
            </slot>
        </template>
    </form-group>
</template>

<script setup lang="ts">
import VuelidateError from "./VuelidateError.vue";
import FormLabel, {FormLabelParentProps} from "~/components/Form/FormLabel.vue";
import FormGroup from "~/components/Form/FormGroup.vue";
import FormMultiCheck from "~/components/Form/FormMultiCheck.vue";
import useSlotsExcept from "~/functions/useSlotsExcept";
import {FormFieldEmits, FormFieldProps, useFormField} from "~/components/Form/useFormField";
import {useSlots} from "vue";
import {FormOption} from "~/functions/objectToNestedFormOptions.ts";

interface FormGroupMultiCheckProps extends FormFieldProps, FormLabelParentProps {
    id: string,
    name?: string,
    label?: string,
    description?: string,
    options: FormOption[],
    radio?: boolean,
    stacked?: boolean
}

const props = withDefaults(
    defineProps<FormGroupMultiCheckProps>(),
    {
        name: null,
        label: null,
        description: null,
        radio: false,
        stacked: false
    }
);

const slots = useSlots();

const emit = defineEmits<FormFieldEmits>();

const {model, isVuelidateField, isRequired} = useFormField(props, emit);
</script>
