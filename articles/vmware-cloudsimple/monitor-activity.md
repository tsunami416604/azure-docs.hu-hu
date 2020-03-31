---
title: Privát felhőbeli tevékenység figyelése
titleSuffix: Azure VMware Solution by CloudSimple
description: Az Azure VMware-megoldás a CloudSimple környezetben végzett tevékenységekkel kapcsolatos információkat ismerteti, beleértve a riasztásokat, eseményeket, feladatokat és naplózást.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019672"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>VMware-megoldás figyelése a CloudSimple tevékenységével

A CloudSimple tevékenységnaplók betekintést nyújtanak a CloudSimple portálon végzett műveletekbe.  A lista riasztásokat, eseményeket, feladatokat és naplózást tartalmaz.  A tevékenységnaplók segítségével határozza meg, hogy ki, mikor és milyen műveleteket hajtottak végre.  A tevékenységnaplók nem tartalmazzák a felhasználó által végzett olvasási műveleteket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

A [CloudSimple portál](access-cloudsimple-portal.md)elérése.

## <a name="activity-information"></a>Tevékenységadatai

A Tevékenység lapok eléréséhez válassza az oldal menü **Tevékenység parancsát.**

![Tevékenység lap – áttekintés](media/activity-page-overview.png)

A tevékenység oldalon található tevékenységek részleteinek megtekintéséhez jelölje ki a tevékenységet. A jobb oldalon megnyílik a részletek panel. A panelen végzett műveletek a tevékenység típusától függenek. A panel bezárásához kattintson az **X** gombra.

Kattintson egy oszlopfejlécre a megjelenítés rendezéséhez.  Szűrheti az oszlopokat a megtekinteni kívánt értékekre.  Töltse le a tevékenységjelentést a **Letöltés CSV-ként** ikonra kattintva.

## <a name="alerts"></a>Riasztások

A riasztások a CloudSimple környezetben végzett bármely jelentős tevékenységről szóló értesítések.  A riasztások közé tartoznak a számlázást vagy a felhasználói hozzáférést befolyásoló események.

A riasztások nyugtázásához és a listából való eltávolításához jelöljön ki egyet vagy többet a listából, és kattintson a **Nyugta gombra.**

A riasztásokhoz a következő információoszlopok érhetők el. Kattintson az **Oszlopok szerkesztése gombra,** és jelölje ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Riasztás típusa | A riasztás kategóriája.|
| Time | A riasztás bekövetkezésének időpontja. |
| Severity | A riasztás jelentősége.|
| Erőforrás neve | Az erőforráshoz rendelt név, például a magánfelhő neve. |
| Erőforrás típusa | Az erőforrás kategóriája: Privát felhő, Cloud Rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Leírás | Annak leírása, hogy mi váltotta ki a riasztást. |
| Nyugtázva | Annak feltüntetése, hogy a figyelmeztető jelzést elismerik-e. |

## <a name="events"></a>Események

Az események felhasználói és rendszertevékenységet mutatnak a CloudSimple portálon. Az Események lap felsorolja az adott erőforráshoz társított tevékenységet és a hatás súlyosságát.

A riasztásokhoz a következő információoszlopok érhetők el. Kattintson az **Oszlopok szerkesztése gombra,** és jelölje ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Time | Az esemény bekövetkezésének dátuma és időpontja. |
| Eseménytípus | Az eseményt azonosító numerikus kód. |
| Severity | Esemény súlyossága.|
| Erőforrás neve | Az erőforráshoz rendelt név, például a magánfelhő neve. |
| Erőforrás típusa | Az erőforrás kategóriája: Privát felhő, Cloud Rack. |
| Leírás | Annak leírása, hogy mi váltotta ki a riasztást. |

## <a name="tasks"></a>Feladatok

A feladatok olyan privát felhőbeli tevékenységek, amelyek végrehajtása várhatóan 30 másodpercet vagy még többet vesz igénybe. (A várhatóan 30 másodpercnél rövidebb idő alatt eltartandó tevékenységek csak eseményként lesznek jelentve.) Nyissa meg a Feladatok lapokat a magánfelhő tevékenységeinek előrehaladásának nyomon követéséhez.

A riasztásokhoz a következő információoszlopok érhetők el. Kattintson az **Oszlopok szerkesztése gombra,** és jelölje ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Feladat azonosítója | A feladat egyedi azonosítója |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladat végrehajtásához rendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrás típusa | Az erőforrás kategóriája: Privát felhő, Cloud Rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Indítás | A tevékenység kezdési időpontja. |
| Befejezés | A tevékenység befejezési időpontja. |
| status | Aktuális tevékenységállapota. |
| Eltelt idő | Az az idő, amelyet a feladat befejezéséhez (ha befejeződött) vagy jelenleg vesz igénybe (ha folyamatban van). |
| Leírás | Feladat leírása. |

## <a name="audit"></a>Naplózás

A naplónaplók nyomon követik a felhasználói tevékenységeket. A naplók segítségével figyelheti az összes felhasználó felhasználói tevékenységét.

A riasztásokhoz a következő információoszlopok érhetők el. Kattintson az **Oszlopok szerkesztése gombra,** és jelölje ki a megtekinteni kívánt oszlopokat.

| Oszlop | Leírás |
------------ | ------------- |
| Time | A naplóbejegyzés időpontja. |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladathoz rendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrás típusa | Az erőforrás kategóriája: Privát felhő, Cloud Rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Eredmény | A tevékenység eredménye, például a **Siker**. |
| Igénybe vett idő | A feladat befejezésének ideje. |
| Leírás | A művelet leírása. |

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
