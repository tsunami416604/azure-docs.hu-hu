---
title: Azure VMware-megoldások (AVS) – az AVS privát Felhőbeli tevékenységének figyelése
description: Az Azure VMware-megoldásban az AVS-környezettel, például riasztásokkal, eseményekkel, feladatokkal és auditálással kapcsolatos tevékenységekre vonatkozó információkat ismerteti.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019672"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>VMware Solutions-(AVS-) tevékenységek figyelése

Az AVS-tevékenység naplói betekintést nyújtanak az AVS-portálon végzett műveletekre. A lista riasztásokat, eseményeket, feladatokat és naplózást tartalmaz. A tevékenységi naplók segítségével meghatározhatja, hogy ki, mikor és milyen műveleteket hajtottak végre. A Tevékenységnaplók nem tartalmazzák a felhasználó által végzett olvasási műveleteket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-avs-portal"></a>Hozzáférés az AVS-portálhoz

Hozzáférés az [AVS-portálhoz](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Tevékenység adatai

A tevékenység oldalaihoz való hozzáféréshez válassza az oldalsó menü **tevékenység** elemét.

![A tevékenységek oldalának áttekintése](media/activity-page-overview.png)

A tevékenység lapon található tevékenységek részleteinek megtekintéséhez válassza ki a tevékenységet. A jobb oldalon megnyílik a részletek panel. A panel műveletei a tevékenység típusától függenek. A panel bezárásához kattintson az **X** gombra.

Kattintson egy oszlop fejlécére a Megjelenítés rendezéséhez. A megtekinthető adott értékek oszlopai szűrhetők. Töltse le a tevékenység jelentését a **Letöltés CSV-ként** elemre kattintva.

## <a name="alerts"></a>Értesítések

A riasztások az AVS-környezetben felmerülő jelentős tevékenységekről szóló értesítések. A riasztások olyan eseményeket érintenek, amelyek befolyásolják a számlázási vagy a felhasználói hozzáférést.

A riasztások elfogadásához és a listából való eltávolításához válasszon ki egyet vagy többet a listából, majd kattintson az **nyugtázás**gombra.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Column | Leírás |
------------ | ------------- |
| Riasztás típusa | A riasztás kategóriája.|
| Idő | A riasztás ideje. |
| Súlyosság | A riasztás jelentősége.|
| Erőforrás neve | Az erőforráshoz hozzárendelt név, például az AVS Private Cloud neve. |
| Erőforrás típusa | Erőforrás kategóriája: AVS Private Cloud, Cloud rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Leírás | A riasztást kiváltó esemény leírása. |
| Elismerte | Annak jelzése, hogy a riasztás igazolva van-e. |

## <a name="events"></a>Események

Az események a felhasználói és rendszertevékenységeket mutatják az AVS-portálon. Az események lap felsorolja az adott erőforráshoz társított tevékenységet és a hatás súlyosságát.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Column | Leírás |
------------ | ------------- |
| Idő | Az esemény dátuma és időpontja. |
| Esemény típusa | Az eseményt azonosító numerikus kód. |
| Súlyosság | Esemény súlyossága.|
| Erőforrás neve | Az erőforráshoz hozzárendelt név, például az AVS Private Cloud neve. |
| Erőforrás típusa | Erőforrás kategóriája: AVS Private Cloud, Cloud rack. |
| Leírás | A riasztást kiváltó esemény leírása. |

## <a name="tasks"></a>Feladatok

A feladatok olyan AVS Private Cloud-tevékenységek, amelyek várhatóan 30 másodpercet vagy annál nagyobb időt vesznek igénybe. (A várhatóan 30 másodpercnél rövidebb tevékenységek jelentése csak eseményként történik.) Nyissa meg a feladatok lapokat az AVS Private Cloud-hoz tartozó feladatok állapotának nyomon követéséhez.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Column | Leírás |
------------ | ------------- |
| Feladat azonosítója | A feladat egyedi azonosítója. |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladat végrehajtásához hozzárendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrás típusa | Erőforrás kategóriája: AVS Private Cloud, Cloud rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Kezdés | A feladat kezdési időpontja. |
| Vége | A feladat befejezési időpontja. |
| Állapot | Aktuális feladat állapota. |
| Eltelt idő | Az az idő, ameddig a feladat befejeződik (ha elkészült) vagy éppen folyamatban van (ha folyamatban van). |
| Leírás | Feladat leírása. |

## <a name="audit"></a>Naplózás

A naplók nyomon követhetik a felhasználói tevékenységeket. A naplók segítségével figyelheti a felhasználói tevékenységeket az összes felhasználó számára.

A riasztásokhoz a következő oszlopokban találhatók információk. Kattintson az **Oszlopok szerkesztése** elemre, és válassza ki a megtekinteni kívánt oszlopokat.

| Column | Leírás |
------------ | ------------- |
| Idő | A naplóbejegyzés időpontja. |
| Művelet | A feladat által végrehajtott művelet. |
| Felhasználó | A feladathoz hozzárendelt felhasználó. |
| Erőforrás neve | Az erőforráshoz rendelt név. |
| Erőforrás típusa | Erőforrás kategóriája: AVS Private Cloud, Cloud rack. |
| Erőforrás-azonosító | Az erőforrás azonosítója. |
| Eredmény | A tevékenység eredménye, például **sikeres**. |
| Szükséges idő | A feladat elvégzéséhez szükséges idő. |
| Leírás | A művelet leírása. |

## <a name="next-steps"></a>Következő lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ az [AVS Private felhőkről](cloudsimple-private-cloud.md)
