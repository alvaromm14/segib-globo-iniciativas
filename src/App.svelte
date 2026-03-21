<script>
  import world from "$data/110m.json";
  import data from "$data/iniciativas.json";
  import Legend from "$components/Legend.svelte";
  import Desplegable from "$components/Desplegable.svelte";
  import Globo from "$components/Globo.svelte";
  import { format } from "d3-format";
  import { scaleLinear } from "d3-scale";
  import { max } from "d3-array";
  import { onMount } from "svelte";

  onMount(() => {
    function updateIframeHeight() {
      const el = document.documentElement;
      const rect = el.getBoundingClientRect();
      const styles = window.getComputedStyle(el);
      const margin =
        parseFloat(styles.marginTop) + parseFloat(styles.marginBottom);
      const height = Math.ceil(rect.height + margin);
      window.parent.postMessage({ type: "resize-iframe", value: height }, "*");
    }

    updateIframeHeight();

    if (window.ResizeObserver) {
      new ResizeObserver(() => updateIframeHeight()).observe(
        document.documentElement,
      );
    } else {
      window.addEventListener("resize", updateIframeHeight);
    }

    window.addEventListener("message", (event) => {
      if (event.data.type === "request-resize") updateIframeHeight();
    });
  });

  const formatThousands = (n) => format(",")(n).replace(/,/g, ".");

  const uniqueCountries = Array.from(new Set(data.map((d) => d.pais))).sort(
    (a, b) => a.localeCompare(b),
  );

  $: desplegableOptions = uniqueCountries.map((name) => ({
    label: name,
    count: data
      .filter((d) => d.pais === name)
      .reduce((sum, d) => sum + d.iniciativas, 0),
  }));

  let selectedCountryName = "";
  let tooltipData = null;
  let iniciativas = null;
  let colorScale = scaleLinear()
    .domain([
      0,
      max(data.map((d) => d.iniciativas)) / 2,
      max(data.map((d) => d.iniciativas)),
    ])
    .range(["#ffffd9", "#ffcb04", "#ef4423"]);

  function handleTooltipChange(e) {
    tooltipData = e.detail.tooltipData;
    iniciativas = e.detail.iniciativas;
    colorScale = e.detail.colorScale;
  }
</script>

<div class="chart-container">
  <h1>
    Iniciativas de cooperación Sur-Sur Bilateral y Triangular de los países de
    Iberoamérica con todos los socios (2007-2024)
  </h1>

  <div class="controls">
    <Desplegable
      options={desplegableOptions}
      bind:value={selectedCountryName}
      placeholder="Selecciona un país"
      formatCount={formatThousands}
    />
    <div class="legend-wrapper">
      <Legend {colorScale} {iniciativas} data={tooltipData} />
    </div>
  </div>

  <Globo
    {world}
    {data}
    {selectedCountryName}
    on:tooltipChange={handleTooltipChange}
  />
</div>

<style>
  .chart-container {
    max-width: 1210px;
    margin: 0 auto;
    color: #3d3935;
  }

  h1 {
    text-align: center;
    color: #212c55;
    font-size: 20px;
    line-height: 26px;
    margin-bottom: 1rem;
  }

  .controls {
    display: flex;
    flex-direction: column;
    align-items: stretch;
    gap: 1rem;
    margin-bottom: 1rem;
  }
</style>
