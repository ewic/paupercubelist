<script lang="ts">
  import { onMount } from 'svelte';
  import type { Card } from './types';
  import axios from 'axios';
  import LayoutGrid, { Cell } from '@smui/layout-grid';
  import CardTable from './CardTable.svelte';
  import StatsView from './StatsView.svelte';

  const KEY = import.meta.env.VITE_KEY
  const BASEURL = import.meta.env.VITE_BASEURL

  axios.defaults.baseURL = BASEURL;
  axios.defaults.headers.common = {'Authorization': `Bearer ${KEY}`}

  let loaded = false;
  let allCards: Card[] = [];

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

        allCards.push(card);

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
    loaded = true;

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
      <StatsView cards={allCards} />
    </LayoutGrid>
    <LayoutGrid>
      <Cell><CardTable title="White" cards={cards['w']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Blue" cards={cards['u']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Black" cards={cards['b']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Red" cards={cards['r']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Green" cards={cards['g']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Artifacts/Colorless" cards={cards['nc']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Lands" cards={cards['lands']} loaded={loaded} /></Cell>
    </LayoutGrid>
    <LayoutGrid>
      <Cell><CardTable title="Azorius" cards={cards['wu']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Orzhov" cards={cards['wb']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Boros" cards={cards['wr']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Selesnya" cards={cards['wg']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Dimir" cards={cards['ub']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Izzet" cards={cards['ur']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Simic" cards={cards['ug']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Rakdos" cards={cards['br']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Golgari" cards={cards['bg']} loaded={loaded} /></Cell>
      <Cell><CardTable title="Gruul" cards={cards['rg']} loaded={loaded} /></Cell>
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