<template>
  <sl-alert
    v-if="!state.globalRegistration"
    open
    variant="danger"
  >
    In Order to use this Bone register the bone component globally in your main file
  </sl-alert>
  <div
    v-else
    class="form"
  >
    <vi-form ref="skelRef"
             :internal="mainformState"
             :skel="value"
             :structure="bone['using']"
             :renderer="state.renderer"
             :collapsedCategories="bone?.params?.['collapsedCategories'] || []"
             @change="changeEvent"
    >

    </vi-form>
  </div>
</template>

<script setup>

import { reactive, defineComponent, onMounted, inject, computed, getCurrentInstance, watch, ref } from "vue"
import { getBoneWidget } from "./index"
import ViForm from "../../forms/ViForm.vue";

const emit = defineEmits(["change"])
const props = defineProps({
  name: String,
  value: null,
  index: Number,
  lang: String,
  readonly: Boolean,
  params: Object,
  bone:Object

})
const skelRef = ref(null)
const boneState = inject("boneState")
const mainformState = inject("mainformState")
const state = reactive({
  renderer: import.meta.env.VITE_DEFAULT_RENDERER || 'json',
  globalRegistration: false,
})

function changeEvent(val) {
  val["value"] = skelRef.value.state.skel // send whole form
  val["name"] = props.name
  val["index"] = props.index
  val["lang"] = props.lang
  emit("change", val)
}

onMounted(() => {
  let app = getCurrentInstance().appContext
  if (app.components.Bone) {
    state.globalRegistration = true
  } else {
    state.globalRegistration = false
  }

  let val = {}
  if (props.value) val = props.value

  for(const [bonename, boneStructure] of Object.entries(props.bone['using'])){
    if(!Object.keys(val).includes(bonename) && boneStructure['defaultvalue']){
      val[bonename] = boneStructure['defaultvalue']
    }
  }

  emit("change", props.name, val, props.lang, props.index) //init
})

function updateValue(e) {
  console.log(e)
}

</script>

<style scoped>
.form {
  width: 100%;
}
</style>
