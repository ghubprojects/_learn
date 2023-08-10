<script setup>
import { computed, ref, watch } from 'vue';

const props = defineProps({
    size: {
        type: String,
        default: 'medium',
        validator(val) {
            return ['large', 'medium', 'small'].includes(val);
        }
    },
    width: {
        type: String,
        default: 'medium',
        validator(val) {
            return ['full', 'extra-large', 'large', 'medium', 'small', 'extra-small'].includes(val);
        }
    },
    id: {
        type: String,
        required: true
    },
    title: {
        type: String
    },
    placeholder: {
        type: String
    },
    value: {
        type: String,
        default: null
    },
    required: {
        type: Boolean,
        default: false
    },
    errMsgs: {
        type: Object
    },
    firstFocus: {
        type: Boolean,
        default: false
    },
    disabled: {
        type: Boolean,
        default: false
    },
    pattern: {
        type: RegExp
    }
});

// Biến lưu trữ giá trị input.
const inputValue = ref(props.value);

// Check empty input
const isEmpty = computed(() => {
    return props.required ? !inputValue.value || !inputValue.value.trim() : false;
});

// Check invalid input
const isInvalid = computed(() => {
    return props.pattern && !props.pattern.test(inputValue.value);
});

/**
 * Dựa vào props size và width được truyền vào,
 * style cho mỗi size và width của input tương ứng.
 */
const showErrorInput = ref(false);
const inputClass = computed(() => [
    `input-${props.size}`,
    `input-width-${props.width}`,
    { 'input-error': showErrorInput.value && (isEmpty.value || isInvalid.value) }
]);

// Dựa vào isEmpty hoặc isInvalid để tạo errorMessage
const errorMessage = computed(() => {
    if (isEmpty.value) {
        return props.errMsgs.isEmpty;
    } else if (isInvalid.value) {
        return props.errMsgs.isInvalid;
    } else {
        return '';
    }
});

/**
 * Khi thay đổi giá trị input,
 * hiển thị những input bị lỗi (viền đỏ) nếu input trống hoặc không hợp lệ.
 */
const handleChange = () => {
    showErrorInput.value = true;
};

/**
 * Khi isEmpty hoặc isInvalid thay đổi, cập nhật lại giá trị showErrorMessage tương ứng.
 */
const showErrorMessage = ref(false);
watch([isEmpty, isInvalid], () => (showErrorMessage.value = isEmpty.value || isInvalid.value));
</script>

<template>
    <div class="textfield">
        <div class="label-group">
            <label :for="id" :title="title">
                <slot name="label"></slot>
            </label>
            <span class="required-mark" v-if="required">&nbsp;*</span>
        </div>
        <div class="input-group">
            <input
                type="text"
                :id="id"
                :placeholder="placeholder"
                :disabled="disabled"
                :class="inputClass"
                v-model="inputValue"
                @change="handleChange"
            />
        </div>
        <span
            class="error-message"
            v-if="showErrorMessage"
            @mouseenter="showErrorMessage = true"
            @mouseleave="showErrorMessage = false"
        >
            {{ errorMessage }}
        </span>
    </div>
</template>

<style lang="scss" scoped>
@import '@/styles/mixins.scss';

$--label-color: rgb(var(--c-gray-900));
$--label-required-mark-color: rgb(var(--c-red-500));

$--input-medium-height: 36px;
$--input-medium-padding-y: 8px;

$--input-padding-x: 12px;
$--input-border-color: rgb(var(--c-gray-300));
$--input-placeholder-color: rgb(var(--c-gray-500));
$--input-hover-bg-color: rgb(var(--c-gray-100));
$--input-error-border-color: rgb(var(--c-red-500));

$--error-message-color: rgb(var(--c-white));
$--error-message-bg-color: rgb(var(--c-gray-900));

.input-medium {
    height: $--input-medium-height;
    padding: $--input-medium-padding-y $--input-padding-x;
}

.label-group {
    margin-bottom: 8px;
    label {
        @include font(14, 500);
        color: $--label-color;
    }
    .required-mark {
        color: $--label-required-mark-color;
    }
}

.input-group {
    input {
        border-radius: 4px;
        border: 1px solid $--input-border-color;
        @include font(14);

        &.input-error {
            border-color: $--input-error-border-color !important;
        }

        &::placeholder {
            @include font(14);
            color: $--input-placeholder-color;
        }

        &:not(.input-error):focus {
            border-color: rgb(var(--c-primary));
            outline: none;
        }
        &.input-error:focus {
            border-color: $--input-error-border-color !important;
            outline: none;
        }
    }
    &:not(.input-error):hover input {
        background-color: $--input-hover-bg-color;
    }
    &.input-error:hover input {
        border-color: $--input-error-border-color !important;
    }
}

/* Style error message */
.error-message {
    position: absolute;
    left: 20px;
    bottom: -8px;

    @include font(12);
    padding: 4px;
    border-radius: 2px;
    color: $--error-message-color;
    background-color: $--error-message-bg-color;
}

.input-width-medium {
    width: 180px;
}
</style>
