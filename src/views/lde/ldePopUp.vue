<template>
  <PopUp 
    :open="props.open"
    @close="close"
    :title="props.title" 
    :useButton="['Save','Close']"
    @click="testClick"
  >
  <slot>

  </slot>
  </PopUp>
</template>


<script setup>
import PopUp from '@/components/popup/commonPopUp.vue'
import { ref, watch, computed } from 'vue'


const formData = ref({})
const props = defineProps({
  open: Boolean,
  title: String,
  rowData: Object,
  formData: Object,
});

const open = ref(false)

const emit = defineEmits(['save', 'close'])

const save = () => {
  emit('save', { ...props.formData })
  open.value = false 
}

const close = () => {
  open.value = false
  emit('close')
}

const testClick = (e) => {
    console.log(e,'e')
    if(e.id === 'Close'){
        close()
    }

    if(e.id === 'Save') {
        save()
    }
}

watch(() => props.open, (newVal) => {
    open.value = newVal
    if (newVal) {
      if (props.rowData) {
        formData.value = { ...props.rowData }
        
      } else {
        formData.value = {}
        
      }
    }
})
</script>

<style scoped>

</style>