<template>
  <div class="q-pa-md">
      <div v-for="item in data" :key="item.id">
        <q-card class="my-card" flat bordered>
          <q-list>
            <q-item >
              <q-item-section avatar>
                <q-icon color="grey" name="settings_remote" />
              </q-item-section>

              <q-item-section>
                <q-item-label>{{item.label? item.label : item.imei}}</q-item-label>
              </q-item-section>
            </q-item>
            <InfoDevice :imei="item.imei" />
          </q-list>
        </q-card>
      </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios';
import InfoDevice from 'src/components/InfoDevice.vue';


export default defineComponent({
    name: 'IndexPage',
    data() {
        return {
            url: 'https://api.boxdev.site',
            data: [],
        };
    },
    methods: {
        async fetchData() {
            const response = await axios.get(this.url + "/api/arduino/");
            this.data = response.data;
            console.log(response.data);
        },
    },
    mounted() {
        this.fetchData();
    },
    components: { InfoDevice }
})
</script>
