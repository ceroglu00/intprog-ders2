<template>
  <section name="DialogBox">
    <div v-if="dialogBox" class="overlay">
      <div id="Mesaj" class="dialogBox">
        <h4 style="padding: 3px; margin: 0px">{{ icerik.baslik }}</h4>

        <div style="padding: 5px">
          <p>{{ icerik.mesaj }}</p>
        </div>

        <div style="display: flex; justify-content: end; gap: 3px">
          <button @click="dialogBox=!dialogBox">Ok</button>
          <button @click="dialogBox=!dialogBox">Cancel</button>
        </div>

      </div>
    </div>
  </section>


</template>

<script setup>

import {ref} from "vue";

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
</style>