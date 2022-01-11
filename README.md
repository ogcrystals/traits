# OG:CR traits generation algortithm and data

OG:CR is a generative art project that consists of 10301 unique 3D crystals generated based on blockchain data. The following text describes how the traits are calculated from the raw data containing transactions list and wallets metadata. The scene file was created and tested in Houdini 18.5.633.

<br>

Project website: [www.ogcrystals.com](https://www.ogcrystals.com/)

Collection on OpenSea: [opensea.io/collection/og-crystals](https://opensea.io/collection/og-crystals)

SideFX Houdini: [www.sidefx.com](https://www.sidefx.com/)

<br><br>


## Input data for each generated part of the crystal

<br>

**Balance_eth** - the amount of ETH the wallet holds

**Balance_nft** -  the number of NFTs the wallet holds

**Change_eth** - the difference between the current ETH amount and the amount 3 months ago

**Change_nft** - the difference between the current NFT number and the number 3 months ago

**Collection_name** - the list of 7 possible collab-collections if the wallet holds any of NFTs from these collections:
* 89 Seconds
* ArtBlocks
* BoredApes
* CryptoPunks
* Ksoids
* Meebits
* Omnimorphs

**Crystal_color** - the color group selected on minting:
* 0 - Red
* 1 - Yellow
* 2 - Green
* 3 - Blue
* 4 - Violet
* 5 - Neutral

**Floor** - the collection floor determined by OpenSea API at the moment of transaction

**Id_block** - the block number in the Ethereum blockchain in which the transaction was stored

**Id_crystal** - the crystal ID within the collection, 1-10301

**Id_iteration** - the generation number this part forms, 0 for Gen1 to 6 for Gen6

**Id_wallet** - a random number based on the wallet address

**Price** - the amount of ETH paid in the transaction

**Timestamp** - UTC timestamp of the transaction

**Transaction_num** - the total number of transactions made by the wallet

**Wallet** - the wallet address

<br><br>

## The traits generation algorithm

<br>

The parts of the algorithm are executed in the order of appearance in the document and each one may override traits set by the previous part. This means that Bright/Dark traits override regular color traits, special materials Obsidian/Gold/Quartz/Pyrite override Bright/Dark, collab-collection traits override special materials, and Dead trait overrides all of them.

<br>

#### id_uniq

Generate a unique ID for the crystal part based on Crystal ID, Block Number, Wallet Address, Timestamp.

<br>

#### crystal_type

By default Spiky, Fractal, Cluster and Coral types have equal probabilities with 10% in favor of Coral. 

The type can be changed then and the probability of this is based on the absolute change in NFT count owned by the wallet - 10% for 3-10 change, 20% for 10-50, and 30% for more than 50. If the probability is met the type will be replaced by random angular type (Spiky, Fractal, or Cluster) for the positive NFT count change and by random Coral type for negative ones.

After that for 8% of Crystal IDs and 5% Wallet IDs, the crystal type may be replaced by a constant determined by Crystal ID or Wallet ID respectively, creating crystals that have all parts of the same type and wallets that create only one particular type of crystal part.

<br>

#### complexity_A and complexity_B

Generates parameters that determine the shape/density/size of each crystal part. 20% of the parts have this parameter set to random base on the id_uniq attribute. 80% have the parameter mapped from the number of transactions made by each particular wallet.

<br>

#### color_pal_group

For the Gen1 part, the color is set to one chosen on minting for the whole crystal.

For the next generation parts in 25% crystal IDs and 5% wallet IDs the color remains the same creating constant color crystals based on crystal ID and wallet ID respectively. In all other cases, the color is selected randomly based on the id_uniq attribute.

<br>

#### pal_A 

Selects color pallet files based on the color_pal_group that will be used for color sampling for the crystal primary color.

<br>

#### pal_B 

Selects color pallet files for the secondary color. For 80% of crystal parts, the secondary color palette is the same as the primary one. For the rest 20% the secondary palette is selected randomly.

<br>

#### shades_A_B

Samples bright-mid-dark color triplets from random color sets within the selected color palette. Then creates a gradient based on these colors and then samples the primary crystal color from the random part of the gradient. Repeats the same process for the secondary color.

<br>

#### special_bright_A and special_bright_B

The chance of having the Bright trait is set to 0.1% by default. For 2% of crystal IDs and 2% of wallet IDs, the chance is changed to 99%. For the rest additional chance is added based on the change in ETH amount the wallet holds (only positive change counts) - +2% for 0.1-2.5 ETH, +4% for 2.5-10 ETH, and +12% for more than 10 ETH. The same process is applied for both primary and secondary colors.

<br>

#### special_dark_A and special_dark_B

The chance of having the Dark trait is set to 0.1% by default. For 2.5% of crystal IDs and 2.5% of wallet IDs, the chance is changed to 99%. For the rest additional chance is added based on the change in ETH amount the wallet holds (only negative change counts) - +5% for - 0.1 to -2.5 ETH, +10% for -2.5 to -10 ETH, and +20% for more than 10 ETH lost. The same process is applied for both primary and secondary colors.

<br>

#### apply_special_colors

Changes primary and secondary colors to a Bright/Dark version if the part got these traits.

<br>

#### shade_mix_type

Randomly selects one of 5 possible mixing types that define the pattern used to mix primary and secondary colors on the crystal surface

<br>

#### mat_1_obsidian

Determines if the part has the Obsidian trait, by default the chance is 0. 

For 0.5% of the crystal parts, 1% of crystal IDs, and 1% of wallet IDs the chance is increased by 99.95% - representing “pure” luck and lucky crystals and wallets. An additional chance is added based on the number of NFTs the wallet holds - starting from 2% for 0 NFTs to 0.1% for 100 NFTs and more. Meaning the fewer NFTs the wallet holds the higher chance to get the Obsidian trait.

<br>

#### mat_2_gold

Determines if the part has the Gold trait, by default the chance is 0. 

For 0.5% of the crystal parts, 1% of crystal IDs, and 1% of wallet IDs the chance is increased by 99.95% - representing “pure” luck and lucky crystals and wallets. An additional chance is added based on the amount of ETH the wallet holds - starting from 2% for 0 ETH to 0.1% for 15 ETH and more. Meaning the fewer ETH the wallet holds the higher chance to get the Gold trait.

<br>

#### mat_3_quartz

Determines if the part has the Quartz trait, by default the chance is 0. 

For 1% of the crystal parts, 1% of crystal IDs, and 1% of wallet IDs the chance is increased by 99.95% - representing “pure” luck and lucky crystals and wallets. An additional chance is added based on the number of NFTs the wallet holds - starting from 2% for 100 NFTs to 0.1% for 0 NFTs. Meaning the more NFTs the wallet holds the higher chance to get the Quartz trait.

<br>

#### mat_4_pyrite

Determines if the part has the Pyrite trait, by default the chance is 0. 

For1% of the crystal parts, 1% of crystal IDs, and 1% of wallet IDs the chance is increased by 99.95% - representing “pure” luck and lucky crystals and wallets. An additional chance is added based on the amount of ETH the wallet holds - starting from 2% for 15 ETH to 0.1% for 0 ETH. Meaning the more ETH the wallet holds the higher chance to get the Pyrite trait.

<br>

#### collab_collections

If the wallet holds any NFTs from the 7 supported collab-collections there is a 33% chance to get a special collab trait. If the wallet has NFTs from more than one supported collection the collab trait is selected randomly between the present collections. Meaning if the wallet has only one supported collection there is a 33% chance to get this collab trait. If the wallet has NFTs from 2 collections there is a 33/2=16.5% chance of getting each of two, etc.

<br>

#### dead

If the crystal was sold below the current floor price there is a chance of getting Dead trait - from 0% for selling 15% lower than the floor price to 99% for selling 85% lower than the floor.

<br><br>

## Raw output data for each generated part of the crystal

<br>

**Mat_type** - the material of the part

**Pal_group** - the color group 

**Shade_A_bright** - the flag for Bright primary color

**Shade_B_dark** - the flag for Dark primary color

**Type** - the type of the crystal part

**Type_Special** - the flag of special types of the crystal part

<br><br>

## How the traits are generated out of the raw output

<br>

Each crystal part has a trait from the Shape group. Some parts have additional traits from the Material group. All parts are treated equally independently of the order they are created. So the algorithm generates all the necessary traits for each part and then counts the number of appearances of the same trait within the crystal and that number is written into the metadata.

For each crystal part, the Shape group trait is defined by the Type attribute:
* 0 - Spiky 
* 1 - Cluster
* 2 - Fractal
* 3 - Coral
* 4 - Coral
* 5 - Coral
* 6 - Ksoid
* 7 - BAYC
* 8 - Meebit
* 9 - Omnimorph
* 10 - 89 Seconds
* 11 - Art Block
* 12 - Cryptopunk
* 13 - Dead

<br>

For each crystal part, the Material group trait is defined by the following algorithm:

1. If Type_special equals 1, then the Material is defined by the Shape and there is no need to output the material trait 

2. If Type_special equals 0

  2.1. If Mat_type equals 0, then the Material is defined by the Pal_type
  * 0 - Red 
  * 1 - Yellow
  * 2 - Green
  * 3 - Blue
  * 4 - Violet
  * 5 - Neutral

  2.2. If Mat_type equals 0 and Shade_A_bright equals 1, then the Material is Bright

  2.3. If Mat_type equals 0 and Shade_A_dark equals 1, then the Material is Dark

  2.4. If Mat_type is greater than 0 it defines the Material according to the list:
  * 1 - Obsidian
  * 2 - Gold
  * 3 - Quartz
  * 4 - Pyrite

<br>

For example, if the crystal consists of 4 parts: 2 of which are Red, 1 is Green, 3 are Fractal type and 1 is BAYC collab, the resulting traits will be - Generation: 4, Red: 2, Green: 1, Fractal: 3, BAYC: 1
