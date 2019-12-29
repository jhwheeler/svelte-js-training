<script>
  const base = 'http://numbersapi.com'

  let result
  let year

  function getFact (num) {
    const fact = fetch(`${base}/${year}/year`).then(response => response.text())
    return fact
  }

  $: year && (async() => result = await getFact())()

</script>

<h1>Get a random fact for any year since 1 AD</h1>

<input bind:value={year} type="number" />

{#await result then fact}
  {#if fact}
    <h3>{fact}</h3>
  {/if}
{/await}

<style>
  h3 {
    margin-top: 1rem;
  }

  input {
    text-align: center;
    height: 2rem;
    width: 4rem;
    border: 1px solid gray;
    border-radius: 5px;
    background-color: #f2f2f2;
    font-size: 1rem;
  }
</style>

