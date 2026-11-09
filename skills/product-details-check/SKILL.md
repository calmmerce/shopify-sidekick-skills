---
name: product-details-check
description: >-
  Product details check reviews products selected by product name, tag, collection,
  or catalog, including every associated variant. Use when the merchant invokes
  product details check or requests a comprehensive product-field review covering
  product type, pricing, theme template, SEO, and variant details. Produces a
  read-only review with attention flags; does not edit products automatically.
---

# Product details check

Prepare a read-only, comprehensive review of the selected products and every variant. Do not run a report merely because the merchant asks to create or edit this skill. Reviewing does not authorize changes.

## Resolve the selection

- Accept a product name, exact tag, collection, or catalog, with an explicit selector type when supplied. If none is supplied, ask for one of these selectors.
- For product-name discovery, search PRODUCT and PRODUCT_VARIANT in parallel with the same query. Deduplicate parent products and retrieve every variant of each selected product, not just matching variants.
- For tags, use a verified product filter and verify exact tag membership. For collections, resolve the collection and enumerate its products. For catalogs, resolve the actual catalog and verify its associated product membership using supported read tools. Read the markets skill when catalog configuration or market-specific pricing is needed. Do not equate a catalog with a collection or treat all store products as catalog members.
- Use search_shop_resource for discovery where supported. Use generate_graphql_operation for verified read-only query shapes and execute them through exec_js with the required read scopes. Never invent fields, IDs, catalog membership, or query capabilities. Consult documentation for Shopify behavior or field interpretations not established by tool evidence.
- A capped search is discovery, not exhaustive membership. Paginate every relevant membership, products, variants, media, collections, and metafields connection used in the review to completion. Deduplicate products appearing through multiple matches. Ask one focused question when materially different plausible matches cannot be resolved from context.
- If access, query support, or scale prevents completion, clearly identify the reviewed scope and remaining or unknown coverage. Never label a partial report complete.

## Fields to review

Retrieve the following where supported and accessible. Distinguish empty values from unavailable or unauthorized fields. Do not silently omit a requested field.

Product identity and organization:
- Title, handle, description, status, vendor, product type, taxonomy/category, tags, and collection membership.
- Assigned theme template suffix. Report an empty/default assignment as default; do not infer whether a custom template exists or is rendered correctly without checking the relevant theme evidence. Read editing-themes before inspecting theme files.
- Creation/update timestamps and storefront/admin links when actually returned by tools.

Merchandising and search:
- SEO title and description as stored, separately from the product title and description. Do not silently substitute fallback text or claim a search engine will display the stored text verbatim.
- Media count and accessible media metadata, including alt text. If judging what an image shows or its visual quality, view it first using the required image tool.
- Sales-channel publication/availability information when accessible, explicitly identifying the channel and keeping publication distinct from product status.
- Product options, option values, and relevant product metafields. Retrieve complete accessible metafield lists, but summarize lengthy values with their names/types and an explicit truncation note rather than flooding the review. Do not expose credentials or sensitive customer data.

Every variant:
- Variant title, option values, SKU, barcode, base price, compare-at price, cost when accessible, and currency.
- Inventory tracking, total available quantity when supported, and out-of-stock selling policy. Do not mistake untracked or inaccessible inventory for zero. Location-level inventory is only required if requested.
- Taxable flag, physical/shipping requirement, weight and unit, assigned media, and accessible variant metafields.
- For a catalog selection, retrieve applicable catalog/context pricing where supported, label it separately from base store prices, and identify the catalog and currency. Distinguish fixed prices from adjustments or other pricing rules only when the evidence supports it. If effective prices cannot be verified, say so instead of calculating assumed prices.

Use additional supported product fields when pertinent to the merchant's stated review, without broadening into unrelated operational or customer data. Do not calculate margins unless requested.

## Review output

Read building-artifacts before choosing a supported review artifact. Do not invent interactive controls or unsupported UI. If no suitable artifact is authorized, provide a clearly grouped conversational review; tables are appropriate when the merchant requests them. For a large scope, retain all product and variant coverage using the skill's supported presentation/paging approach, not an arbitrary first batch. Do not create a download unless requested or authorized by a system skill.

Lead with the selection, product count, variant count, and coverage. Organize the review into product details, variant details, template/SEO, and attention items so the merchant can inspect the fields rather than only seeing a score. Keep complete descriptions and long values accessible through supported detail presentation where possible; otherwise label excerpts clearly and explain how to request a specific full value.

Flag evidence-based review candidates, including missing product type/category, empty descriptions or SEO fields, missing SKUs/barcodes/alt text, duplicate nonempty SKUs within the reviewed scope, zero or negative prices, and compare-at prices not greater than the selling price. Treat missing optional fields as review candidates, not proof of error. Preserve intentional free products and other possible business choices in the wording. Do not claim SEO ranking improvements, legal compliance, invalid template assignments, or publication problems without appropriate evidence. Use seo-product only if the merchant requests a search-focused audit or fixes, and legal for regulated questions.

End with the highest-priority observed gaps and offer to prepare specific corrections for review. Make no changes during this report. If corrections are requested, follow store-changes and the current page/run rules, preserving all unrelated fields.
