<script>
  import { onMount } from "svelte";
  let offers = $state([[]]);
  let totals = $state([0]);
  let catType = $state(["Fine"]);
  let catCount = $state([1]);
  let catalogue = $state({});

  const addCat = (index) => {
    let cat = offers[index].find((cat) => cat.name === catType[index]);
    if (cat) {
      if (catCount[index] < 0 && cat.count == 1) {
        let catIndex = offers[index].findIndex(
          (cat) => cat.name === catType[index]
        );
        offers[index].splice(catIndex, 1);
      } else {
        if (cat.count + catCount[index] < 0) {
          return;
        }
        cat.count += catCount[index];
      }
    } else {
      if (catCount[index] < 0) {
        return;
      }
      offers[index].push({ name: catType[index], count: catCount[index] });
    }

    totals[index] =
      Math.round(
        (totals[index] + catalogue[catType[index]] * catCount[index]) * 100
      ) / 100;
  };

  const addOffer = () => {
    offers.push([]);
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
    const catalogueRes = await fetch("/catalogue.json");
    catalogue = await catalogueRes.json();
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
          <label for="people" class="form-label">Offers</label>
          <button type="button" class="btn col col-md-1" onclick={addOffer}>
            +
          </button>
        </div>

        {#each offers as offer, i (i)}
          <div class="row m-auto border p-2 rounded mb-3">
            <div class="d-flex">
              <p>Offer {i + 1}</p>
              <button
                type="button"
                class="btn text-danger ms-auto align-self-end"
                style="font-size: x-large; padding: unset;"
                onclick={() => removeOffer(i)}
              >
                ✖</button
              >
            </div>
            <div class="d-flex align-items-center gap-3 mb-2">
              <select class="form-select w-25" bind:value={catType[i]}>
                {#each Object.keys(catalogue) as cat}
                  <option value={cat}>
                    {cat}
                  </option>
                {/each}
              </select>
              <input
                class="form-control"
                style="width: 10%"
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
              {#each offers[i] as cat}
                <div class="col-md-3 mb-2">
                  {cat["name"]}: {cat["count"]}
                </div>
              {/each}
            </div>
            <hr />
            <p><b>Total:</b> {totals[i]}</p>
          </div>
        {/each}
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
  .section {
    width: 50%;
  }
</style>
