---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7a0e7e19d53d8566b859004c363ec5bbc71cb733
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
Mielőtt létrehozna elemeket, tekintsük át, a hatókör fogalmát. Azure biztosít négy szintű felügyeleti: felügyeleti csoportok, előfizetés, erőforráscsoport és erőforrások. [Felügyeleti csoportok](../articles/billing/billing-enterprise-mgmt-group-overview.md) előzetes kiadás szerepelnek. Az alábbi ábrán egy példa a rétegek.

![Hatókör](./media/resource-manager-governance-scope/scope-levels.png)

Alkalmazza hatókör szinten, a felügyeleti beállításokat. A kiválasztott szintre határozza meg, hogy a beállítás lesz alkalmazva. Alacsonyabb szinteken beállítások öröklése a magasabb szinteken. Az előfizetés egy beállítást alkalmazza, amikor adott beállítást csoportok és a erőforrást az előfizetésében alkalmazza. A beállítás a az erőforráscsoportot, a beállítás, alkalmazza az erőforráscsoport és az ahhoz tartozó összes erőforrást. Egy másik erőforráscsoportban azonban nem rendelkezik beállítás.

Általában érdemes magasabb szinten kritikus beállítások és az alacsonyabb szinteken projektre vonatkozó követelmények vonatkoznak. Például előfordulhat, hogy kívánt győződjön meg arról, hogy a szervezet összes erőforrást telepített egyes régiókban. Ezt a követelményt megvalósítása érdekében az előfizetést, amely meghatározza az engedélyezett helyek házirend vonatkozik. A munkahely más felhasználóinak új erőforráscsoportok és erőforrások, az engedélyezett helyek automatikusan lépnek érvénybe. Egy adott projekt érdemes győződjön meg arról, hogy a címke, amely azonosítja a projekt jelölt összes erőforrást. Az erőforráscsoport a projekt, amely képes kikényszeríteni a címke a házirend vonatkozik. Mivel a felhasználók új erőforrások hozzáadása az erőforráscsoportot, a rendszer automatikusan hozzáadja a címke. Azonban felhasználókat adhat hozzá, amelyek nem kapcsolódnak a projekt erőforráscsoportok, és a címke nem lép életbe.
