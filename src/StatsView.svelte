<script lang="ts">
    import CircularProgress from '@smui/circular-progress';

    export let cards:Card[] = []
    
    let progress = 0.0;

    console.log(cards);

    let collectedCount: number = 0;
    cards.forEach((card:Card) => {
        if (card['Collected'] === true) collectedCount = collectedCount + 1;
        progress = collectedCount/cards.length;
    })
</script>

<div>
    <h1>Stats</h1>
    <span>Percentage Collected</span>
    <div>
        <CircularProgress style="height: 48px; width: 48px;" progress={progress} /> {`${Math.trunc(progress*100)}%`}
    </div>
</div>