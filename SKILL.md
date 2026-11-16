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

| Tool                    | Sign-in                          | Use it to                                                                                                                                                                                                    |
| ----------------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `search_grocery_prices` | no                               | Find a product and see every store's price, offer, loyalty card and unit price, cheapest first. Filter by brand, store, own brand, on offer, category; sort by unit price or saving. Page with `nextCursor`. |
| `price_shopping_list`   | no                               | Price up to 40 plain-words lines at one store, the cheapest store, or split across stores. Names missing lines and close candidates for unmatched ones.                                                      |
| `find_cheaper_swaps`    | no                               | For a list at one store, find each item's cheapest acceptable swap, labelled same product, equivalent or different, with savings.                                                                            |
| `check_price_history`   | no (dates over 90 days: sign-in) | Compare today's price with the 60-day low and high, a price on a past date, or a receipt price (`paidPence`).                                                                                                |
| `get_nutrition`         | no                               | Per-100g macros and Nutri-Score for one product, credited to Open Food Facts.                                                                                                                                |
| `create_list_page`      | no                               | Make a shareable link that prices a list at every store.                                                                                                                                                     |
| `compare_basket`        | yes                              | Total per store with like-for-like swaps, the cheapest single store and the best two or three store split.                                                                                                   |
| `save_shopping_list`    | yes                              | Add products to the user's saved list.                                                                                                                                                                       |
| `list_saved_items`      | yes                              | Read the user's saved list with prices.                                                                                                                                                                      |
| `remove_saved_items`    | yes                              | Remove products from the list or delete them. Destructive: confirm with the user first.                                                                                                                      |
| `create_deal_alert`     | yes                              | Alert the user when a brand or keyword goes on offer.                                                                                                                                                        |

## Workflows

- **Where is X cheapest?** Call `search_grocery_prices` with the product name and brand. If several products match, show the top few with their unit prices and ask which is meant.
- **Is this a good price?** Find the product, then `check_price_history`. Report the verdict against the 60-day low and high.
- **Cheapest store for a shop.** Call `price_shopping_list` with the user's lines. `price_shopping_list` already names the cheapest store and the best split, with no sign-in. Use `compare_basket` only for a signed-in user, or when they want like-for-like swaps or delivery and minimum-order notes; it takes `productId`s from `search_grocery_prices`. Report the total per store, what each store is missing, and the saving.
- **Save at my usual store.** `find_cheaper_swaps` at that store. Label each swap so the user can decline non-identical ones.
- **Share a list.** `create_list_page` and give the link.

## Limits

Calls are rate limited per caller and per day. If a tool says a limit is reached, tell the user in plain words and stop retrying.

Site: https://mygrocerypal.co.uk
