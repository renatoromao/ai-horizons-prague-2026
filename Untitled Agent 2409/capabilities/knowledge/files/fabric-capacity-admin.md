# Name: Fabric Capacity Admin

<!-- Microsoft Fabric capacity administration: how to configure, resize, and monitor a capacity (not licensing/SKU selection) -->

## What this covers

This snapshot describes the administrative surface for managing an existing Microsoft Fabric
capacity from the Fabric Admin portal's Capacity settings page. It is about **operating** a
capacity you already have — not about choosing which SKU or license type to buy (see the Fabric
Licenses source for that).

## Capacity types visible to an admin

The Capacity settings page lists capacities grouped by type: Power BI Premium (P-SKU, being
retired in favor of Fabric capacity), Power BI Embedded (A/EM-SKU), Trial, and Fabric capacity
(F-SKU). Each type has its own creation flow, typically routed through the Azure portal for
Fabric (F-SKU) and Power BI Embedded (A-SKU) capacities, and through the Fabric admin portal for
Power BI Premium and EM-SKU capacities.

## Core admin tasks

- **Create** a new capacity, choosing a name, admins, region, and size during setup.
- **Rename** a capacity (not supported for Fabric F-SKU capacities once created).
- **Add or remove capacity admins**, who can change settings, add contributors, and manage
  workspace assignment. A narrower **contributor** role can assign workspaces to a capacity
  without full administrative control.
- **Resize** a capacity to a different tier.
- **Delete** a capacity — non–Power BI items in workspaces on a deleted capacity are soft-deleted
  and can be restored within a short grace window if the workspace is reassigned to another
  capacity in the same region.
- **Autoscale** is available for Power BI Premium and Power BI Embedded capacities but not for
  Trial or Fabric (F-SKU) capacities.

## Capacity-level settings

Beyond lifecycle management, each capacity exposes settings such as: designating the capacity as
a "Copilot in Fabric" capacity, configuring contributor and admin permissions, enabling disaster
recovery, choosing the preferred capacity for "My workspace," configuring Power BI workload
behavior (semantic models, paginated reports, AI features), and adjusting spark pool sizing for
data engineering workloads. Fabric admins can also delegate specific tenant-level settings down
to individual capacity admins, scoped to that capacity only.

## What this source does not do

This snapshot does not state which SKU size to choose, its price, or its capacity-unit value —
that is a licensing/sizing question, covered by the Fabric Licenses source instead. It also does
not restate exact memory percentages or other tunable numeric defaults; for those, the admin
should consult the live page.

For current prices/limits, see: https://learn.microsoft.com/en-us/fabric/admin/capacity-settings
