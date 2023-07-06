# mainnet

Yes, it is the Titan!

## Quick Information

### rizon tag flow

-  [v0.2.8](https://github.com/rizon-world/rizon/tree/v0.2.8) -> [v0.3.0](https://github.com/rizon-world/rizon/tree/v0.3.0) ->  [v0.4.0](https://github.com/rizon-world/rizon/tree/v0.4.0) ->  [v0.5.1](https://github.com/rizon-world/rizon/tree/v0.5.1)

### chain-id

- titan-1

### genesis file

- [genesis.json](./genesis.json)

### genesis file checksum

```sh
$ jq -S -c -M '' ~/.rizon/config/genesis.json | shasum -a 256
5f00af49e86f5388203b8681f4482673e96acf028a449c0894aa08b69ef58bcb  -
```

### minimum gas prices

- 0.0001 uatolo
- `$ sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "0.0001uatolo"/g' ~/.rizon/config/app.toml`

### official peers

- 83c9cdc2db2b4eff4acc9cd7d664ad5ae6191080@seed-1.mainnet.rizon.world:26656
- ae1476777536e2be26507c4fbcf86b67540adb64@seed-2.mainnet.rizon.world:26656
- 8abf316257a264dc8744dee6be4981cfbbcaf4e4@seed-3.mainnet.rizon.world:26656

### community peers

- Fill your peers here if you want to provide yours to public.

## Docs

https://docs.rizon.world
