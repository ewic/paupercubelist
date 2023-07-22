<script lang="ts">
  import { onMount } from 'svelte';
  import type { Card } from './types';
  import axios from 'axios';
  import LayoutGrid, { Cell } from '@smui/layout-grid';
  import CardTable from './CardTable.svelte';

  const KEY = import.meta.env.VITE_KEY
  const BASEURL = import.meta.env.VITE_BASEURL

  console.log(KEY);

  axios.defaults.baseURL = BASEURL;
  axios.defaults.headers.common = {'Authorization': `Bearer ${KEY}`}

  let cards: { 
    w: Card[],
    u: Card[],
    b: Card[],
    r: Card[],
    g: Card[],
    wu: Card[],
    wb: Card[],
    wr: Card[],
    wg: Card[],
    ub: Card[],
    ur: Card[],
    ug: Card[],
    br: Card[],
    bg: Card[],
    rg: Card[],
    nc: Card[],
    lands: Card[],
  } = {
    w: [],
    u: [],
    b: [],
    r: [],
    g: [],
    wu: [],
    wb: [],
    wr: [],
    wg: [],
    ub: [],
    ur: [],
    ug: [],
    br: [],
    bg: [],
    rg: [],
    nc: [],
    lands: [],
  };
  
  let records = []

  onMount(async () => {
    let offset: string = undefined;
    do {
      const res = await axios.get('/', {params: {offset: offset}});
      records.concat(res.data.records);
      
      for (var i in res.data.records) {
        let item = res.data.records[i]

        const CMC = item.fields['CMC'] ? item.fields['CMC'] : 0;

        const collected = item.fields['Collected'] ? true : false
        const color = determineColor(item.fields['Color'])

        let card = {
          'id': item.id,
          'Name': item.fields['Name'],
          'CMC': CMC,
          'Type': item.fields['Type'],
          'Color': color,
          'Collected': collected,
          'image URL': item.fields['image URL'],
          'Tags': item.fields['Tags'],
          'MTGO ID': item.fields['MTGO ID'],
          'Notes': item.fields['Notes'],
        }

        if (card['Type'].includes('Land')) {
          cards['lands'].push(card);
        } else {
          const color = card['Color'].toLowerCase();
          cards[color].push(card);
        }


      }
      offset = res.data.offset;

    } while (offset != undefined)

    cards = cards;

  })

  const determineColor = (value: string) => {
    let out = '';
    if (value == undefined) return 'nc';
    
    value = value.toLowerCase();
    if (value.includes('w')) out += 'w';
    if (value.includes('u')) out += 'u';
    if (value.includes('b')) out += 'b';
    if (value.includes('r')) out += 'r';
    if (value.includes('g')) out += 'g';

    return out;
  }

</script>

<main>
  <div class="main">
    <LayoutGrid>
      <Cell><CardTable title="White" cards={cards['w']} /></Cell>
      <Cell><CardTable title="Blue" cards={cards['u']} /></Cell>
      <Cell><CardTable title="Black" cards={cards['b']} /></Cell>
      <Cell><CardTable title="Red" cards={cards['r']} /></Cell>
      <Cell><CardTable title="Green" cards={cards['g']} /></Cell>
      <Cell><CardTable title="Artifacts/Colorless" cards={cards['nc']} /></Cell>
      <Cell><CardTable title="Lands" cards={cards['lands']} /></Cell>
    </LayoutGrid>
    <LayoutGrid>
      <Cell><CardTable title="Azorius" cards={cards['wu']} /></Cell>
      <Cell><CardTable title="Orzhov" cards={cards['wb']} /></Cell>
      <Cell><CardTable title="Boros" cards={cards['wr']} /></Cell>
      <Cell><CardTable title="Selesnya" cards={cards['wg']} /></Cell>
      <Cell><CardTable title="Dimir" cards={cards['ub']} /></Cell>
      <Cell><CardTable title="Izzet" cards={cards['ur']} /></Cell>
      <Cell><CardTable title="Simic" cards={cards['ug']} /></Cell>
      <Cell><CardTable title="Rakdos" cards={cards['br']} /></Cell>
      <Cell><CardTable title="Golgari" cards={cards['bg']} /></Cell>
      <Cell><CardTable title="Gruul" cards={cards['rg']} /></Cell>
    </LayoutGrid>
  </div>
</main>

<!-- SMUI Styles -->
<link rel="stylesheet" href="node_modules/svelte-material-ui/bare.css" />

<style>
  .main {
    width: '100%';
  }
</style>