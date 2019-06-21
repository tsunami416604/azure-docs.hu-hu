---
title: fájl belefoglalása
description: fájl belefoglalása
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293452"
---
Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületen a jelenlegi fogyasztásalapú tarifacsomag 2018 április rendszerben bevezetett:

|     | Per GB 2018 |
| --- | --- | 
| Naponta összegyűjtött adatmennyiség | None |
| Adatmegőrzés időtartama | 30 és 730 nap<sup>1</sup> |

Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületet a tarifacsomagok legutóbbi régebbi:

|  | Ingyenes | Önálló (GB-onként) | Csomópontonként (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| Naponta összegyűjtött adatmennyiség |500 MB<sup>2</sup> |None |None |
| Adatmegőrzés időtartama |7 nap | 30 és 730 nap<sup>1</sup> | 30 és 730 nap<sup>1</sup> |

Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületet a tarifacsomagok legrégebbi régebbi:

|  | Standard | Prémium | 
| --- | --- | --- | --- | --- | --- |--- |
| Naponta összegyűjtött adatmennyiség | None | None | 
| Adatmegőrzés időtartama |30 nap | 365 nap |

<sup>1</sup>31 napos időszak letelte után az adatmegőrzés érhető el további díjakat. Tudjon meg többet [díjszabás az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

<sup>2</sup>amikor munkaterületét eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap kezdetekor folytatódik. A napi elszámolás UTC-alapú.

>[!NOTE]
>Attól függően, hogy mennyi ideig már használja egy Log Analytics előfordulhat, hogy hozzáfér a régi díjszabási szint. Tudjon meg többet [tarifacsomagok Log Analytics örökölt](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
>

Az alábbi korlátok vonatkoznak az Azure Log Analytics-erőforrások (munkaterület) előfizetésenként.

| Tarifacsomag    | Munkaterületek száma előfizetésenként száma | Megjegyzések
| --- | --- | --- |
| Ingyenes szint  | 10 | Ez a korlát nem növelhető. |
| Ingyenes kivételével az összes szint | – | Az erőforráscsoporton belül erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátot jelent. | 

Az alábbi korlátozások érvényesek a Log Analytics API-khoz:

| Category | Limits | Megjegyzések
| --- | --- | --- |
| Data Collector API | Az egyedi közzétételek maximális mérete 30 MB-ot.<br>A mezőértékek maximális mérete 32 KB. | A nagyobb mennyiségeket bontsa több közzétételre.<br>A 32 KB-nál hosszabb mezők csonkolva lesznek. |
| Keresési API | összesen 5 000 rekord nem összesített adatok adja vissza.<br>500 000 rekord az összesített adatok. | Összesített adatok tartalmazó kereséssel a `summarize` parancsot.
 
