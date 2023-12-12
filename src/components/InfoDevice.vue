<template>
    <q-card class="my-card" flat>
        <q-list>
            <q-item>
                <q-item-section>
                    <q-item-label>Temperatura</q-item-label>
                    <q-item-label caption>{{ data.temperature }}</q-item-label>
                </q-item-section>
                <q-item-section>
                    <q-item-label>Humidade</q-item-label>
                    <q-item-label caption>{{ data.humidity }}%</q-item-label>
                </q-item-section>
                <q-item-section>
                    <q-item-label>Soil</q-item-label>
                    <q-item-label caption>{{ data.soil_value }}</q-item-label>
                </q-item-section>
                <q-item-section>
                    <q-item-label>Quatidade Água</q-item-label>
                    <q-item-label caption>{{ data.soil_percentage }}</q-item-label>
                </q-item-section>
                <q-item-section>
                    <q-item-label>Activar Água</q-item-label>
                    <q-btn :disabled="is_water" color="primary" @click="turn_water_on" label="Activar" />
                    <q-btn :disabled="!is_water" color="orange" @click="turn_water_off" label="Desactivar" />
                </q-item-section>
            </q-item>
        </q-list>
    </q-card>
</template>
  
<script>
import axios from 'axios';

export default {
    props: {
        imei: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            url: 'https://api.boxdev.site',
            data: [],
            is_water: false,
        };
    },
    mounted() {
        this.fetchData();

        // Fetch data every second
        this.intervalId = setInterval(() => {
            this.fetchData();
        }, 2500);
    },
    beforeDestroy() {
        // Clear the interval when the component is destroyed
        clearInterval(this.intervalId);
    },
    watch: {
        // Watch for changes to the 'id' prop and fetch data when it changes
        imei() {
            this.fetchData();
        },
    },
    methods: {
        turn_water_on() {
            const payload = { "imei": this.imei };
            axios.post(this.url + "/api/arduino/water/on/", payload)
                .then(response => {
                    this.is_water = true
                })
                .catch(error => {
                    console.error('Error fetching data:', error);
                });
        },
        turn_water_off() {
            const payload = { "imei": this.imei };
            axios.post(this.url + "/api/arduino/water/off/", payload)
                .then(response => {
                    this.is_water = false
                })
                .catch(error => {
                    console.error('Error fetching data:', error);
                });
        },

        fetchData() {

            // Use this.id to include the id in the payload
            const payload = { "imei": this.imei };
            console.log("Payload" + this.imei)
            // Make a POST request with the payload
            axios.post(this.url + "/api/arduino/log/recent/", payload)
                .then(response => {
                    console.log("Recent" + response.data)
                    this.data = response.data[0];
                })
                .catch(error => {
                    console.error('Error fetching data:', error);
                });

            axios.post(this.url + "/api/arduino/arduino/water", payload)
                .then(response => {
                    console.log("Recent" + response.data)
                    this.is_water = response.data[0];
                })
                .catch(error => {
                    console.error('Error fetching data:', error);
                });
        },
    },
};
</script>