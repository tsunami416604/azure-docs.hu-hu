---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53995708"
---
Szerepköralapú hozzáférés-vezérlés egy öröklési által készített biztonsági stratégia hozzáférési engedélyek és szerepkörök kezeléséhez. Leszármazott szerepkörök öröklik az engedélyeket a szülő szerepköröket. Emellett engedélyek rendelhetők folyamatban van egy szülő szerepkörből örökölt nélkül. Akkor is rendelhet igény szerint testre szabhatja a szerepkör.

Előfordulhat, hogy például, hogy a hely rendszergazdája globális hozzáférési futtatásához egy megadott hely összes műveletet kell. Minden csomópont alatt, vagy a címtéren belüli hozzátartozik. Egy eszköz-telepítő csak szükség lehet *olvasási* és *frissítése* eszközeit és érzékelőit engedélyeit.

Minden esetben szerepkörök kapnak *pontosan, és legfeljebb szükséges hozzáférést* száma a minimális jogosultság elvének feladataik teljesítéséhez. Ez az elv alapján identitást kapnak a *csak*:

* Olyan mértékű hozzáférést a feladat végrehajtásához szükséges.
* Egy szerepkör megfelelő, és csak a feladat elvégzése.

>[!IMPORTANT]
> A minimális jogosultság elvének mindig követik.

Két másik fontos szerepköralapú hozzáférés-vezérlés: bevált gyakorlatokat követnie:

> [!div class="checklist"]
> * Ellenőrizze, hogy minden egyes szerepkör rendelkezik a megfelelő engedélyekkel a szerepkör-hozzárendelések rendszeresen naplózza.
> * Ha egyéni felhasználók számára a szerepkörök vagy hozzárendelések módosítása a törlés szerepkörök és -hozzárendelések.