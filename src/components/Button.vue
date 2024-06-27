<script setup>
import { computed } from "vue"
import { cva } from 'class-variance-authority';

const props = defineProps({
    leftIcon: Object,
    rightIcon: Object,
    loading: Boolean,
    disabled: Boolean,
    as: {
        type: [String, Object],
        default: "button"
    },
    intend: {
        type: String,
        validator: (val) => ['primary', "secondary", 'danger', 'text'].includes(val),
        default: "secondary"
    }
})

const buttonClass = computed(() => {
    return cva("inline-flex items-center  text-sm rounded min-h-[32px] px-3 py-0.5 font-semibold ", {
        variants: {
            intend: {
                primary: "bg-black text-white hover:bg-gray-800",
                secondary: "bg-black/5 hover:bg-black/10 text-gray-700",
                danger: "bg-red-600 texxt-white hover:bg-red-500",
                text: "text-gray-700 hover:bg-black/5"
            },
            dsabled: {
                true: "!bg-gray-100 !text-gray-400 cursor-not-allowed",

            }
        }
    })({
        intend: props.intend,
        disabled: props.disabled
    });
})

</script>

<template>
    <component :is="props.as" :class="buttonClass" :disabled="props.disabled">
        <svg v-if="props.loading" class="animate-spin  h-5 w-5 absolute " xmlns="http://www.w3.org/2000/svg" fill="none"
            viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
            </path>
        </svg>
        <component :is="props.leftIcon" class="w-5 h-5 mr-2" :class="[props.loading && 'invisible']" />
        <span :class="[props.loading && 'invisible']">
            <slot />
        </span>
        <component :is="props.rightIcon" class="w-5 h-5 mr-2" :class="[props.loading && 'invisible']" />
    </component>
</template>