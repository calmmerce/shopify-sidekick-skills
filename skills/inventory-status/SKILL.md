---
name: inventory-status
description: >-
  Checks inventory status for products selected by product name, tag, or collection,
  including every variant and warehouse location. Use when the merchant invokes
  inventory status or asks for this warehouse stock and scan-status report.
  Reports product and variant stock in tables with green for 10 or more,
  yellow for 5–9, red for 1–4, and explicit out-of-stock and unknown statuses.
---

# Inventory status

Produce a read-only inventory and warehouse receipt/scan check. Do not adjust stock, receive shipments, or change products.

## Resolve scope

- Accept a product name, exact tag, or collection name, optionally a warehouse location. If no selector was supplied, ask for a product name, tag, or collection.
- Honor explicit selector types. For product-name searches, search PRODUCT and PRODUCT_VARIANT in parallel with the same query, deduplicate parent products, and include every variant of each selected product, not just matching variants. For an exact tag, use an appropriate verified product filter and verify the tag matches exactly. For collections, resolve the collection and enumerate its current products.
- Use search_shop_resource for discovery and generate_graphql_operation for verified read-only query shapes, then execute through exec_js with the required read scopes. Paginate every relevant products, variants, inventory-level, and collection-products connection to completion. Do not treat a search result limit as exhaustive. If a selector has materially different plausible matches, ask one focused clarification rather than silently choosing an unrelated group.
- Resolve warehouse locations from the store's actual locations and any merchant-specified location. Do not assume every retail or fulfillment-service location is a warehouse. If no warehouse can be identified reliably, ask which location to check. If several clearly identified warehouses apply, show each separately and a clearly labeled combined total across those warehouses only.

## Inventory evidence

Retrieve product title, variant title and SKU, inventory tracking state, and each applicable location's available and on-hand quantities. Include incoming quantities when available to distinguish goods not yet received. Keep absent inventory levels, inactive stocking, untracked inventory, and inaccessible data distinct from observed zero quantities.

Use **available** units for stock flags, not on-hand totals, incoming stock, or permission to continue selling when out of stock. Available means sellable stock; on hand includes available, committed, and unavailable inventory. Incoming units do not count as in stock. Reference: https://help.shopify.com/en/manual/inventory-and-locations/fundamentals/inventory-states . Consult current documentation when explanations need verification.

Warehouse stock recorded in inventory is not proof of a physical barcode scan. Never infer scanned-in status from positive stock, a barcode's existence, or incoming quantities. Look for an accessible, documented warehouse receipt/scan source, such as a merchant-identified warehouse app, a verified scan/receipt record, or a known relevant metafield. Read any applicable app skill before using its tools. Do not invent field names, scan timestamps, or capabilities. A receipt record may confirm receipt but not a scan; label these distinctly. If no verifiable scan evidence is accessible, report **Scan not verified** and explain that inventory is recorded but scanning cannot be confirmed. Confirm scanning only for the items, location, and quantity actually supported by the source; an old or partial receipt does not prove every currently stocked unit was scanned. Missing evidence is not evidence that the item was never scanned.

## Tables and verdict

The merchant explicitly requests tables. Present a product-summary table plus variant detail tables (or an equivalent supported tabular artifact after reading building-artifacts). Include all variants; never hide an out-of-stock variant behind a large product total.

Use mutually exclusive thresholds on available quantity:
- 🟢: 10 or more.
- 🟡: 5–9.
- 🔴: 1–4.
- ⛔ Out of stock: 0 or less, preserving negative quantities in the report.
- Unknown: untracked, unavailable, or missing quantity evidence; no numeric stock flag.

Product summary columns: Product | Total warehouse available | Lowest variant available | Product flag | All variants in stock? | Warehouse scan status.
Variant detail columns: Product | Variant | SKU | Warehouse | Available | On hand | Incoming (if retrieved) | Stock flag | Scan/receipt evidence.

For multiple warehouses, show location-level detail and combined-per-variant totals without double counting. Base the product flag on the lowest combined available quantity of any variant across the selected warehouses, not the sum of every variant. If any variant has an unknown quantity that prevents a reliable verdict, use Unknown for the product flag and “Not confirmed” for all-variants status. Otherwise “All variants in stock” requires every variant to have available quantity greater than zero. Separate overall in-stock and scanned-in verdicts; only confirm both when complete evidence supports both.

Lead with counts of products checked, products with every variant in stock, and products needing attention. State the warehouse scope and that flags use available inventory. Note missing scan evidence and any partial retrieval clearly. On query/access failure, report the covered scope and unresolved records, never claim all items were checked. End with the most useful next action for any stock or evidence gaps. Do not run this report merely because the merchant is asking to create or edit this skill.
