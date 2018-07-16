---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7843410043b726526380b2a916d96f414a2decda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740569"
---
Az erőforrások felcímkézése után megtekintheti az adott címkével ellátott erőforrások költségét. A legutóbbi használati adatok megjelenítése eltarthat egy darabig a költségelemzés számára, tehát előfordulhat, hogy rögtön nem látja a költségeket. Amikor a költségek már elérhetők, megtekintheti az előfizetésében lévő erőforráscsoportok erőforrásainak költségét. A költségek megtekintéséhez a felhasználóknak [előfizetéses szintű hozzáféréssel](../articles/billing/billing-manage-access.md) kell rendelkezniük a számlázási adatokhoz.

Ha címke szerint szeretné megtekinteni a költségeket a portálon, válassza ki az előfizetését, és válassza a **Költségelemzés** lehetőséget.

![Költségelemzés](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Ezután szűrjön címkeérték szerint, és válassza az **Alkalmaz** lehetőséget.

![Költség megtekintése címke szerint](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Használhatja az [Azure-beli számlázási API-kat](../articles/billing/billing-usage-rate-card-overview.md) is a költségek programozott módon való megtekintéséhez.
