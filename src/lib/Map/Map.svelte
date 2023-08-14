<script>
    import { onMount, onDestroy } from 'svelte'
    import { Map, NavigationControl, Marker } from 'maplibre-gl';
    import 'maplibre-gl/dist/maplibre-gl.css';
  
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
          'data': './countries.json',
        })
        map.addLayer({
          'id': 'polygons',
          'type': 'fill',
          'source': 'countries',
          'paint': {
            'fill-color': '#FFAA01',
            'fill-opacity': 0.5,
          }
        })
      });
    });
    onDestroy(() => {
          map.remove();
      });
</script>
  
<div class="wrap">
    <div class="map" id="map" bind:this={mapContainer}></div>
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
</style>
  