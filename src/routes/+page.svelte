<script>
  import { onMount } from "svelte";
  import CloseIcon from "../icons/CloseIcon.svelte";

  let offers = $state([[]]);
  let totals = $state([0]);
  let offerNames = $state(["Offer 1"]);
  let catType = $state(["Fine"]);
  let catCount = $state([1]);
  let catalogues = $state({ official: { data: {} } });
  let catalogue = $state("official");

  const catculateTotal = (
    index,
    cat = catType[index],
    count = catCount[index]
  ) => {
    totals[index] =
      Math.round(
        (totals[index] + catalogues[catalogue]["data"][cat] * count) * 100
      ) / 100;
  };

  const addCat = (index) => {
    let cat = offers[index].find((cat) => cat.name === catType[index]);
    if (cat) {
      if (catCount[index] < 0 && cat.count + catCount[index] <= 0) {
        let catIndex = offers[index].findIndex(
          (cat) => cat.name === catType[index]
        );
        offers[index].splice(catIndex, 1);
      } else {
        cat.count += catCount[index];
      }
    } else {
      if (catCount[index] < 0) {
        return;
      }
      offers[index].push({ name: catType[index], count: catCount[index] });
    }
    catculateTotal(index);
  };

  const catculateAllTotal = () => {
    offers.forEach((offer, index) => {
      totals[index] = 0;
      for (let cat of offer) {
        catculateTotal(index, cat["name"], cat["count"]);
      }
    });
  };

  const addOffer = () => {
    offers.push([]);
    offerNames.push("Offer " + offers.length);
    catType.push("Fine");
    catCount.push(1);
    totals.push(0);
  };

  const removeOffer = (i) => {
    offers.splice(i, 1);
    catType.splice(i, 1);
    catCount.splice(i, 1);
    totals.splice(i, 1);
  };

  onMount(async () => {
    const cataloguesRes = await fetch("/catalogues.json");
    catalogues = await cataloguesRes.json();
    window.eGirlStressTest = (i, count) => {
      Object.keys(catalogues["official"]["data"]).forEach((cat) => {
        offers[i].push({ name: cat, count: count });
        catculateTotal(i, cat, count);
      });
    };
  });
</script>

<div class="vh-100 d-flex justify-content-center font-monospace">
  <div class="w-100">
    <nav class="bg-light p-1 text-center d-flex navbar justify-content-center">
      <h1 class="header">Catculator!</h1>
    </nav>
    <form>
      <div class="section mx-auto p-4">
        <div class="d-flex justify-content-between">
          <label for="catalouge" class="form-label">Catalouge</label>
        </div>

        <select
          class="form-select"
          style="width: 40%;"
          bind:value={catalogue}
          onchange={catculateAllTotal}
        >
          {#each Object.values(catalogues) as catalouge, i}
            <option value={Object.keys(catalogues)[i]}>
              {catalouge.name}
            </option>
          {/each}
        </select>
      </div>
      <div class="section mx-auto p-4">
        <div class="d-flex justify-content-between">
          <p class="form-label">Offers</p>
          <button type="button" class="btn" onclick={addOffer}> + </button>
        </div>
        {#if offers.length == 0}
          <div class="col-md-12 ms-5">No offers!</div>
        {/if}
        {#each offers as offer, i (i)}
          <div class="row m-auto border p-2 rounded mb-3">
            <div class="d-flex my-3">
              <input
                class="form-control form-control-sm"
                style="width: 25%;"
                type="text"
                bind:value={offerNames[i]}
              />
              <button
                type="button"
                class="btn text-danger ms-auto align-self-end"
                style="font-size: x-large; padding: unset;"
                onclick={() => removeOffer(i)}
              >
                <CloseIcon></CloseIcon></button
              >
            </div>
            <div class="d-flex align-items-center gap-3 mb-2">
              <select
                class="form-select"
                style="width: 30%;"
                bind:value={catType[i]}
              >
                {#each Object.keys(catalogues[catalogue]["data"]) as cat}
                  <option value={cat}>
                    {cat}
                  </option>
                {/each}
              </select>
              <input
                class="form-control"
                style="width: 20%"
                type="number"
                bind:value={catCount[i]}
              />
              <button
                type="button"
                class="btn btn-primary btn-sm"
                onclick={() => addCat(i)}
              >
                Add Cat
              </button>
            </div>
            <hr />
            <div class="row">
              {#if offers[i].length == 0}
                <div class="col-md-12 mb-3">Nothing offered!</div>
              {/if}
              {#each offers[i] as cat}
                <div class="col-md-3 mb-2">
                  {cat["name"]}: {cat["count"]}
                </div>
              {/each}
            </div>
            <hr />
            <p><b>Total Value:</b> {totals[i]}</p>
          </div>
        {/each}
      </div>
      <div class="section mx-auto p-4">
        <div class="d-flex justify-content-between">
          <p class="form-label">Output</p>
        </div>

        <div class="row m-auto border p-2 rounded mb-3">
          {#if offers.length == 0}
            <div class="col-md-12 my-2">No offers!</div>
          {:else}
            <p class="mb-0">Using <b>{catalogues[catalogue]["name"]}</b> Catalouge!</p>
          {/if}
          {#each offers as offer, i (i)}
            <div>
              <p class="mt-3">{offerNames[i]}: <b>{totals[i]}</b></p>
              <div class="row">
                {#if offers[i].length == 0}
                  <div class="col-md-12 mb-3 ms-4">Nothing offered!</div>
                {/if}
                {#each offers[i] as cat}
                  <div class="col-md-3 mb-2">
                    {cat["name"]}: {cat["count"]}
                  </div>
                {/each}
                <hr />
              </div>
            </div>
          {/each}
        </div>
      </div>
    </form>
  </div>
</div>

<style>
  .header {
    color: var(--bs-primary);
  }

  /* TODO: add button color difference for hover and onclick */

  /* 
  .btn-primary, .btn-primary:hover, .btn-primary:active, .btn-primary:visited{
    background-color: var(--bs-primary) !important;
    border: var(--bs-primary) !important;
  } */

  .section {
    width: 50%;
  }
  @media (max-width: 1500px) {
    .section {
      width: 75%;
    }
  }
  @media (max-width: 748px) {
    .section {
      width: 100%;
    }
  }
</style>
