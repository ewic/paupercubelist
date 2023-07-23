<script lang="ts">
  import type { Card } from "./types";
  import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
  import LinearProgress from '@smui/linear-progress';
  import Checkbox from "@smui/checkbox"
  import axios from "axios";

  export let cards: Card[] = [];
  export let title: String = '';
  export let loaded: boolean = false;

  const toggleCollected = async (card: Card) => {
    const paramBody = {
        "fields": {
            "Collected": card['Collected']
        }
    }

    const res = await axios.patch(`${card.id}`, paramBody )
    if (res && res.status === 200) {
        console.log('Success')
    } else {
        alert("ERROR")
        console.log('error');
    }
  }

  const dynamicSort = (property) => {
    var sortOrder = 1;
    if (property[0] === "-") {
        sortOrder = -1;
        property = property.substr(1);
    }
    return function (a,b) {
        var result = (a[property] < b[property]) ? -1 : (a[property] > b[property]) ? 1 : 0;
        return result * sortOrder; 
    }
  }

  const handleSort = (property) => {
    cards = cards.sort(dynamicSort(property));
  }

</script>

<div class="table">
    <h1>{title}</h1>
    <DataTable table$aria-label="Card List">
        <Head>
            <Row>
                <Cell on:click={() => handleSort('Name')}>Name</Cell>
                <Cell on:click={() => handleSort('Type')}>Type</Cell>
                <Cell on:click={() => handleSort('Collected')}>Collected</Cell>
            </Row>
        </Head>
        <Body>
        {#each cards as card}
            <Row>
                <Cell>
                    {card.Name}
                </Cell>
                <Cell>{card.Type}</Cell>
                <Cell checkbox><Checkbox on:change={() => toggleCollected(card)} bind:checked={card['Collected']} /></Cell>
            </Row>
        {/each}
        </Body>

        <LinearProgress
            indeterminate
            bind:closed={loaded}
            aria-label="Data is being loaded..."
            slot="progress"
        />

    </DataTable>
</div>

<style>
    .table {
        max-width: '100%';
    }
</style>