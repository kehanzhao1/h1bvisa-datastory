
<script>
  import Scrolly from "./Scrolly.svelte";
  import { fade } from 'svelte/transition';
  import { onMount } from "svelte";
  import * as d3 from 'd3'; 
   import LineGraph from "./linegraph.svelte";
   import barchart from "./barchart.svelte";
  import Barchart from "./barchart.svelte";
  import Linegraph from "./linegraph.svelte";
  let value;
  let data= [];
  const steps = [    {
      heading: "🗳️ A popular visa",
      paragraph: "Since 2006, H-1B visa quota has been capped at 85,000 annually. The number of registration has far exceeded it.",
    },
    {
      heading: "🎲 Winning the lottery",
      paragraph: "A random, computer generated lottery is used to select applicants unbiasedly. The odds have decreaed to 1 in 5 in recent years",
    },
    {
      heading: "🙋 Who are the applicants?",
      paragraph: "Majority are from India and China, with demographics being young male under 39 years old.",
    },
    {
      heading: "👨🏻‍💻 What occupations are most common?",
      paragraph: "Most have a bachelor's degree or higher and work in the tech industry.",
    }
  ];

 
</script>
<main>
 <div class="banner">
   <div class="text">
     <h1>Demystifying the H-1B Visa</h1>
   </div>
  </div>
  <section class="intro">
    <h1>🎰 How competitive is the H-1B visa? </h1>
    <p>
     As a country of immigration, thousands of people apply for U.S. visas. The H-1B is a visa category that allows employers to hire international talent for certain occupations and also allows holders to be eligible for permanent residency. 
     However, applicants are subject to a <strong>lottery system </strong> as demand greatly exceeds visa cap.
    </p>
  </section>

