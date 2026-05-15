<template>
  <section name="DialogBox">
    <div @click="$emit('close')" class="overlay">
    </div>


    <div id="Mesaj" class="dialogBox">
      <h4 style="padding: 3px; margin: 0px">{{ icerik.baslik }}</h4>

      <div style="padding: 5px">
        <p>{{ icerik.mesaj }}</p>
      </div>

      <div style="display: flex; justify-content: end; gap: 3px">
        <button @click="$emit('close')">OKEY</button>
        <button @click="$emit('close')">Cancel</button>
      </div>

    </div>

  </section>


</template>

<script setup>

import {ref} from "vue";

defineEmits(["close"]);

defineProps({
  icerik: {
    type: Object
  }
})

var dialogBox = ref(true);

</script>


<style scoped>

.dialogBox {
  background-color: white;
  border-radius: 7px;


  width: 200px;
  position: absolute;
  top: 20px;
  left: calc(50% - 100px);
  padding: 5px;
  box-shadow: black 2px 2px 10px;
}

.overlay {
  top: 0px;
  left: 0px;
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
}

button {
  color: orange !important;
}

</style>