<template>
    <div class="container">
        <div style="width: 100%;">
            <h2>Les parkings à vélo sur Poitiers : </h2>
            <div class="map-container">
                <div id="mapCyclePark" style="height: 400px;"></div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'
    import mapboxgl from 'mapbox-gl'
    import { publicKey } from '../private/mapBoxKey'


    export default {
        name: 'MobilityGraph',
        data: function() {
            return {
                cycleParkDatas: {},
                mapCyclePark: null,
                nbCyclePark: 0
            }
        },
        props: {
        },
        mounted: function() {
            mapboxgl.accessToken = publicKey;
            this.mapCyclePark = new mapboxgl.Map({
                container: 'mapCyclePark',
                center: [0.340375, 46.580224],
                zoom: 14,
                style: 'mapbox://styles/mapbox/streets-v9'
            })
            this.mapCyclePark.addControl(new mapboxgl.NavigationControl())
            this.mapCyclePark.resize()
            // get the cycle park from poitiers
            this.getCyclePark()

        },
        methods: {
            getCyclePark() {
                axios.get('https://data.grandpoitiers.fr/api/records/1.0/search/?dataset=mobilite-stationnement-velos-grand-poitiers-donnees-metiers&facet=etat&rows=1000')
                    .then((resultCyclePark) => {
                        console.log(resultCyclePark)
                        this.cycleParkDatas = resultCyclePark
                        this.cycleParkDatas.data.records.map((record) => {
                            console.log(record.fields.localisation)
                            let localisation = record.fields.localisation
                            let typeStationnement = record.fields.type_stationnement
                            let nbPlaces = record.fields.places
                            let html = ''
                            if(localisation) html += '<p>Lieu : ' + localisation + '</p>'
                            if(typeStationnement) html += '<p> Type stationnemet : ' + typeStationnement + '</p>'
                            if(nbPlaces) html += '<p> Nb places : ' + nbPlaces + '</p>'
                            let popup = new mapboxgl.Popup()
                                .setLngLat(record.geometry.coordinates)
                                .setHTML(html)
                                .addTo(this.mapCyclePark)

                            let marker = new mapboxgl.Marker()
                                .setLngLat(record.geometry.coordinates)
                                .addTo(this.mapCyclePark)
                                .setPopup(popup)


                        })
                    })
            }
        }
    }
</script>

<style>
    .container {
        display: flex;
        padding: 0 15px;
    }
    .map-container {
        position: relative;
    }
    #mapCyclePark {
        position:absolute;
        top:0;
        bottom:0;
        width:100%;
    }
    #mapCyclePark .mapboxgl-canvas {
        left:0;
    }
</style>
