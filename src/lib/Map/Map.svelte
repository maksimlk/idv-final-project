<script>
    import { onMount, onDestroy } from 'svelte'
    import { Map, MouseRotateWrapper, NavigationControl, Popup } from 'maplibre-gl';
    import 'maplibre-gl/dist/maplibre-gl.css';
    import { UniqueActivities } from './uniqueActivities.js'
    import MapboxChoropleth from 'mapbox-choropleth';
    import {csv} from 'd3-fetch';

    let map;
    let mapContainer;
    let datasetKey = 'Social';
    const datasetsNames = Object.values(UniqueActivities);
    const datasetsKeys = Object.keys(UniqueActivities);
    let loaded = false;
    let hoveredStateName = null;

    let data = null;

    let dataset = csv(`./activities_data/${datasetKey}.csv`).then((dat) => {
      data = dat;
    });

    const positions = {
      'baker': {
            bearing: 27,
            center: [-0.15591514, 51.51830379],
            zoom: 15.5,
            pitch: 20
        },
        'aldgate': {
            duration: 6000,
            center: [-0.07571203, 51.51424049],
            bearing: 150,
            zoom: 15,
            pitch: 0
        },
    }
    let activePositionName = 'baker';
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

      map.on('click', 'choropleth', (e) => {
        let hrPerDay = data.filter((row) => row.countryISO3 == e.features[0].properties.iso_a3_eh)[0]?.hoursPerDayCombined;
        if(hrPerDay)
          new Popup()
            .setLngLat(e.lngLat)
            .setHTML(e.features[0].properties.name_long + "<br>" 
            + "GDP: $" + e.features[0].properties.gdp_md.toLocaleString('en-US') + "M" + "<br>" 
            + UniqueActivities[datasetKey] + ": " + data.filter((row) => row.countryISO3 == e.features[0].properties.iso_a3_eh)[0]?.hoursPerDayCombined + " hr/day on average per person")
            .addTo(map);
        else
          new Popup()
            .setLngLat(e.lngLat)
            .setHTML(e.features[0].properties.name_long + "<br>" 
            + "GDP: $" + e.features[0].properties.gdp_md.toLocaleString('en-US') + "M" + "<br>" 
            + UniqueActivities[datasetKey] + ": No data for this country")
            .addTo(map);
        });

      map.on('mouseenter', 'choropleth', () => {
            map.getCanvas().style.cursor = 'pointer';
        });

        // Change it back to a pointer when it leaves.
        map.on('mouseleave', 'choropleth', () => {
            map.getCanvas().style.cursor = '';
        });
      loaded = true;

    });
    onDestroy(() => {
          map.remove();
      });

      $: {
        if(loaded){
          dataset = csv(`./activities_data/${datasetKey}.csv`).then((dat) => {
            data = dat;
          });
          let choroplethMap = new MapboxChoropleth({
              tableUrl: `./activities_data/${datasetKey}.csv`,
              tableNumericField: 'hoursPerDayCombined',
              tableIdField: 'countryISO3',
              geometryUrl: './countries.geojson',
              geometryIdField: 'iso_a3_eh',
              paint: {'fill-opacity': 0.5},
              legendElement: '#legend',
              colorScheme: ['#ffffe5', '#004529'],
              numberFormatFunc: x => x.toFixed(2),
          }).addTo(map);
        }
      }
</script>
  
<div class="wrap">
    <div>
      <label for="column-select">Select a dataset:</label>
        <select bind:value={datasetKey} id="column-select">
        {#each datasetsNames as ds, i}
          <option value={datasetsKeys[i]}>
            {ds}
          </option>
        {/each}
      </select>
    </div>
    <div class="map" id="map" bind:this={mapContainer}/>
    <div id="legend-wrap">
      <div id="legend-title"> Hours per day <br> spent on the activity</div>
      <div id="legend"/>
    </div>
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
  #legend-wrap {
    position: absolute;
    transform: scale(0.7, 0.7);
    top: 0.05em;
    left: 0.05em;
    color: black;
    font-weight: 700;
  }
  #legend-title{
    font-size: 0.8em;
  }
</style>
  