---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: d25f6f0d787b7343b93025ff2dd2bd5bf4f0f6d8
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948935"
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