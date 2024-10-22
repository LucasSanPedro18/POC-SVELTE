<script>
  import { onMount } from "svelte";

  let amount = 0;
  let fromCurrency = "USD";
  let toCurrency = "ARS";
  let convertedAmount = 0;
  let exchangeRates = {};

  // Lista de divisas, ahora incluye ARS (Pesos Argentinos)
  const currencies = ["USD", "EUR", "GBP", "JPY", "AUD", "CAD", "CHF", "CNY", "ARS"];

  // Cargar las tasas de cambio al montar el componente
  onMount(async () => {
    try {
      const response = await fetch(
        "https://open.er-api.com/v6/latest/USD"
      );
      const data = await response.json();
      exchangeRates = data.rates;
    } catch (error) {
      console.error("Error al obtener las tasas de cambio:", error);
    }
  });

  // Función para realizar la conversión
  function convert() {
    if (fromCurrency === toCurrency) {
      convertedAmount = amount;
    } else {
      const rate = exchangeRates[toCurrency] / exchangeRates[fromCurrency];
      convertedAmount = amount * rate;
    }
  }
</script>

<main>
  <h1>Convertidor de Divisas</h1>

  <div>
    <label>Monto:</label>
    <input type="number" bind:value={amount} min="0" />

    <label>De:</label>
    <select bind:value={fromCurrency}>
      {#each currencies as currency}
        <option value={currency}>{currency}</option>
      {/each}
    </select>

    <label>A:</label>
    <select bind:value={toCurrency}>
      {#each currencies as currency}
        <option value={currency}>{currency}</option>
      {/each}
    </select>

    <button on:click={convert}>Convertir</button>
  </div>

  <div>
    <h2>Resultado:</h2>
    <p>{amount} {fromCurrency} = {convertedAmount.toFixed(2)} {toCurrency}</p>
  </div>
</main>

<style>
  main {
    font-family: sans-serif;
    padding: 20px;
  }
  div {
    margin-bottom: 10px;
  }
  input, select, button {
    margin-left: 5px;
    margin-right: 10px;
  }
</style>
