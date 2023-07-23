# Pauper Cube List

This was mostly just done to learn Sveltekit

It's a table interface for a MTG Pauper Cube collection

The list is the main one from here: https://cubecobra.com/cube/list/thepaupercube

I imported it into airtable and created this rudimentary interface to check off collected or not collected.

Demo in process...

Produced with Vite

# Some cool ideas

## Card sorting

The format in which the cards in a cube are sorted is as a string containing the letters w,u,b,r,g, or undefined in any order. I wanted to sort them into buckets of colors up to two colors, so my method for determining color was done by building the color string like so:

```ts
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
```

This converts the cards' color value, which is a string in any order, into a string where the order always follows `wubrg`. This makes putting the cards into their buckets easy later on.

# Getting Started

This was made just for me for fun, but if you want some basic direction on having your own for some reason, do the following:

This was made with airtable in mind, any basic rest API could work, but I've been messing with Airtable lately so here we are.

- Export a cube from cubecobra as csv
- Paste it into a new Airtable table. I will not be providing instructions on that here, it's pretty intuitive.
- Clone the repo
- Create a `.env` file with the following data
```
VITE_KEY='YOUR-AIRTABLE-KEY'
VITE_BASEURL='https://api.airtable.com/v0/YOUR-AIRTABLE-BASE/YOUR-AIRTABLE-TABLE/'
```
- Run the following command
```
npm run dev
```

# Future features

- Currently the app only sorts multi-color cards into 2-color buckets. It does not support 3-color cards. The basic pauper cube does not contain any 3, 4, or 5 color cards so this is fine, but maybe I wanna add it later?
- I'd like image previews to get loaded on hover, but it does the bare minimum of what I want now, which is to manage a collection, so I'll maybe take care of it later.

