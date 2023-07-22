<script lang="ts">
  import type { Card } from "./types";
  import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
  import Checkbox from "@smui/checkbox"
  import axios from "axios";

  export let cards: Card[] = [];
  export let title: String = '';

  const toggleCollected = async (card: Card) => {
    const paramBody = {
        "fields": {
            "Collected": card['Collected']
        }
    }

    // console.log(card, paramBody);
    const res = await axios.patch(`${card.id}`, paramBody )
    console.log(res.data);
  }

</script>

<div class="table">
    <h1>{title}</h1>
    <DataTable table$aria-label="Card List">
        <Head>
            <Row>
            <Cell>Name</Cell>
            <Cell>Type</Cell>
            <Cell>Collected</Cell>
            </Row>
        </Head>
        <Body>
        {#each cards as card}
            <Row>
                <Cell on:click={() => {console.log('Click!')}}>
                    {card.Name}
                </Cell>
                <Cell>{card.Type}</Cell>
                <Cell checkbox><Checkbox on:change={() => toggleCollected(card)} bind:checked={card['Collected']} /></Cell>
            </Row>
        {/each}
        </Body>
    </DataTable>
</div>

<style>
    .table {
        max-width: '100%';
    }
</style>