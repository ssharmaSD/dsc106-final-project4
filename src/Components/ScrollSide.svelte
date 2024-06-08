<script>
  import Scrolly from "./Scrolly.svelte";
  import { select } from "d3-selection";

  // Scroll iterator
  let value;

  // Paragraph text for scrolly
  $: steps = [
    `<h1 class='step-title'>The Early Beginnings</h1>
       <br><br>
      <p>
        While no one knows the exact beginnings of alcohol,
        the best evidence of alcohol’s origin comes from China,
        where the first makings of the beverage can be traced
        back to 7,000 BCE. With China as the known birthplace
        of alcohol, we wanted to kick off our story by seeing
        how modern day drinking habits fare up to documented
        history. Out of the 193 countries we have data on, China
        currently ranks 75th in average consumption of all alcoholic
        beverages, despite being its known birthplace.
      </p>`,
    `<h1 class='step-title'>Popularity in Ancient Civilizations</h1>
      <h3>Ancient Mesopotamia</h3>
      <p>
         Over the course of the next few thousand years,
         it was discovered that other societies were developing
          their own versions of alcohol.
          Ancient Mesopotamians and Egyptians were known for
          creating what we now know as beer. They cultivated
          this drink from harvesting cereal grain. The area
          known as Ancient Mesopotamia is now known as Iraq
          and Syria. From this ancient history, how do the beer
          drinking habits of Iraq, Syria, and Egypt compare now?

          Through exploring the dataset, it appears that despite being
          the cultivators of beer, the modern day countries are among
          the bottom ranked when it comes to average yearly beer
          consumption. Iraq is 160th, Egypt is 165th, and Syria is
          169th when it comes to beer consumption out of 193 countries.
      </p>`,

    `<h1 class='step-title'>Popularity in Ancient Civilizations</h1>
      <h3>Ancient Greece</h3>
      <p>
         Wine was a much more common commodity in Ancient Greece due
         to the more suitable climate for grape cultivation. This has
         prompted the nation of Greece to be the 13th highest country
         when it comes to average yearly wine consumption in the world,
         being beaten out by fellow European countries such as France,
         Andorra, and Italy. The pattern of creating fermented drinks
         out of harvested fruits in the environment became more common
         across the world.

         Some examples include the people of South America making Chicha,
         East Africans making beer out of bananas and palm, and
         people making sake out of rice in modern-day Japan. The
         creation of spirits would also become a hot commodity,
         eventually becoming a staple in international trade.
         Due to the presence of spirit trade on an international level,
         we thought it would be of interest to understand spirit consumption
         across all continents. The continent coming out on top with spirit
         consumption is Europe at an average of 5,286 spirits. Europe is
         followed by Asia, then North America, South America, Africa,
         then Oceania.
      </p>`,
  ];

  const target2event = {
    0: () => {
      select("#map1").style("display", "block");
      select("#map2").style("display", "none");
      select("#map3").style("display", "none");
    },
    1: () => {
      select("#map1").style("display", "none");
      select("#map2").style("display", "block");
      select("#map3").style("display", "none");
    },
    2: () => {
      select("#map1").style("display", "none");
      select("#map2").style("display", "none");
      select("#map3").style("display", "block");
    },
  };

  $: if (typeof value !== "undefined") target2event[value]();
</script>

