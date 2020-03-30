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
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334849"
---
**Adatgyűjtési mennyiség és -megőrzés** 

| Szint | Napi korlát | Adatmegőrzés | Megjegyzés |
|:---|:---|:---|:---|
| Aktuális GB-onkénti tarifacsomag<br>(2018. április) | Nincs korlátozás | 30 - 730 nap | A 31 napon túli adatmegőrzés további költségek ellenében áll rendelkezésre. További információ az Azure Monitor díjszabásáról. |
| Örökölt ingyenes csomagok<br>(2016. április) | 500 MB | 7 nap | Amikor a munkaterület eléri a napi 500 MB-os korlátot, az adatbetöltés leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú. Vegye figyelembe, hogy az Azure Security Center által gyűjtött adatok nem szerepelnek ebben az 500 MB/nap korlátban, és továbbra is e korlát felett kerülnek gyűjtésre.  |
| Örökölt önálló GB-szintenként<br>(2016. április) | Nincs korlátozás | 30-730 nap | A 31 napon túli adatmegőrzés további költségek ellenében áll rendelkezésre. További információ az Azure Monitor díjszabásáról. |
| Örökölt csomópontonként (OMS)<br>(2016. április) | Nincs korlátozás | 30-730 nap | A 31 napon túli adatmegőrzés további költségek ellenében áll rendelkezésre. További információ az Azure Monitor díjszabásáról. |
| Örökölt standard szint | Nincs korlátozás | 30 nap  | A megőrzés nem módosítható |
| Örökölt prémium szint | Nincs korlátozás | 365 nap  | A megőrzés nem módosítható |

**Előfizetésenkénti munkaterületek száma.**

| Tarifacsomag    | Munkaterületi korlát | Megjegyzések
|:---|:---|:---|
| Ingyenes szint  | 10 | Ez a korlát nem növelhető. |
| Minden más szint | Nincs korlátozás | Az erőforráscsoporton belüli erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátozza. |

**Azure-portál**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Naplólekérdezés által visszaadott rekordok maximális | 10,000 | Csökkentse az eredményeket a lekérdezés hatókörével, időtartományával és szűrőivel a lekérdezésben. |


**Adatgyűjtő API**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Egyetlen bejegyzés maximális mérete | 30 MB | Nagyobb kötetek felosztása több bejegyzésre. |
| A mezőértékek maximális mérete  | 32 KB | A 32 KB-nál hosszabb mezők csonkolva lesznek. |

**Keresési API**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Egyetlen lekérdezésben visszaadott rekordok maximális | 500 000 | |
| A visszaadott adatok maximális mérete | 64 000 000 bájt (~61 miB)| |
| A lekérdezés maximális futási ideje | 10 perc | A részleteket az [Időeltések](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) ben találja.  |
| Maximális kérelemarány | 200 kérelem 30 másodpercenként AAD-felhasználónként vagy ügyfél IP-címenként | A részleteket lásd [a Díjkorlátok](https://dev.loganalytics.io/documentation/Using-the-API/Limits) oldalon. |

**Általános munkaterületi korlátok**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Táblázat oszlopainak maximális értéke         | 500 | |
| Az oszlopnév maximális karaktere | 500 | |
| Adatexportálás | Jelenleg nem érhető el | Az Azure Function vagy a Logic App használatával összesítése és exportálása. | 

**Adatbetöltési hangerő**


Az Azure Monitor egy nagy méretű adatszolgáltatás, amely több ezer ügyfelet szolgál ki, akik havonta több terabájtnyi adatot küldenek növekvő ütemben. Az Azure-erőforrásokból [diagnosztikai beállításokkal](../articles/azure-monitor/platform/diagnostic-settings.md) küldött adatok alapértelmezett betöltési volumensebesség-korlátja munkaterületenként körülbelül **6 GB/perc.** Ez egy hozzávetőleges érték, mivel a tényleges méret adattípusok on-val változhat a napló hosszától és tömörítési arányától függően. Ez a korlát nem vonatkozik az ügynököktől vagy az [adatgyűjtő API-tól küldött adatokra.](../articles/azure-monitor/platform/data-collector-api.md)

Ha az adatokat nagyobb sebességgel küldi el egyetlen munkaterületre, a rendszer eldob bizonyos adatokat, és 6 óránként egy eseményt küld a munkaterület *Operation* táblájának, miközben a küszöbértéket továbbra is túllépi. Ha a betöltési kötet továbbra is meghaladja a sebességkorlátot, vagy várhatóan hamarosan eléri, támogatási kérelem megnyitásával kérheti a munkaterület növelését.
 
Ha értesítést szeretne kapni egy ilyen eseményről a munkaterületen, hozzon létre egy [naplóriasztási szabályt](../articles/azure-monitor/platform/alerts-log.md) a következő lekérdezés használatával, amely riasztási logikai alapot biztosít a nullánál nagyobb számú eredményre.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Attól függően, hogy mennyi ideig használja a Log Analytics, előfordulhat, hogy hozzáférhet a régebbi tarifacsomagok. További információ a [Log Analytics örökölt tarifacsomagjairól.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) 