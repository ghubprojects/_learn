<script setup>
import VTextField from './components/VTextField.vue';
</script>

<template>
    <VTextField
        class="m"
        size="medium"
        width="medium"
        id="employee-code-textfield"
        required
        :errMsgs="{ isEmpty: 'Mã không được để trống.' }"
        firstFocus
    >
        <template #label>Mã</template>
    </VTextField>
</template>

<style lang="scss" scoped>
@import '@/styles/global.scss';

.m {
    margin: 20px;
}
</style>