<div class="container">
  <div class="scrolly">
    <Scrolly bind:value>
      {#each steps as step, i}
      <div class="step" class:active={value === i} on:click={() => value = i}>
        <h2 >{step.heading}</h2>
        <p>{step.paragraph}</p>
      </div>
      {/each}
    </Scrolly>
  </div>
  
  <div class="graph" style="position: relative; height: 400px;"> <!-- Adjust height as needed -->
    <div style="position: absolute; top: 0; left: 0; right: 7%; display: {value === 0 || value === 1 ? 'block' : 'none'};">
      <LineGraph {value} />
    </div>
    <div style="position: absolute;  top: 0; left: 0; right: 7%; display: {value === 2 || value === 3 ? 'block' : 'none'};">
      <Barchart {value} />
    </div>
  </div>
</div>
<section class="additional-container">
  <div class="text-content">
    <h2>Cons of holding H-1B</h2>
  </div>
  <div class="image-container">
    <img src="pixel.png" alt="Pixel Image" />
  </div>
  
  <div class="text-container">
    <div class="overlay">H-1B "Slaves"🙍</div>
    <ul>
      <li>Following a lay-off, holders have to leave U.S. within 60 days</li>
      <li>Holders cannot have a side-income</li>
      <li>Changing jobs is subjected to immigration approval</li>
      <li>Employers may have less incentive to promote holders</li>
      <!-- Add more points as needed -->
    </ul>
  </div>
</section>
</main>

<style>
  .intro {
    margin-bottom: 2rem; /* Add space between the intro section and the container */
    margin-top: 2rem;
    width: 90vw; /* Make the intro section span most of the page */
    margin: 0 auto; /* Center the intro section */
    text-align: center; /* Center the text */
    position: relative; 
    height: auto; /* Adjust height as needed */
    display: flex; /* Center content */
    align-items: center;
    padding:2rem
  }

  @keyframes slideInFromLeft {
    from {
      opacity: 1;
      transform: translateX(-100%); /* Start off-screen to the left */
    }
    to {
      opacity: 1;
      transform: translateX(0); /* End at the original position */
    }
  }
  .intro h1 {
    margin-bottom: 2rem; /* Add space between the h1 and p elements */
    font-size: 2.5rem; /* Adjust font size as needed */
    font-family: 'Lato', sans-serif;
    font-align: right;
    flex: 1;
    margin-right: 1rem;
    margin-left: 1vw;
    animation: slideInFromLeft 1s ease-in-out;
  }
  .intro p {
    font-size: 1.5rem;
    width: 10vw;
    height: auto;
     /*border: 3.5px solid #141a3e; Add a border around the p element */
    padding: 2.5rem; /* Add padding inside the p element */
   /* Optional: Add rounded corners to the border */
    background-color: #cbd2ed;
    border-left: 5px solid #dbe1ecfd; /* Add a top border */
    flex: 1.2 ;
    border-radius: 12px;
    font-family: 'Lato', sans-serif;
    margin-left: 2vw;
    margin-right: 15vw;
  }
 

  .graph {
    flex: 1.2;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column; 
    margin-left: -6vw;
    margin-right: 5%;
    margin-top: 10vh;
    
  }
  .scrolly {
    flex: 1.5;
    height: 70vh;
    width: auto;
    overflow-y: auto;
    margin-right: 8vw;
    margin-left: 8vw;
    padding-right: 1rem;
    
  }
  .step {
    height: 40vh;
    cursor: pointer; 
    background: #e8e2e2;
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    font-family: 'Lato', sans-serif; 
    align-items: center;
    justify-content: center;
    display: flex;
    flex-direction: column;
    border-radius: 10px;
  }
  .step h2 {
    font-size: 2rem; /* Adjust font size as needed */
    margin-bottom: 0.5em; /* Add margin below heading */
    font-family: 'Lato', sans-serif; /* Apply Lato font */
  }
  .step p {
    font-size: 1.5rem; /* Adjust font size as needed */
    padding: 1em; /* Add padding for better readability */
    font-family: 'Lato', sans-serif; /* Apply Lato font */
  }
  
  .step.active {
    background: #aa3131b7;
  }
  
  .step.active h2,.step.active p {
    color: white; /* Change text color to white when active */
    font-weight: bold;;
  }
  .mark {
    padding: 0.5em;
    position: fixed;
    top: 0;
    right: 0;
  }
  
  section {
    width: 20vw;
    margin: 0 auto;
    margin-bottom: 0.5em; 
  }
  
  .spacer {
    height: 10vh;
  }

  
  * {
  margin: 0;
  padding: 0;
  box-sizing: border-box; /* Ensure consistent box model */
  
}
 html, body {
  margin: 0; /* Remove default browser margin */
  padding: 0; /* Remove default browser padding */
  width: 100%; /* Ensure body spans full width */
  height: 100%; /* Ensure body spans full height */
 
  overflow-x: hidden; 
}
main {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}
.banner {
  /* Make sure banner is pinned to the top */
  top: 0;
  left: 0;
  display: flex; /* Center content */
  z-index: 1; /* Ensure banner stays on top of other content */
  background-color: rgb(242, 238, 239);
  background-size: cover; 
  /*background: linear-gradient(to bottom, #031436 15%, rgba(24, 29, 24, 0) 100%);*/   
  color: white;
  padding: 2rem; /* Increase padding for larger banner */
  text-align: center;
  font-size: 2rem; /* Larger font size for the banner title */
  width: 100vw; /* Full viewport width */
  height: 35vh; /* Adjust height for a larger banner (30% of viewport height) */
  box-sizing: border-box; /* Include padding in width/height calculations */
  display: flex; /* Center content */
  justify-content: center; /* Horizontally centered */
  align-items: center; /* Vertically centered */
  margin-top: -5vh;
  margin-left: -50vh;
  margin-right: -50vh;
  margin-bottom: 5vh;
  position: relative;
  border-bottom: 5px solid #73789ea4;
  border-right: 5px solid transparent;
  animation: borderSlideIn 1s ease-in-out;
  border-radius: 10px;
}
@keyframes borderSlideIn {
  from {
    border-bottom-width: 0;
    border-right-width: 0; /* Start with no right border */
  }
  to {
    border-bottom-width: 5px;
    border-right-width: 5px; /* End with the right border */
  }
}

.banner .text h1 {
     /* Take up the left side of the banner */
    display: flex;
    justify-content: center; /* Horizontally centered */
    align-items: center; /* Vertically centered */
    color: rgb(1, 7, 19); /* Set font color to black */
    position: relative;
    order:1;
    font-size: 4vw; 
    font-family:  'Lato', sans-serif; 
  
  }
  .banner .text img {
    position: relative;
    order: 2;
    /* Align the image to the right */
    /* Center the image vertically */
    transform: translateX(-5%);
    transform: translateY(5%); /* Adjust for vertical centering */
    height: 160px; /* Adjust the height as needed */
    width:10vw; /* Maintain aspect ratio */
    margin-left: -1rem; /* Space between text and image */
  }
    /* Optional: Style the scrollbar */
    .scrolly::-webkit-scrollbar {
    width: 11px;
  }

  .scrolly::-webkit-scrollbar-thumb {
    background-color: #d3d6e2;
    border-radius: 4px;
  }

  .scrolly::-webkit-scrollbar-thumb:hover {
    background-color: #d3d6e2;
  }

  .additional-container {
    display: flex;
    margin-top: 0rem;
    padding: 2rem;
    align-items: center;
    width: 100vw;
    margin-bottom: 10vh;
  }

  .image-container {
    flex: 1;
    text-align: center;
  }

  .image-container img {
    max-width: 30%;
    height: auto;
    margin-right: -8vw;
  }

  .text-container {
    position: relative;
    flex: 1.5;
    padding: 3rem;
    background-color: #cbd2ed;
    border-left: 5px solid #dbe1ecfd;
    border-radius: 12px;
    font-family: 'Lato', sans-serif;
    margin-left: 2vw;
    margin-right: 25%;
    height:100%;
    
  }

  .text-container ul {
    list-style-type: disc;
    padding-left: 1rem;
  }

  .text-container li {
    font-size: 1.5rem;
    margin-bottom: 0.5rem;
  }
  
  .text-content h2 {
    font-family: 'Lato', sans-serif;
    font-size: 38px;
    margin-bottom: 1rem;
    margin-right: -10vw;
    margin-left: 5vw;
  }
  .overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(32, 40, 61, 0.8); /* Semi-transparent background */
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 2rem;
    border-radius: 12px;
    cursor: pointer;
    transition: opacity 0.3s ease, transform 0.3s ease;
  }
  .text-container:hover .overlay {
    opacity: 0;
    transform: scale(1.05);
  }
  .container {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    width: 100vw;
    height: 100vh;
    margin-top: 10vh;
    /*flex-direction: column;*/
  }

</style>