<h2 class="body-header">Exploration Through the Centuries</h2>
<section>
  <!-- scroll container -->
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each steps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="maps-container">
      <div id="map1" class="map">
        <iframe
          width="100%"
          height="100%"
          frameborder="0"
          style="border:0"
          src="https://api.mapbox.com/styles/v1/mapbox/streets-v11.html?title=true&access_token=pk.eyJ1IjoiYW50YXJhc2VuZ3VwdGEiLCJhIjoiY2x2eXdxam9tMnlsdjJpcGdnZDhxNG1hOCJ9.qiKSj4W-8tFjUvOAbCFBdA#5.00/35.0000/104.0000"
          allowfullscreen>
        </iframe>
        <svg class="hover-svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 500">
          <g transform="translate(0 5)">
            <g>
              <polygon points="70,180 130,180 140,80 60,80" fill="#d19d0f" stroke="black" stroke-width="5" />
              <polygon points="55,50 145,50 140,80 60,80" fill="white" stroke="black" stroke-width="5" />
            </g>
          </g>
          <title>Information about China</title>
        </svg>
      </div>
      <div id="map2" class="map" style="display:none;">
        <iframe
          width="100%"
          height="100%"
          frameborder="0"
          style="border:0"
          src="https://api.mapbox.com/styles/v1/mapbox/streets-v11.html?title=true&access_token=pk.eyJ1IjoiYW50YXJhc2VuZ3VwdGEiLCJhIjoiY2x2eXdxam9tMnlsdjJpcGdnZDhxNG1hOCJ9.qiKSj4W-8tFjUvOAbCFBdA#5.00/33.3152/44.3661"
          allowfullscreen>
        </iframe>
        <svg class="hover-svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 500">
          <g transform="translate(0 5)">
            <g>
              <line x1="200" y1="180" x2="260" y2="180" stroke="black" stroke-width="5" />
              <line x1="230" y1="180" x2="230" y2="120" stroke="black" stroke-width="5" />
              <path d="M 275,80 A 50,50 0 0,1 175,80 Z" fill="purple" stroke="black" stroke-width="5" />
            </g>
          </g>
          <title>Information about Mesopotamia</title>
        </svg>
      </div>
      <div id="map3" class="map" style="display:none;">
        <iframe
          width="100%"
          height="100%"
          frameborder="0"
          style="border:0"
          src="https://api.mapbox.com/styles/v1/mapbox/streets-v11.html?title=true&access_token=pk.eyJ1IjoiYW50YXJhc2VuZ3VwdGEiLCJhIjoiY2x2eXdxam9tMnlsdjJpcGdnZDhxNG1hOCJ9.qiKSj4W-8tFjUvOAbCFBdA#4.00/39.0000/22.0000"
          allowfullscreen>
        </iframe>
        <svg class="hover-svg" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 500">
          <g transform="translate(0 5)">
            <g>
              <polygon points="320,180 380,180 380,80 320,80" fill="#d1760f" stroke="black" stroke-width="5" />
              <rect x="335" y="30" width="30" height="50" fill="black" stroke="black" stroke-width="5" />
            </g>
          </g>
          <title>Information about Greece</title>
        </svg>
      </div>
    </div>
  </div>
  <br /><br />
  <p class="body-text">And that concludes the history lesson.</p>
</section>

<style>
  .map {
    width: 100%;
    height: 100%;
    position: relative;
    overflow: hidden; /* Ensure the map doesn't overflow its container */
  }

  .hover-svg {
    position: absolute;
    top: 10px;
    right: 10px;
    width: 150px; /* Increase the width */
    height: 150px; /* Increase the height */
  }

  .hover-svg:hover::after {
    content: attr(title);
    position: absolute;
    top: 170px; /* Adjust the distance from the top */
    right: 10px;
    background-color: rgba(255, 255, 255, 0.8);
    padding: 10px; /* Increase the padding */
    border: 1px solid #ccc;
    border-radius: 5px;
    z-index: 10;
    font-size: 16px; /* Adjust the font size */
    width: 200px; /* Adjust the width */
    text-align: center; /* Center the text */
  }

  /* space after scroll is finished */
  .spacer {
    height: 40vh;
  }

  .maps-container {
    position: sticky;
    top: 10%;
    display: grid;
    width: 50%;
    grid-template-columns: 100%;
    grid-row-gap: 2rem;
    grid-column-gap: 0rem;
    grid-template-rows: repeat(3, 1fr);
    height: 85vh;
    border: 3px solid black;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
  }

  .step {
    height: 110vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 18px;
    background: var(--bg);
    color: #ccc;
    border-radius: 1px;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    text-align: left;
    width: 75%;
    margin: auto;
    max-width: 500px;
    font-family: var(--font-main);
    line-height: 1.3;
    border: 5px solid var(--default);
  }

  .step.active .step-content {
    background: #f1f3f3ee;
    color: var(--squid-ink);
  }

  .steps-container {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }

  /* Comment out the following line to always make it 'text-on-top' */
  @media screen and (max-width: 950px) {
    .section-container {
      flex-direction: column-reverse;
    }

    .steps-container {
      pointer-events: none;
    }

    .maps-container {
      top: 7.5%;
      width: 95%;
      margin: auto;
    }

    .step {
      height: 130vh;
    }

    .step-content {
      width: 95%;
      max-width: 768px;
      font-size: 17px;
      line-height: 1.6;
    }

    .spacer {
      height: 100vh;
    }
  }
</style>







