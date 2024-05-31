<script>
  import Meta from "./Meta.svelte";
  import Title from "./Components/Title.svelte";
  import Intro from "./Components/Intro.svelte";
  import TextAndMathEquations from "./Components/TextAndMathEquations.svelte";


  import CountryBar from "./Components/CountryBarChart.svelte";
  import ContinentBar from "./Components/ContinentBarChart.svelte";


  import ScrollCenter from "./Components/ScrollCenter.svelte";
  import Conclusion from "./Components/Conclusion.svelte";
  import Resources from "./Components/Resources.svelte";
  import ScrollSide from "./Components/ScrollSide.svelte";
</script>

<Meta />
<Title />
<Intro />

<ScrollSide />
<CountryBar />

<ContinentBar />

<Conclusion />
<Resources />
