# Pricing Consistency Analysis (Power BI)

## Overview

This project demonstrates how to analyse and diagnose pricing inconsistencies using Power BI.

The approach is based on real-world ERP (Infor M3) pricing structures, adapted into a safe, shareable format.

The focus is not on revenue or margin, but on pricing behaviour and consistency:
- Are prices applied consistently?
- Where do inconsistencies occur?
- Are they driven by discounting or pricing logic?

---

## Real-World Context

The underlying logic and structure of this dataset originate from actual client-facing work on Infor M3.

To preserve confidentiality:
- Customer names have been synthesised
- Product names have been generalised
- No sensitive commercial data is included

However, the pricing mechanics remain realistic:
- Price lists  
- Customer-specific pricing  
- Layered discount structures  

---

## Problem

In ERP systems (e.g. Infor M3), pricing is typically governed by:
- Price lists  
- Customer agreements  
- Discount rules  

While these rules exist, there is often no clear visibility into the resulting prices.

This project answers:

> Is our pricing structure consistent, and where does it look wrong?

---

## Data Extraction (SQL / Infor M3)

The dataset used in this project was derived from custom SQL queries written against an Infor M3 environment.

The goal of the extraction was to reconstruct actual pricing outcomes, not just master data, by combining:

- Price list structures  
- Customer assignments  
- Item master data  
- Discount layers  

This required translating ERP pricing logic into a flattened dataset at:

Customer × Product level

---

### Approach

The SQL logic (simplified for this project) follows:

- Join customer master to assigned price lists  
- Join item master and pricing tables  
- Apply discount structures  
- Derive final effective price per customer/product  

---

### Example (simplified)

```sql
SELECT
    c.customer_no,
    c.customer_name,
    p.price_list,
    i.item,
    i.brand,
    i.segment,
    p.base_price,
    d.discount_rate,
    (p.base_price * (1 - d.discount_rate)) AS final_price
FROM customers c
JOIN price_list_assignments p
    ON c.customer_no = p.customer_no
JOIN items i
    ON p.item = i.item
LEFT JOIN discounts d
    ON c.customer_no = d.customer_no
    AND p.item = d.item;
```
### Closing Note

The SQL layer is where ERP pricing logic is translated into something analysable.

By reconstructing pricing outcomes at a customer × product level, this approach ensures that the dataset reflects how pricing is actually applied, not just how it is configured.

This provides a reliable foundation for identifying inconsistencies and understanding their underlying causes.

It effectively bridges:

ERP configuration → analytical model → pricing insight

---

## Cloud Architecture Consideration (Infor M3 Data Lake)

In Infor M3 Cloud (multi-tenant environments), direct SQL access to application tables is not available.

Instead, data is exposed through the Infor Data Lake, where relevant tables are published and made available for downstream consumption.

This changes the extraction approach from:

ERP tables → direct SQL queries

to:

ERP → Data Lake → query layer → Power BI

### Adapted Approach

To support this model:

 - Required M3 datasets are published to the Data Lake
 - Data is accessed via a query layer (e.g. SQL endpoint, Synapse, or equivalent)
 - The same transformation logic is applied to reconstruct:
   Customer × Product pricing outcomes
   Discount structures
   Final effective pricing

## Key Point

While the access method changes, the analytical objective remains the same:

Reconstruct pricing outcomes at a granular level to enable consistency analysis.
