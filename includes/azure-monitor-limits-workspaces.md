---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392897"
---
**Adatgyűjtés mennyisége és megőrzése** 

| Szint | Napi korlát | Adatmegőrzés | Megjegyzés |
|:---|:---|:---|:---|
| Aktuális/GB-os árképzési szintek<br>(bevezetve április 2018.) | Korlátlan | 30-730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt ingyenes szintek<br>(bevezetve április 2016.) | 500 MB | 7 nap | Ha a munkaterület eléri a napi 500 MB-os korlátot, az adatfeldolgozás leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú. Vegye figyelembe, hogy a Azure Security Center által gyűjtött adatok nem szerepelnek a napi 500 MB-os korláton, és ezen a korláton felül tovább lesznek gyűjtve.  |
| Örökölt önálló/GB szintű<br>(bevezetve április 2016.) | Korlátlan | 30 – 730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt csomópontok száma (OMS)<br>(bevezetve április 2016.) | Korlátlan | 30 – 730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt standard szint | Korlátlan | 30 nap  | A megőrzés nem módosítható |
| Örökölt prémium szint | Korlátlan | 365 nap  | A megőrzés nem módosítható |

**Munkaterületek száma/előfizetés.**

| Tarifacsomag    | Munkaterület korlátja | Megjegyzések
|:---|:---|:---|
| Ingyenes csomag  | 10 | Ez a korlát nem növelhető. |
| Az összes többi szintje | Korlátlan | Az erőforráscsoport erőforrásainak száma és az egyes előfizetésekhez tartozó erőforráscsoportok száma korlátozza. |

**Azure Portal**

| Category | Korlátok | Megjegyzések |
|:---|:---|:---|
| Egy napló lekérdezés által visszaadott maximális rekordok | 10,000 | Csökkentheti az eredményeket a lekérdezés hatóköre, az időtartomány és a lekérdezésben szereplő szűrők használatával. |


**Adatgyűjtő API**

| Category | Korlátok | Megjegyzések |
|:---|:---|:---|
| Egyetlen bejegyzés maximális mérete | 30 MB | Nagyobb kötetek felosztása több bejegyzésbe. |
| Mezőértékek maximális mérete  | 32 KB | A 32 KB-nál hosszabb mezők csonkolva lesznek. |

**Keresési API**

| Category | Korlátok | Megjegyzések |
|:---|:---|:---|
| Egyetlen lekérdezésben visszaadott rekordok maximális száma | 500,000 | |
| Visszaadott adatmennyiség maximális mérete | 64 000 000 bájt (~ 61 MiB)| |
| Lekérdezés maximális futási ideje | 10 perc | További részletek: [időtúllépések](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Kérelmek maximális száma | 200 kérelem/30 másodperc/HRE felhasználó vagy ügyfél IP-címe | Részletekért lásd a [díjszabási korlátokat](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Általános munkaterület korlátai**

| Category | Korlátok | Megjegyzések |
|:---|:---|:---|
| Oszlopok maximális száma egy táblában         | 500 | |
| Oszlop nevének maximális száma | 500 | |
| Kapacitású régiók | USA nyugati középső régiója | Ebben a régióban jelenleg nem hozhat létre új munkaterületet, mert az átmeneti kapacitási korláttal rendelkezik. Ez a korlát 2019 október végére tervezett. |
| Adatexportálás | Jelenleg nem érhető el | Az Azure Function vagy a Logic App használatával összesítheti és exportálhatja az adatokat. | 

>[!NOTE]
>Attól függően, hogy mennyi ideig használta a Log Analytics, lehet, hogy hozzáfér a régi díjszabási csomagokhoz. További információ a [log Analytics korábbi díjszabási szintjeiről](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 