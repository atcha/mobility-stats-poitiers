<template>
    <div class="container">
        <div class="stats-container panel">
            <h2>Les stationnements vélo sur Poitiers : </h2>
            <div class="map-container">
                <div id="mapCyclePark" style="height: 400px;"></div>
            </div>
            <div class="stats-table">
                <table>
                    <thead>
                        <tr>
                            <th>Nombre de parking</th>
                            <th>Nombre total de place</th>
                            <th>Nombre moyen de place par parking</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td>{{ tableCyclePark.nbParking }}</td>
                            <td>{{ tableCyclePark.nbTotalPlace }}</td>
                            <td>{{ tableCyclePark.nbMoyPlace }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <div class="stats-container panel">
            <h2>Les stationnements vélo sur Poitiers : </h2>
            <div class="map-container">
                test
            </div>
            <div class="stats-table">
                <table>
                    <thead>
                    <tr>
                        <th>Nombre de parking</th>
                        <th>Nombre total de place</th>
                        <th>Nombre moyen de place par parking</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr>
                        <td>{{ tableCyclePark.nbParking }}</td>
                        <td>{{ tableCyclePark.nbTotalPlace }}</td>
                        <td>{{ tableCyclePark.nbMoyPlace }}</td>
                    </tr>
                    </tbody>
                </table>
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
                tableCyclePark: {
                    nbParking: 0,
                    nbTotalPlace: 0,
                    nbMoyPlace: 0
                },
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
                        this.tableCyclePark.nbParking = resultCyclePark.data.nhits
                        this.cycleParkDatas = resultCyclePark
                        this.cycleParkDatas.data.records.map((record) => {
                            let localisation = record.fields.localisation
                            let typeStationnement = record.fields.type_stationnement
                            let nbPlaces = record.fields.places
                            let html = ''
                            this.tableCyclePark.nbTotalPlace += record.fields.places
                            if(localisation) html += '<p>Lieu : ' + localisation + '</p>'
                            if(typeStationnement) html += '<p> Type stationnement : ' + typeStationnement + '</p>'
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
                        this.tableCyclePark.nbMoyPlace = Math.round(this.tableCyclePark.nbTotalPlace / this.tableCyclePark.nbParking)
                    })
            }
        }
    }
</script>

<style>
    .container {
        display: flex;
        flex-direction: column;
        padding: 0 15px;
    }
    .stats-container {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        align-content: flex-start;
        flex-wrap: wrap;
        margin-bottom: 15px;
    }
    .stats-container:last-child { margin: 0; }
    .panel {
        background-color: #FFF;
        border: 1px solid rgba(0,0,0,.12);
        padding: 0 15px;
        min-height: 500px;
    }
    .stats-container h2 {
        width: 100%;
        text-align: left;
    }
    .map-container {
        margin-right: 15px;
        position: relative;
        flex: 1;
    }
    .stats-table {
        flex: 1;
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

    table {
        color: rgba(0,0,0,.87);
        border-collapse: collapse;
        border-spacing: 0;
        max-width: 100%;
        width: 100%;
        border: 1px solid #e1e6ef;
        border-bottom: 0;
    }
    table thead, table td {
        border-color: rgba(0,0,0,.12);
        border-width: 0 0 1px;
        border-style: solid;
    }
    table thead tr {
        height: 56px;
    }
    table th, table td { padding: 7px 14px; }
    table th {
        color: rgba(0,0,0,.54);
        font-size: 12px;
        font-weight: 500;
    }
    table td {
        font-size: 13px;
        font-weight: 400;
        height: 48px;
    }
</style>
