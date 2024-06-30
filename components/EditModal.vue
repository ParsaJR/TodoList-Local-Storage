<!-- a Modal that would to get new title for Todos -->
<script lang="ts" setup>
let newValue = ref('');

const emit = defineEmits(['success'])
function onSuccess() {
    emit('success',newValue.value)
}
</script>

<template>
    <UModal>
        <UCard>
            <div class="space-y-2">
                <p>Enter new value !</p>
                <UInput v-model="newValue"></UInput>
                <UButton @click="onSuccess">
                    Submit
                </UButton>
            </div>
        </UCard>
    </UModal>
</template>