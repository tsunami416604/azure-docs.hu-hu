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
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900988"
---
Az erőforrások felcímkézése után megtekintheti az adott címkével ellátott erőforrások költségét. A legutóbbi használati adatok megjelenítése eltarthat egy darabig a költségelemzés számára, tehát előfordulhat, hogy rögtön nem látja a költségeket. Amikor a költségek már elérhetők, megtekintheti az előfizetésében lévő erőforráscsoportok erőforrásainak költségét. A költségek megtekintéséhez a felhasználóknak [előfizetéses szintű hozzáféréssel](../articles/cost-management-billing/manage/manage-billing-access.md) kell rendelkezniük a számlázási adatokhoz.

Ha címke szerint szeretné megtekinteni a költségeket a portálon, válassza ki az előfizetését, és válassza a **Költségelemzés** lehetőséget.

![Költségelemzés](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Ezután szűrjön címkeérték szerint, és válassza az **Alkalmaz** lehetőséget.

![Költség megtekintése címke szerint](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Az [Azure használati API áttekintése](../articles/cost-management-billing/manage/consumption-api-overview.md) segítségével programozott módon tekintheti meg a költségeket.
