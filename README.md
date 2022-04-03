# 8bidou-metadata

TZIP-21 Compatible IPFS Metadata Template for 8Bidou

## What is this for?

[8bidou](https://www.8bidou.com/) is a pixel art marketplace that stores its data on-chain instead of on ipfs.

The minter provides an option to add additional metadata, but at this time the url is pointing to localhost which is confusing and you end up with very technical looking metadata with limited rich media support in wallets, marketplaces, and indexers.

You can add your own TZIP-21 metadata link which will add all the bells and whistles needed for maximum compatibility across third party marketplaces and wallets.

## How do I use it?

The instructions below are for working in the terminal. You can just follow the same instructions in your ipfs extension, or even just manually upload the files to pinate - as long as you get the hash and the file is remotely pinned you will be ok.

It's a 2 step process, first you add your image, then you modify and add the metadata file.

This must all be done before you mint, and both the files must be resolving on ipfs before you perform the minting action on 8bidou.

### Prepare the Image File

1. Upload your 8x8 PNG image to ipfs and get its hash

```
ipfs add myfilename.png
```

This will give you a long hash starting with the letter Q

2. Pin the file locally

```
ipfs pin add QmTHEHASHFROMSTEP1
```

3. Pin the file on a pinning service like [Pinata](https://www.pinata.cloud)

```
ipfs pin remote add --service='Your Service Name' --background QmTHEHASHFROMSTEP1
```

4. Make sure the file is available *before* minting

Visit https://ipfs.io/ipfs/QmTHEHASHFROMSTEP1 in an incognito window (or a browser without the ipfs extension) and make sure the file loads. You should see your 8x8 image at the URL.

Now that you have your image hash you can move on to the next step and make your metadata file.

### Prepare the Metadata File

1. Make a copy the `template.json` file

```
cp template.json my-token.json
```

Open the file in a text editor and make the following changes:

1. Replace all 4 instances of `ipfs://IMAGEIPFSADDRESS` with `ipfs://QmTHEHASHFROMSTEP1`

- artifactUri
- displayUri
- thumbnailUri
- the uri entry in the 'formats' array

**NB** do not use the ipfs.io address! It must be `ipfs://` followed by the hash only

2. Replace all 4 instances of `tz1xYOURFULLTEZOSADDRESS` with your full tezos address (no domains, must be full tz address)

- creators
- minter
- publishers
- shares

3. DO NOT TOUCH THE ROYALTY VALUE IT IS SET TO 10% AND MATCHES 8BIDOU

The values required for 10% are:

- decimals: 6
- value: 100000

(the root decimals value is always 0 and is different to the shares one)

4. Add attributes for your token

There are 2 placeholders in the template, and you can add more by expanding the attributes array with more entries.

5. Update the 'rights' value with your name

Or just use the default `(c) 2022`. For maximum compatibility do not use the unicode copyright symbol, rather use the 3 characters `(c)`

6. Add a `name` and `description` for the token

NB: Use the same values you enter on 8bidou!

7. The identifier and symbol are set to `8BIT`

I'm not sure what the standard 8bidou symbol is, you can change this. Maximum 5 characters, all uppercase.

8. Set the type

The included template has the 8x8 type set, if you mint at a different size you should update the value to reflect the type you are minting.

9. Update the tags array, adding or removing as needed

No spaces, full words only, alphanumeric, underscore and dash allowed

10. Update format (only if minting bigger than 8x8 px)

If minting non-8x8 size then change the dimensions value to match the size of your artwork.

11. Do not touch anything else

The following **must remain default**

- decimals
- isBooleanAmount
- royalties
- shouldPreferSymbol
- burnable
- limit
- language

### Add the metadata file to ipfs

The same steps as before

```
ipfs add my-metadata-file.json
```

Then take that hash and pin it locally and on pinata

```
ipfs pin add QmTHEHASHOFYOURJSONFILE
ipfs pin remote add --service='Your Service Name' --background QmTHEHASHOFYOURJSONFILE
```

### Mint it!

Once you can ooen the ipfs.io/ipfs/xyz link and see the contents you can use the following value inside the 'metadata' field when minting (instead of the localhost url)

```
ipfs://QmTHEHASHOFYOURJSONFILE
```

Always make sure the name and description match!

## ADVANCED MODE

### Collaboration with Royalty Splitting

It's possible to do collaboration details here and sites like objktcom and rarible will respect the split royalties you define.

The total value **must** add up to `100000`, so if it is a collab between 2 people the values must be 50000 each (5% each for a total of 10%). You **cannot** go over 10%

If you do this you **must** also add the collaborators address to the `creators` and `publishers` arrays. Do not add them to the `minter` field, leave this as your address.
