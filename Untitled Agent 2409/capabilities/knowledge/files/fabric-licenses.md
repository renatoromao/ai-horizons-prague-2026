# Name: Fabric Licenses

<!-- Microsoft Fabric licensing and capacity SKU structure (conceptual: what tiers exist and what they're for, not current prices) -->

## What this covers

Microsoft Fabric access is governed by two layers that work together: a **tenant**-level
Microsoft Entra identity boundary, and one or more **capacities** inside that tenant. A capacity
is a dedicated resource pool that provides the compute power for Fabric workloads. Workspaces —
the containers that hold Fabric items (reports, lakehouses, warehouses, notebooks, pipelines, and
so on) — are assigned to a capacity, and every Fabric user also gets a personal "My workspace."

## Capacity SKU families (structure, not pricing)

Fabric capacities are sold as a graduated family of tiers, generally referred to as **F SKUs**,
purchased through Azure with consumption-based or reserved billing options. A separate,
being-retired family of **Power BI Premium (P) SKUs** also supports Fabric when enabled on top of
an existing Power BI Premium subscription; Microsoft's current guidance is that new and existing
customers should move to F SKUs going forward. A smaller **A/EM SKU** family exists specifically
for Power BI embedded-analytics scenarios and does not, by itself, support non–Power BI Fabric
items.

Each tier in the F-SKU family provides progressively more compute power (measured in Fabric
"Capacity Units"), and the tier you choose determines both raw throughput and certain feature
gates — for example, whether users with only a free per-user license can view Power BI content in
that capacity's workspaces without an additional per-user license. The exact tier names, their
capacity-unit values, and their v-core equivalents change over time and are not repeated here —
see the official page below for the current table.

## Per-user license types (conceptual)

Independent of the capacity tier, individual users need a per-user license to work in Fabric:

- A **Free** license is granted automatically on first sign-in (when the tenant has Fabric
  enabled) and covers creating/sharing non–Power BI Fabric items on an F or Trial capacity.
- A **Pro** license is required to create and share Power BI items (reports, dashboards, etc.)
  outside a personal workspace, and to view Power BI content on smaller capacity tiers.
- A **Premium Per User (PPU)** license gives an individual most Power BI Premium features without
  the organization buying a full capacity — it is a per-user feature entitlement, not a capacity,
  so it does not by itself enable non–Power BI Fabric workloads.

## Trials and purchase channels

A time-limited Fabric trial capacity exists for evaluation. Production capacities can be
purchased directly through Azure or through a Cloud Solution Provider (CSP).

## What this source does not do

This snapshot never states specific SKU capacity-unit values, v-core counts, or prices. For the
current SKU table, tier names, capacity units, and pricing, the agent must retrieve them from —
and cite — the official page.

For current prices/limits, see: https://learn.microsoft.com/en-us/fabric/enterprise/licenses
