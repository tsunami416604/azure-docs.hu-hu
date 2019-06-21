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
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305290"
---
**Gyűjtemény adatmennyiség és megőrzés** 

| Szint | Korlátozza a naponta | Adatmegőrzés | Megjegyzés |
|:---|:---|:---|:---|
| Jelenlegi GB tarifacsomag kiválasztása<br>(a 2018 április ismertetése) | Korlátlan | 30 - 730 napig | Adatmegőrzés 31 napos időszak letelte után számítunk fel további díjat érhető el. További információ az Azure Monitor díjszabásáról. |
| Régi ingyenes szint<br>(2016. április jelent meg). | 500 MB | 7 nap | Ha a munkaterület eléri a napi 500 MB-os, adatbetöltés leállítja, és a következő nap kezdetekor folytatódik. A napi elszámolás UTC-alapú. Vegye figyelembe, hogy az Azure Security Center által gyűjtött adatokat nem tartalmazza ezt a napi 500 MB-ban, és továbbra is meghaladja a gyűjtendő.  |
| Örökölt önálló GB szint<br>(2016. április jelent meg). | Korlátlan | 30 és 730 nap | Adatmegőrzés 31 napos időszak letelte után számítunk fel további díjat érhető el. További információ az Azure Monitor díjszabásáról. |
| Örökölt Csomópontonkénti (OMS)<br>(2016. április jelent meg). | Korlátlan | 30 és 730 nap | Adatmegőrzés 31 napos időszak letelte után számítunk fel további díjat érhető el. További információ az Azure Monitor díjszabásáról. |
| Örökölt Standard csomag | Korlátlan | 30 nap  | Nem módosítható a megőrzési |
| Örökölt prémium szint | Korlátlan | 365 nap  | Nem módosítható a megőrzési |

**Munkaterületek száma előfizetésenként száma.**

| Tarifacsomag    | Munkaterület-korlát | Megjegyzések
|:---|:---|:---|
| Ingyenes szint  | 10 | Ez a korlát nem növelhető. |
| Minden más szinten | Korlátlan | Az erőforráscsoporton belül erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátot jelent. |

**Azure Portal**

| Category | Limits | Megjegyzések |
|:---|:---|:---|
| A napló lekérdezés által visszaadott rekordok maximális | 10,000 | Csökkentse a lekérdezés hatókörébe, idő tartomány és szűrők használatával a lekérdezés eredményeit. |


**Adatgyűjtő API**

| Category | Limits | Megjegyzések |
|:---|:---|:---|
| Az egyedi közzétételek maximális mérete | 30 MB | A nagyobb mennyiségeket bontsa több közzétételre. |
| A mezőértékek maximális mérete  | 32 KB | A 32 KB-nál hosszabb mezők csonkolva lesznek. |

**Keresési API**

| Category | Limits | Megjegyzések |
|:---|:---|:---|
| Nem összesített adatok a visszaadott rekordok maximális | 5,000 | |
| Maximális rekord az összesített adatok | 500,000 | Összesített adatok tartalmazó kereséssel a `summarize` parancsot. |
| Visszaadott adatok maximális mérete | 64,000,000 bájtok (KB 61 MiB)| |
| Maximális futási idejének lekérdezés | 10 perc | Lásd: [időtúllépések](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) részleteiről.  |
| Kérelem maximális sebesség | AAD-felhasználó vagy az ügyfél IP-címenként 30 másodpercenként 200 kérelmek | Lásd: [sebességhatárok](https://dev.loganalytics.io/documentation/Using-the-API/Limits) részleteiről. |

**Általános munkaterület korlátok**

| Category | Limits | Megjegyzések |
|:---|:---|:---|
| Egy tábla oszlopok maximális száma         | 500 | |
| Az oszlop neve legfeljebb karakter | 500 | |
| Régiók kapacitással | USA nyugati középső régiója | Ön jelenleg nem lehet létrehozni egy új munkaterületet ebben a régióban, ideiglenes kapacitásának határát, mert. Ez a korlátozás tervezett elhárítandó 2019. szeptember végéig. |
| Adatok exportálása | Jelenleg nem érhető el | Használja az Azure-függvény vagy a logikai alkalmazás összesítéséhez és exportálhat adatokat. | 

>[!NOTE]
>Attól függően, hogy mennyi ideig már használja egy Log Analytics előfordulhat, hogy hozzáfér a régi díjszabási szint. Tudjon meg többet [tarifacsomagok Log Analytics örökölt](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 