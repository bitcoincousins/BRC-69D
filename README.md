# BRC69D - Dynamic Ordinals Standard

Introducing BRC69D, the upgraded dynamic version of the original BRC69. This standard allows Ordinals Collections to be fully customizable on-chain by using the recursive structure of Ordinals. Creators can select which traits are dynamic, so only those specific traits are customizable without affecting the rarity of the rest of the collection. The owner of the inscription can customize their Ordinal by inscribing a child in the correct format.

---

## Bitcoin Cousins - First Dynamic Ordinals Collection

Bitcoin Cousins was created to showcase BRC69D. In this collection, owners can customize their Hair and Eyebrow traits simply by inscribing a child in the correct format. We've provided a variety of colors for Hair and Eyebrow so you can find the look that suits you. Note that you can only change the color, not the type. So the overall rarity of the collection remains unaffected.

---

## Process

1. Inscribing the images of the traits on-chain
2. Inscribing the BRC69D collection deployment JSON
3. Inscribing the BRC69D assets
4. Inscribing the BRC69D child

### Deployment JSON

The deployment JSON contains collection and compiler information along with attributes. The attributes are organized into three layers: the trait type, the trait, and the dynamic trait.

*Example of a collection deploy json:*

```javascript
{
   "p": "brc69d",
   "op": "deploy",
   "collection": {
      "slug": "collection-name"
   },
   "compilerInfo": {
      "preview": "",  // optional preview inscription id
      "renderSize": {
         "width": 800, // width dimension rendered
         "height": 800 // heigh dimension rendered
      },
      "imageRendering": "auto"  // or "pixelated"
   },
"attributes": [
       {
          "traitType": "Background",
          "order": 0,
          "traitList": [
            {
               "name": "Red",
               "dynamic": false,
               "rarity": 50,
               "traits": [
                  {
                     "name": "Red",
                     "id": "/content/trait_inscription_id_1"
                  }
               ]
            },
            {
               "name": "Yellow",
               "dynamic": false,
               "rarity": 50,
               "traits": [
                  {
                     "name": "Yellow",
                     "id": "/content/trait_inscription_id_2"
                  }.
                  // More dynamic traits...
               ]
            },
            // More traits...
          ]
       },
       {
         "traitType": "Hair",
         "order": 1,
         "traitList": [
           {
              "name": "Punk",
              "dynamic": true,
              "rarity": 50,
              "traits": [
                 {
                    "name": "White",
                    "id": "/content/trait_inscription_id_3"
                 },
                 {
                    "name": "Black",
                    "id": "/content/trait_inscription_id_4"
               }
              ]
           },
           {
            "name": "Braid",
            "dynamic": true,
            "rarity": 50,
            "traits": [
               {
                  "name": "White",
                  "id": "/content/trait_inscription_id_5"
               },
               {
                "name": "Black",
                "id": "/content/trait_inscription_id_6"
                },
            // More dynamic traits...
            ]
         }
         ]
      },
      // More trait categories...
   ]
}
```

| Key         | Required | Description                                                  |
| ----------- | -------- | ------------------------------------------------------------ |
| p           | NO      | Protocol: Helps other systems identify and process brc69d events |
| op          | NO      | Operation: Type of event (Deploy)                            |
| slug        | NO      | Slug: Identifier of the collection                           |
| compilerInfo| YES      | Settings for the standard compiler                           |
| attributes  | YES      | Array of trait categories with their inscription IDs and rarities |


### Inscription HTML

In the Inscription HTML, the inscription ID is passed to our compiler. The compiler then detects the most recent child inscription to customize the Ordinal. 

The "t" attribute holds trait pairs used to generate the asset, with each group separated by a hyphen ("-").

For example, from the string "2,0-1,1-6,1-4,7-2,0-6,0-2,0-1,3", a group value of "1,1" means that for the attribute at order 1, the second trait from its trait list is selected and it uses the second dynamic trait. In our Deployment JSON example, this corresponds to the "Black Braid" trait.


```html
<script type="module" >
    (async () => {
      const { OrdClient } = await import('/content/5aa512a80659a7ec189ffd2cdf84af2614c898eec0605a86d0e813eef39c9452i0');
      const ordClient = new OrdClient();
      const inscriptionId = await ordClient.currentInscriptionId();
      const tag = document.createElement('script');
      tag.setAttribute('i', `/r/children/${inscriptionId}`);
      tag.setAttribute('t', "2,0-1,1-6,1-4,7-2,0-6,0-2,0-1,3");
      tag.setAttribute('src', '/content/<complier_inscription_id>');
      tag.setAttribute('d', `/content/<deployer_inscription_id>`);
      document.body.appendChild(tag);
    })();
  </script>
```

| Key  | Required | Description                                                  |
| ---- | -------- | ------------------------------------------------------------ |
| i    | YES      | ID: Current Inscription ID                                    |
| t    | YES      | Traits: index of the trait pairs used to generate the asset   |
| src  | YES      | Source: Points to the standard compiler inscription           |
| d    | YES      | Deploy: Points to your collection's deploy inscription        |

Current Standard Compiler Inscription ID: `<complier_inscription_id>`
Last Updated: March 18, 2025


### Inscribing a BRC69D Child 

The child inscription must be in plain text, following a simple format: groups of three numbers separated by a hyphen. In each group:

The first number indicates the attribute order.
The second number is the trait index.
The third number is the dynamic trait index.

For example:

```
1,0,2-3,0,4
```

When the compiler runs, it picks up the last child inscription and checks the format. It compares the trait index from the child with the default settings and verifies if that trait is dynamic. If everything matches, the asset’s appearance is updated accordingly. If the format is incorrect, the asset will revert to its default look.

This simple format ensures that only valid child inscriptions can change the asset's appearance.
