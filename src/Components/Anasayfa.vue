<template>
  <section name="TÜM SAYFA">
    <h2 class="Başlık">VİZE CANAVARI</h2>


    <section name="Oyuncu Elementleri">
      <p class="text-center">Oyuncu</p>
      <div class="text-center">
        <div class="HpBar text-center" style="display: inline-block" :style="`width: ${oyuncu.can*2}px;`">
          <span :style="`color: ${oyuncu.can<=0 ? 'black' : 'white'}`">{{ oyuncu.can }}</span>
        </div>
      </div>
    </section>
    <section name="Canavar Elementleri">
      <p class="text-center">Canavar</p>

      <div class="text-center">
        <div class="HpBar text-center" style="display: inline-block;" :style="`width: ${canavar.can*2}px;`">
          <span :style="`color: ${canavar.can<=0 ? 'black' : 'white'}`">{{ canavar.can }}</span>
        </div>
      </div>

    </section>


    <section name="Aksiyonlar">
      <div style="margin-top:50px" class="text-center">
        <button @click="Atak(5,20)" class="d-block" style="width: 100%">Atak</button>
        <button @click="ÖzelAtakYap" :disabled="oyuncu.özelAtakHakkı>0?false:true" style="width: 50%">Özel Atak</button>
        <!--      <button v-if="oyuncu.özelAtakHakkı>0?true:false" @click="ÖzelAtakYap" :disabled="oyuncu.özelAtakHakkı>0?false:true" style="width: 50%">Özel Atak</button>-->
        <button @click="CanBas" style="width: 50%">Can Bas</button>
      </div>
    </section>
    <section name="Loglar" style="margin-top: 20px">

      <div v-for="log in loglar" style="margin-top: 10px">
        <div
            style="background-color: rgba(0,0,255,0.4); padding-left: 5px; padding: 5px; border-radius: 5px; color: white">
          <p style="font-size: 10px;font-style: italic;">Round {{ log.round + 1 }}</p>
          <p v-for="aksiyon in log.aksiyonlar">{{ aksiyon }}</p>
        </div>
      </div>

    </section>


    <section name="DialogBoxlar">

      <DialogBox @close="dialogBox=!dialogBox" v-if="dialogBox" :icerik="oyunSonuMesajı"></DialogBox>

    </section>

  </section>

</template>

<script setup>

import {ref} from "vue";
import DialogBox from "@/Components/DialogBox.vue";

var dialogBox = ref(false);

var oyunSonuMesajı = ref({
  mesaj: "",
  baslik: "Oyun sonu mesajı",
})

var oyuncu = ref({
  can: 100,
  saldırıGücü: 1,
  özelAtakHakkı: 1,
})

var canavar = ref({
  can: 100,
  saldırıGücü: 1,
})

var roundSayısı = 0;

var loglar = ref([]);

// {
//   round: 1,
//       aksiyonlar:[
//   "OYUNCU CANAVAR 10 VURDU",
//   "CANAVAR OYUNCUYA 16 VURDU",
// ],
// }


function Atak(min, max) {
  var rastgeleSayı = RastgeleSayıUret(min, max)
  canavar.value.can = canavar.value.can - rastgeleSayı;
  if (canavar.value.can < 0) {
    canavar.value.can = 0;
  }

  //LOG
  LogOluştur(`OYUNCU CANAVARA ${rastgeleSayı} vurdu`)

  CanavarAtak();
}

function CanavarAtak() {
  var rastgeleSayı = RastgeleSayıUret(5, 20)
  oyuncu.value.can = oyuncu.value.can - rastgeleSayı;
  if (oyuncu.value.can < 0) {
    oyuncu.value.can = 0;
  }


  LogOluştur(`CANAVAR OYUNCUYA ${rastgeleSayı} vurdu`)

  roundSayısı++;

  // KAZANAN KONTROLÜ
  KazananKontrolü();
}

function ÖzelAtakYap() {
  if (oyuncu.value.özelAtakHakkı <= 0)
    return;

  Atak(10, 20);

  oyuncu.value.özelAtakHakkı--;
}

function CanBas() {
  var rastgeleSayı = RastgeleSayıUret(5, 20)
  oyuncu.value.can = oyuncu.value.can + rastgeleSayı;
  if (oyuncu.value.can > 100) {
    oyuncu.value.can = 100;
  }


  LogOluştur(`OYUNCU KENDİNE ${rastgeleSayı} CAN VERDİ`)
  CanavarAtak();

}

function KazananKontrolü() {

  var oyunBitti = false;

  if (oyuncu.value.can <= 0) {
    // Canavar kazandı
    //  alert("CANAVAR KAZANDI")

    setTimeout(() => {
      oyunSonuMesajı.value.mesaj = `Toplam Round Sayısı: ${roundSayısı}\nCanavar Kazandı`
      dialogBox.value = true;
    }, 50);

    oyunBitti = true;
  } else if (canavar.value.can <= 0) {
    // Oyuncu Kazandı
    //  alert("OYUNCU KAZANDI")
    setTimeout(() => {
      oyunSonuMesajı.value.mesaj = `Toplam Round Sayısı: ${roundSayısı}\nOyuncu Kazandı`
      dialogBox.value = true;
    }, 50);
    oyunBitti = true;
  }

  if (oyunBitti) {
    oyuncu.value.can = 100
    oyuncu.value.özelAtakHakkı = 1;
    canavar.value.can = 100
    roundSayısı = 0;
    loglar.value = [];
  }

}

function RastgeleSayıUret(min, max) {
  return Math.floor((Math.random() * (max - min)) + min)
}

function LogOluştur(mesaj) {

  // EĞER LOGLARIN İÇİNDE
  // ŞUANKİ ROUNDA AİT BİR LOG VAR MI

  var öncekiLog = loglar.value.find(x => x.round == roundSayısı);
  if (öncekiLog != undefined) {
    // DAHA ÖNCEDEN BU ROUNDA AİT LOG OLUŞTURULMUŞTUR
    // DİREK AKSİYONLAR KISMINA ATABİLİRİZ.
    öncekiLog.aksiyonlar.push(mesaj);
  } else {
    // BU ROUNDA AİT BİR LOG YOK
    // YENİ LOG OLUŞTURALIM
    var yeniLog = {
      round: roundSayısı,
      aksiyonlar: [mesaj]
    }
    loglar.value.push(yeniLog);
  }

}

</script>


<style scoped>

.text-center {
  text-align: center;
}

.d-block {
  display: block;
}

.Başlık {
  text-align: center;
  background-color: blueviolet;
  color: white;
  margin: 0px;
  border-radius: 10px;
}

.HpBar {
  background-color: green;
  height: 20px;
  border-radius: 20px;
}




</style>