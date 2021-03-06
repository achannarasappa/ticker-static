# ticker-static

Repository that contains static content for [ticker](https://github.com/achannarasappa/ticker)

## Content

### ticker symbols

`symbols.json` is a static config file that contains convenience symbols by data source. This is used to define shortcut symbols that exist only in ticker but reference a symbol in an external data source. e.g. `bitcoin` as `BTC.X`

#### CoinGecko

Coingecko convenience symbols were generated by retrieving the top 250 coins/tokens by market cap at the time of commit. Since the top 250 can change over time, it's important that the ticker convenience symbols are only added so as to not break users configuration files.

Generating the latest top symbols can be done with this command and merged with the existing file:

```sh
curl -X 'GET' \
  'https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=250&page=1&sparkline=false' \
  -H 'accept: application/json' \
  | jq -r '.[].symbol |= ascii_upcase| .|=sort_by(.id)|.|=unique_by(.symbol) |.[] | {"symbol-ticker": (.symbol + ".X"), "symbol-source": .id, source: "cg"}' | jq -r '[.[]] | @csv' > symbols.csv
```