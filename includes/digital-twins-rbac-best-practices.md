---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044919"
---
A szerepköralapú hozzáférés-vezérlés öröklődésalapú biztonsági stratégia a hozzáférés, az engedélyek és a szerepkörök kezelésére. A leszármazott szerepkörök a szülőszerepköröktől öröklik az engedélyeket. Az engedélyek fölérendelt szerepkörtől való öröklés nélkül is hozzárendelhetők. A szerepkör szükség szerint testreszabásához is hozzárendelhetők.

Előfordulhat például, hogy egy térrendszergazdának globális hozzáférésre van szüksége egy adott terület összes műveletének futtatásához. Az Access tartalmazza az összes csomópontot a tér alatt vagy azon belül. Előfordulhat, hogy az eszköztelepítőnek csak *olvasási* és *frissítési* engedélyeket kell kérnie az eszközökhöz és az érzékelőkhöz.

A szerepkörök minden esetben pontosan és legfeljebb a feladataik *teljesítéséhez szükséges hozzáféréssel* rendelkeznek a legkisebb jogosultság elve szerint. Ennek az elvnek megfelelően a személyazonosság *ot csak*a következőkbiztosítják:

* A feladat elvégzéséhez szükséges hozzáférés mennyisége.
* A feladat elvégzésére vonatkozó és feladatának elvégzésére korlátozódó szerep.

>[!IMPORTANT]
> Mindig kövesd a legkisebb kiváltság elvét.

Két másik fontos szerepkör-alapú hozzáférés-vezérlési gyakorlat, amelyet követni kell:

> [!div class="checklist"]
> * Rendszeresen naplózza a szerepkör-hozzárendeléseket annak ellenőrzéséhez, hogy az egyes szerepkörök rendelkeznek-e a megfelelő engedélyekkel.
> * A szerepkörök és a hozzárendelések karbantartása, amikor az egyének szerepköröket vagy hozzárendeléseket módosítanak.