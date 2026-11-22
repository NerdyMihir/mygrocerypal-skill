---
name: mygrocerypal
description: Price UK groceries with MyGroceryPal's remote MCP server. Use when a user asks where a UK supermarket product is cheapest, whether a price is a good deal, which store is cheapest for a shopping list, or wants a grocery price alert. UK only.
---

# MyGroceryPal

MyGroceryPal compares daily shelf prices at 13 UK stores: Tesco, Sainsbury's, ASDA, Aldi, Morrisons, Co-op, Waitrose, Iceland, B&M, Boots, Superdrug, Savers and Ocado (including M&S). It is free.

## Connect

Remote MCP server, streamable HTTP, no API key:

```
https://mygrocerypal.co.uk/api/mcp
```

MCP Registry name: `uk.co.mygrocerypal/grocery-prices`. Setup for Claude and other clients: https://mygrocerypal.co.uk/ai-assistants

Searching, pricing a list, price history within the last 90 days, nutrition and share links work without an account. Tools with Sign-in "yes" below ask the user to connect a free MyGroceryPal account through OAuth the first time; tell the user that is expected.

There is no public REST API, no API key and no bulk or data export. Use the MCP tools, one question at a time, and do not scrape mygrocerypal.co.uk.

## Rules for answering

- **UK only.** Prices are in pounds, for UK stores. For a non-UK question, say MyGroceryPal does not cover it.
- **Loyalty prices count as the price.** A Clubcard, Nectar or similar card price is the price MyGroceryPal shows and the price you should quote. Say which card it needs when the tool returns one.
- **One national price per store.** Each store has a single price, the logged-out view of that store's site. A shopper's local, postcode or delivery-slot price can differ slightly; that is expected, not an error. Do not promise the price at a specific branch.
- **Say the date.** Prices refresh daily. Quote prices as "today" or with the date a tool returns, never as permanent.
- **Name what is missing.** If a store has no price for a line, say so; do not guess it.
- **Facts only.** Quote prices, offers, unit prices and links the tools return. Do not invent product images or descriptions.
- **Link to the store.** Each price has a `storeUrl` on mygrocerypal.co.uk that redirects to the store's product page. Offer it when the user wants to buy. `productUrl` is the product's full comparison page on mygrocerypal.co.uk.

## Tools

| Tool                    | Sign-in                          | Use it to                                                                                                                                                                                                                                                        |
| ----------------------- | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `search_grocery_prices` | no                               | Find a product and see every store's price, offer, loyalty card and unit price, cheapest first, with the app's filters (brand, store, own brand, on offer, category, diet) and sorts (price, unit price, saving, dearest). Page with `nextCursor`. Covers swaps. |
| `price_shopping_list`   | no                               | Price up to 40 plain-words lines: the cheapest store that stocks every line, one store, or split across stores. A store missing lines says "N of M items" and is not ranked with full ones. Returns the shared-list link.                                        |
| `check_price_history`   | no (dates over 90 days: sign-in) | Compare today's price with the 60-day low and high, a price on a past date, or a receipt price (`paidPence`).                                                                                                                                                    |
| `get_nutrition`         | no                               | Per-100g macros and Nutri-Score for one product, credited to Open Food Facts.                                                                                                                                                                                    |
| `add_to_list`           | yes                              | Add products to the user's saved list.                                                                                                                                                                                                                           |
| `get_list`              | yes                              | Read the user's saved list with prices.                                                                                                                                                                                                                          |
| `remove_from_list`      | yes                              | Remove products from the list or delete them. Destructive: confirm with the user first.                                                                                                                                                                          |
| `create_price_alert`    | yes                              | Alert the user when a brand or keyword goes on offer.                                                                                                                                                                                                            |
| `report_problem`        | no                               | Tell MyGroceryPal about a missing product or a wrong price, when the user asks you to report it.                                                                                                                                                                 |

## Workflows

- **Where is X cheapest?** Call `search_grocery_prices` with the product name and brand. If several products match, show the top few with their unit prices and ask which is meant.
- **Is this a good price?** Find the product, then `check_price_history`. Report the verdict against the 60-day low and high.
- **Cheapest store for a shop.** Call `price_shopping_list` with the user's lines, once. It names the cheapest store that stocks every line and the best split. Report the total per store, what each store is missing, and the saving. A store with "N of M items" is not comparable with a full one.
- **Save at my usual store.** `search_grocery_prices` with `stores` set to that store, sorted by `price` or `unit_price`, with `ownBrandOnly` for own brands. Say when a result is a different product so the user can decline it.
- **Share a list.** `price_shopping_list` and give its `listPageUrl`.

## Limits

Calls are rate limited per caller and per day. If a tool says a limit is reached, tell the user in plain words and stop retrying.

Site: https://mygrocerypal.co.uk
