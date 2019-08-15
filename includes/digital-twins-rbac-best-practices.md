---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: fa88bf7f8db9b4fea63429dbfd18f84ee84ccda1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012161"
---
A szerepköralapú hozzáférés-vezérlés egy öröklésre épülő biztonsági stratégia a hozzáférés, az engedélyek és a szerepkörök kezeléséhez. A leszármazott szerepkörök öröklik az engedélyeket a fölérendelt szerepköröktől. Az engedélyek a fölérendelt szerepkörből való öröklés nélkül is hozzárendelhetők. Emellett a szerepkörök szükség szerinti testreszabásához is hozzárendelhetők.

Előfordulhat például, hogy a hely rendszergazdájának globális hozzáférésre van szüksége az összes művelet futtatásához egy adott helyen. A hozzáférés magában foglalja az összes csomópontot a terület alatti vagy azon belül. Előfordulhat, hogy az eszköz telepítője csak *olvasási* és *frissítési* engedélyeket igényel az eszközök és érzékelők számára.

Minden esetben a szerepköröket a legalacsonyabb jogosultsági szint elve alapján kell megadni, *és nem lehet nagyobb, mint a* feladatok teljesítéséhez szükséges hozzáférés. Ezen elv alapján *csak*az identitást kell megadni:

* A feladatok elvégzéséhez szükséges hozzáférés mennyisége.
* Megfelelő szerepkör, amely a feladatának elvégzésére korlátozódik.

>[!IMPORTANT]
> Mindig kövesse a legalacsonyabb jogosultsági szint elvét.

Két további fontos szerepköralapú hozzáférés-vezérlési eljárás a következő lépésekhez:

> [!div class="checklist"]
> * A szerepkör-hozzárendelések rendszeres auditálásával ellenőrizze, hogy az egyes szerepkörök rendelkeznek-e a megfelelő engedélyekkel.
> * Szerepkörök és hozzárendelések törlése, ha a felhasználók szerepköröket vagy hozzárendeléseket módosítanak.