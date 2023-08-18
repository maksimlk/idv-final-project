<script>
    import { onMount, onDestroy } from 'svelte'
    import { Map, NavigationControl, Marker } from 'maplibre-gl';
    import 'maplibre-gl/dist/maplibre-gl.css';
    import { UniqueActivities } from './uniqueActivities.js'
    import MapboxChoropleth from 'mapbox-choropleth';

    let map;
    let mapContainer;
  
    onMount(() => {
      const initialState = { lng: 0, lat: 0, zoom: 1 };
      map = new Map({
        container: mapContainer,
        style: `https://basemaps.cartocdn.com/gl/positron-gl-style/style.json`,
        center: [initialState.lng, initialState.lat],
        zoom: initialState.zoom
      });
      map.addControl(new NavigationControl(), 'top-right');
      map.on('load', function() {
        map.addSource('countries', {
          'type': 'geojson',
          'data': './countries.geojson',
        })
      });

      let c = new MapboxChoropleth({
          tableUrl: './activities_data/all_countries_social.csv',
          tableNumericField: 'hoursPerDayCombined',
          tableIdField: 'countryISO3',
          geometryUrl: './countries.geojson',
          geometryIdField: 'iso_a3_eh',
          paint: { 'fill-opacity': 0.3 },
          legendElement: '#legend',
          colorScheme: ['#ffffe5', '#004529'],
      }).addTo(map);
    });
    onDestroy(() => {
          map.remove();
      });
</script>
  
<div class="wrap">
    <div class="map" id="map" bind:this={mapContainer}></div>
    <div id="legend"></div>
</div>
  
<style>
  
  .wrap {
    position: relative;
    width: 100%;
    height: 60vh;
  }
  
  .map {
    position: absolute;
    width: 100%;
    height: 100%;
  }
  #legend {
    position: absolute;
    transform: scale(0.7, 0.7);
    top: 0.5em;
    left: 0.5em;
    color: black;
    font-weight: 700;
  }
</style>
  