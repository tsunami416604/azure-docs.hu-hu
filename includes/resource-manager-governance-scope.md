---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740580"
---
Mielőtt létrehoznánk bármit is, tekintsük át a hatókör fogalmát. Az Azure négy felügyeleti szintet biztosít: felügyeleti csoportok, előfizetés, erőforráscsoport és erőforrás. A [felügyeleti csoportok](../articles/billing/billing-enterprise-mgmt-group-overview.md) szintje egyelőre előzetes verzióban érhető el. Az alábbi ábra ezekre a rétegekre mutat egy példát.

![Hatókör](./media/resource-manager-governance-scope/scope-levels.png)

Felügyeleti beállításokat a hatókörszintek bármelyikéhez megadhat. A kiválasztott szint határozza meg, milyen széles körben lesz alkalmazva a beállítás. Az alacsonyabb szintek öröklik a magasabb szintek beállításait. Amikor alkalmaz egy beállítást az előfizetésre, a beállítást a rendszer alkalmazza az előfizetésben lévő összes erőforráscsoportra és erőforrásra. Amikor alkalmaz egy beállítást az erőforráscsoportra, az a beállítás érvényben lesz az erőforráscsoporton és annak összes erőforrásán. Más erőforráscsoportra azonban nem érvényes ez a beállítás.

Általában logikus döntés a kritikus fontosságú beállításokat magasabb szinten alkalmazni, a projektspecifikus feltételeket pedig alacsonyabb szinten. Előfordulhat például, hogy biztosítani szeretné, hogy a szervezet összes erőforrása bizonyos régiókban legyen üzembe helyezve. A feltétel teljesítéséhez alkalmazzon egy, a megengedett helyeket megadó szabályzatot az előfizetésre. Ahogy a szervezet más felhasználói új erőforráscsoportokat és erőforrásokat vesznek fel, a rendszer automatikusan kikényszeríti a megengedett helyeket. 
