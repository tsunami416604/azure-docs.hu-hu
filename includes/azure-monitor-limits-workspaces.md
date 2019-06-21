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
ms.openlocfilehash: dfcf47a95d1dbff34ff322768fc4ac6c9674cff4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295928"
---
**Gyűjtemény adatmennyiség és megőrzés** 

| Szint | Korlátozza a naponta | Adatmegőrzés | Megjegyzés |
|:---|:---|:---|:---|
| Jelenlegi GB tarifacsomag kiválasztása<br>(a 2018 április ismertetése) | Korlátlan | 30 - 730 napig | Adatmegőrzés 31 napos időszak letelte után számítunk fel további díjat érhető el. További információ az Azure Monitor díjszabásáról. |
| Régi ingyenes szint<br>(2016. április jelent meg). | 500 MB | 7 nap | Amikor a munkaterület eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap kezdetekor folytatódik. A napi elszámolás UTC-alapú. |
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