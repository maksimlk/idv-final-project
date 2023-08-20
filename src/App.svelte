<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Scrolly from './lib/Scrolly.svelte'
  import Map from './lib/Map/Map.svelte'
  import { tweened } from 'svelte/motion'

  let currentStep;
  let map = null;
  const steps = [
		 "<p> This map allows users to take a look at the data showing the amount of time spent in each country for different activities. Now the selected dataset is 'Social' and it shows how many hours per day average citizen of a country spends on socializing, attending or hosting social events, telephone calls, discussing, gossiping, family and socializing, visiting relatives and friends.</p>",
    "<p> It allows to easily notice <b>similarities and differences</b> across the regions of our planet. For example, we can see that Africa region is very social all across the region.</p>",
    "<p> By the way, you can click on each country to see detailed information about it! <b>(Try it)</b></p>",
    "<p> While Europe region is surpirsingly not that social. This data can and should be used to better understand the socioeconomic differences between countries and regions, reasons behind this differences, and what impact do this differences have on a day to day life. Some of the factors that could be considered: GDP, level of education, unemployment, and many others </p>",
    "<p> Now you are free to explore datasets for all the activities on your own! Name of a dataset represents type of activity according to <a href='https://www.pnas.org/doi/full/10.1073/pnas.2219564120#t02'> the Motivating-Outcome-Oriented General Activity Lexicon (MOOGAL) </a> </p>",
  ];

  const mapPositions = {
      0: {
            bearing: 0,
            center: [1, 1],
            zoom: 1,
            pitch: 0,
        },
        1: {
            duration: 4000,
            center: [0, 27],
            bearing: 90,
            zoom: 2.5,
            pitch: 0,
        },
        2: {
            duration: 4000,
            center: [0, 27],
            bearing: 90,
            zoom: 2.5,
            pitch: 0,
        },
        3: {
            duration: 4000,
            center: [15, 50],
            bearing: 0,
            zoom: 2.5,
            pitch: 0,
        },
        4: {
          duration: 4000,
          bearing: 0,
          center: [1, 1],
          zoom: 1,
          pitch: 0,
        },
    };
    $: {
      if(map != null && currentStep != null && currentStep >= 0 && currentStep <= 4){
        map.cameraTo(mapPositions[Math.round(currentStep)]);
      }
      if(currentStep == 4){
        let sel = document.getElementsByClassName('dataset-selector')[0];
        if(sel != null){
          sel.setAttribute('style', "visibility: visible")
        }
      }
    }
</script>

<!-- https://www.connorrothschild.com/post/svelte-scrollytelling - taken from here -->
<main>
	<div class='hero'>
		<h1> 
			How do we spend our time?
      <br> How does it differ across the globe?
      <br> How does it connect to the socioeconomic factors?
		</h1>
		<h2>
      Based on the <a href="https://zenodo.org/record/8040631">data</a> from 
      <a href="https://www.pnas.org/doi/full/10.1073/pnas.2219564120"> the global human day article.</a>
		</h2>
	</div>
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value={currentStep}>
        {#each steps as text, i}
          <div class="step" class:active={currentStep === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="sticky">
        <Map bind:this={map}/>
    </div>
  </div>
	<div class='hero'>
		<h1> 
			Thank you for your attention!
		</h1>
    <div style="text-align:left">
    <br> <b>Short description of the project: </b>
    <br> As an international group we became interested in the differences and similarities of leisure and work activities across our societies and how they shape different countries.
    We took the data available online that shows the amount of time the “average” citizen of each country spends on certain activities.
    We used the scrollytelling technique to improve the comprehension and interactiveness of our project. 
    <br> <br> The goal of this visualization is to show how people spend their time across the globe. It is done by creating a map with a choropleth layer, where each country is colored according to the amount of time spent on a particular activity.
    This type of visualization is highly interactive, which allows and engages users to explore the data on their own. Users can select different datasets, which represent different activities, and see how the map changes.
    Moreover, it is possible to use the popup over each country to see additional information (i.e., GDP, and it is possible to add many others) about the country.
    <br> <br> Actually, the map is one of the most effective visual devices to show this kind of data. Contrary to the basic table, it allows users to see the position of the country on the map and easily see the information about countries' neighbours.
     This gives many new possibilities to use this data. For example, it is possible to see that countries with similar level of development are similar in terms of the amount of time spent on some activities. This can be used to better
    understand the socioeconomic differences between countries and regions, reasons behind this differences, and what impact do this differences have on a day to day life.
    <br> <br>As per visual devices, this map uses countries' geometries and choropleth layer with sequentialy segmented colormap to indicate the amount of time spent on a particular activity. This is a good choice, because it is easy to see the difference between countries. 
    Also, users can click on each country to see the precise amount of time spent on each activity (as data is divided into bins and as saturation channel has 1.7 perceived sensation, so using only saturation layer is not very accurate) and some additional information. This allows to provide the users with more data while keeping the visualization clean and intuitive.
    There is also a dropbox selector to allow users to choose the dataset that they want to explore. This allows to use the same map with the same visual devices to explore different datasets.
    </div>
  </div>
</main>

<style>
:global(body) {
		overflow-x: hidden;
	}
	
	.hero {
		height: 85vh;
		display: flex;
		place-items: center;
		flex-direction: column;
		justify-content: center;
		text-align: center;
	}
	
	.hero h2 {
		margin-top: 0;
		font-weight: 200;
	}
  .spacer {
    height: 40vh;
  }

  .sticky {
    position: sticky;
    top: 10%;
		flex: 1 1 60%;
    width: 60%;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
  }

  .step {
    height: 80vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 1rem;
    background: whitesmoke;
    color: #ccc;
    border-radius: 5px;
    padding: .5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    box-shadow: 1px 1px 10px rgba(0, 0, 0, .2);
    text-align: left;
		width: 75%;
		margin: auto;
		max-width: 500px;
  }

  .step.active .step-content {
		background: white;
		color: black;
	}
	
  .steps-container,
  .sticky {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }
  /* Comment out the following line to always make it 'text-on-top' */
  @media screen and (max-width: 768px) {
    .section-container {
      flex-direction: column-reverse;
    }
    .sticky {
      width: 95%;
			margin: auto;
    }
  }
</style>
