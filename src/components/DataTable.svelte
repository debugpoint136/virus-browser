<script>
  import SvelteTable from "./SvelteTable.svelte";
  import { onMount, afterUpdate } from "svelte";
  import { Cart } from "../stores/Cart.js";
  import { saveDataOnWindow } from "../scripts/saveDataOnWindow";
  export let virusName;
  export let DATA;
  export let FILESJSON;
  // import FILESJSON from '../json/database.json';
  // import FILESJSON from "../json/pairwise.json";
  import ALIGNMENTSJSON from "../json/alignment_bed.json";

  function updateCart(input) {
    let found = $Cart.data.filter(
      d => d.Accession === input.detail.row.Accession
    );
    if (found.length > 0) {
      Cart.addDataItems(
        $Cart.data.filter(d => d.Accession !== input.detail.row.Accession)
      );
    } else {
      Cart.addDataItems([...new Set([...$Cart.data, input.detail.row])]);
    }
    const tracksWindow = saveDataOnWindow(
      $Cart.data,
      virusName,
      FILESJSON,
      ALIGNMENTSJSON
    );
  }

  let example = 0;
  let sortBy = "id";
  let sortOrder = 1;
  let iconAsc = "↑";
  let iconDesc = "↓";
  const ROWS_PER_PAGE = 20;
  const COLS = [
    {
      key: "_id",
      title: "ID",
      value: v => v._id,
      sortable: true,
      filterOptions: rows => {
        let nums = {};
        rows.forEach(row => {
          let num = Math.floor(row._id / ROWS_PER_PAGE);
          if (nums[num] === undefined)
            nums[num] = {
              name: `${num * ROWS_PER_PAGE} to ${(num + 1) * ROWS_PER_PAGE}`,
              value: num
            };
        });
        // fix order
        nums = Object.entries(nums)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(nums);
      },
      filterValue: v => Math.floor(v._id / ROWS_PER_PAGE),
      headerClass: "text-left"
    },
    {
      key: "Accession",
      title: "Accession",
      value: v => v.Accession,
      sortable: true,
      filterOptions: rows => {
        let letrs = {};
        rows.forEach(row => {
          let letr = row.Accession.charAt(0);
          if (letrs[letr] === undefined)
            letrs[letr] = {
              name: `${letr.toUpperCase()}`,
              value: letr.toLowerCase()
            };
        });
        // fix order
        letrs = Object.entries(letrs)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(letrs);
      },
      filterValue: v => v.Accession.charAt(0).toLowerCase()
    },
    {
      key: "Isolate",
      title: "Isolate",
      value: v => v.Isolate,
      sortable: true,
      filterOptions: rows => {
        let letrs = {};
        rows.forEach(row => {
          let letr = row.Isolate.charAt(0);
          if (letrs[letr] === undefined)
            letrs[letr] = {
              name: `${letr.toUpperCase()}`,
              value: letr.toLowerCase()
            };
        });
        // fix order
        letrs = Object.entries(letrs)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(letrs);
      },
      filterValue: v => v.Isolate.charAt(0).toLowerCase()
    },
    {
      key: "Molecule_Type",
      title: "Molecule Type",
      value: v => v.Molecule_Type,
      sortable: true,
      filterOptions: rows => {
        let letrs = {};
        rows.forEach(row => {
          let letr = row.Molecule_Type;
          if (letrs[letr] === undefined)
            letrs[letr] = {
              name: letr,
              value: letr
            };
        });
        // fix order
        letrs = Object.entries(letrs)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(letrs);
      },
      filterValue: v => v.Molecule_Type
    },
    {
      key: "Country",
      title: "Country",
      value: v => v.Country,
      sortable: true,
      filterOptions: rows => {
        let letrs = {};
        rows.forEach(row => {
          let letr = row.Country.split(":")[0];
          if (letrs[letr] === undefined)
            letrs[letr] = {
              name: letr,
              value: letr
            };
        });
        // fix order
        letrs = Object.entries(letrs)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(letrs);
      },
      filterValue: v => v.Country.split(":")[0]
    },
    {
      key: "Collection_Date",
      title: "Collection Date",
      value: v => v.Collection_Date,
      sortable: true,
      filterOptions: rows => {
        let letrs = {};
        rows.forEach(row => {
          let splitStr = row.Collection_Date.split("-");
          // let year =
          //   splitStr.length !== 0 ? splitStr[splitStr.length - 1] : "N/A";
          let year =
            splitStr.length !== 0 ? splitStr[0] : "N/A";
          let letr = year;
          if (letrs[letr] === undefined)
            letrs[row.Collection_Date] = {
              // name: letr,
              // value: letr,
              name: row.Collection_Date,
              value: row.Collection_Date
            };
        });
        // fix order
        letrs = Object.entries(letrs)
          .sort()
          .reduce((o, [k, v]) => ((o[k] = v), o), {});
        return Object.values(letrs);
      },
      filterValue: v => {
        let splitStr = v.Collection_Date.split("-");
        // let year =
        //   splitStr !== undefined && splitStr.length !== 0
        //     ? splitStr[splitStr.length - 1]
        //     : "N/A";
        // return splitStr[splitStr.length - 1];
        let year =
          splitStr !== undefined && splitStr.length !== 0
            ? splitStr[0]
            : "N/A";
        // return splitStr[0];
        return v.Collection_Date;
      }
    }
  ];
</script>

{#if DATA !== undefined}
  <div class="overflow-y-scroll border-2 p-2 m-4 h-screen">
    <div
      class="w-full pt-6 pb-6 text-sm text-center md:text-left fade-in flex
      justify-center">
      <div class="text-gray-500 no-underline hover:no-underline">
        Use header row to filter data, click on rows to add to Cart
      </div>
    </div>
    <SvelteTable on:clickRow={updateCart} columns={COLS} rows={DATA} />
  </div>
{:else}
  <div>No data to show</div>
{/if}
