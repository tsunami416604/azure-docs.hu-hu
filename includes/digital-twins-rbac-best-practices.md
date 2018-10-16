---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324107"
---
Szerepköralapú hozzáférés-vezérlés egy öröklési által készített biztonsági stratégia hozzáférési engedélyek és szerepkörök kezeléséhez. Leszármazott szerepkörök öröklik az engedélyeket a szülő szerepköröket. Folyamatban van egy szülő szerepkörből örökölt nélkül is rendelt engedélyek. Akkor is rendelhet igény szerint testre szabhatja a szerepkör.

Például egy **terület rendszergazda** globális hozzáférés minden művelet egy megadott hely (beleértve az összes csomópont alatt, vagy a benne lévő) futtatásához szükség lehet, mivel egy **eszközt telepítő** is elegendő lehet *olvasási* és *frissítése* eszközeit és érzékelőit engedélyeit.

Minden esetben szerepkörök számára ajánlott hozzáférést adni **pontosan, és legfeljebb szükséges hozzáférést** kiszolgálónként feladataik teljesítéséhez a **minimális jogosultság elvének**, amely engedélyezi az identitás *csak*:

* Olyan mértékű hozzáférést a feladat végrehajtásához szükséges.
* Egy szerepkör megfelelő, és csak a feladat elvégzése.

>[!IMPORTANT]
> **Mindig a minimális jogosultság elvének**.

Két másik fontos szerepköralapú hozzáférés-vezérlés bevált gyakorlatokat követnie:

> [!div class="checklist"]
> * Ellenőrizze, hogy minden egyes szerepkör rendelkezik a megfelelő engedélyekkel a szerepkör-hozzárendelések rendszeresen naplózza.
> * Szerepkörök és -hozzárendelések kell kell tisztítani felfelé egyének szerepkörök vagy a hozzárendelések módosítása esetén.