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
ms.openlocfilehash: 017c02a10137579e6e3497775e9e4a3ac0a5d72d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350858"
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

**Adatfeldolgozási kötetek aránya**


A Azure Monitor egy nagy léptékű adatszolgáltatás, amely több ezer ügyfelet szolgál ki havonta több, mint havi terabájt adatküldéssel. Az Azure-erőforrásokból a [diagnosztikai beállítások](../articles/azure-monitor/platform/diagnostic-settings.md) használatával elküldett adatokra vonatkozó alapértelmezett adatfeldolgozási sebesség legfeljebb **6 GB/perc/** munkaterület. Ez egy hozzávetőleges érték, mivel a tényleges méret eltérő lehet az adattípusok között a napló hosszától és a tömörítési aránytól függően. Ez a korlát nem vonatkozik az ügynököktől vagy adatgyűjtő [API](../articles/azure-monitor/platform/data-collector-api.md)-ból továbbított adatokra.

Ha egy adott munkaterülethez magasabb sebességgel küldi az adatmennyiséget, egyes adatvesztést okoz, és a rendszer 6 óránként küldi el az eseményt a munkaterület *műveleti* táblájába, amíg a küszöbérték továbbra is túllépve lesz. Ha a betöltési kötet továbbra is meghaladja a díjszabási korlátot, vagy hamarosan várhatóan elérheti azt, akkor a támogatási kérés megnyitásával növelheti a munkaterületet.
 
Ha értesítést szeretne kapni a munkaterületen lévő ilyen eseményekről, hozzon létre egy [naplózási riasztási szabályt](../articles/azure-monitor/platform/alerts-log.md) a következő lekérdezés és a riasztási logika alapján a nulla értékű eredmények száma értékkel.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Attól függően, hogy mennyi ideig használta a Log Analytics, lehet, hogy hozzáfér a régi díjszabási csomagokhoz. További információ a [log Analytics korábbi díjszabási szintjeiről](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 