# SanchoNet Always YES DRep
This script script DRep is registered on SanchoNet and **always** votes "YES". Was created by the one and only [Thomas Velekoop](https://github.com/perturbing) here: https://github.com/perturbing/always-yes-drep. 

The SanchoNet AlwaysYesDRep was just parameterized with a utxo I controlled so that I could register it on SanchoNet. It is identified by the scriptHash `1ffa2ae5f54e88a2e6a29642936aceebdd3aea948d70ace645912440`

## How to vote yes with it

To vote you can use
```bash
cardano-cli conway governance vote create \
    --yes \
    --governance-action-tx-id "yourGovActionHashHere" \
    --governance-action-index "yourGovActionIndexHere" \
    --drep-script-hash 1ffa2ae5f54e88a2e6a29642936aceebdd3aea948d70ace645912440 \
    --out-file myGovAction.vote
```
and submit it to the chain by witnessing the drep script via the following `build-raw` command:

```bash
UTXO_VALUE=$(cardano-cli conway query utxo --address $(cat payment.addr) --output-json | jq '.[keys[0]].value.lovelace')
FEE=1000000
CHANGE=$(($UTXO_VALUE - $FEE))

cardano-cli query protocol-parameters --out-file pparams.json

cardano-cli conway transaction build-raw \
 --tx-in "$(cardano-cli query utxo --address $(cat payment.addr) --output-json --testnet-magic 4| jq -r 'keys[0]')" \
 --tx-in-collateral "$(cardano-cli query utxo --address $(cat payment.addr) --output-json --testnet-magic 4 | jq -r 'keys[0]')" \
 --vote-file myGovAction.vote \
 --vote-script-file alwaysVoteYesDrep.plutus \
 --vote-redeemer-value {} \
 --vote-execution-units "(200000000, 600000)" \
 --tx-out $(cat payment.addr)+$CHANGE \
 --fee $FEE \
 --protocol-params-file pparams.json \
 --out-file tx.raw

cardano-cli transaction sign --testnet-magic 4 --signing-key-file payment.skey --tx-body-file tx --out-file tx.signed

cardano-cli transaction submit --testnet-magic 4 --tx-file tx.signed
```
And to convince you that the script can never vote no, try voting not with it :)



