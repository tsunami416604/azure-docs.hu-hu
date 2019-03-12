---
title: fájl belefoglalása
description: fájl belefoglalása
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553693"
---
Az alábbi korlátok vonatkoznak az Azure Log Analytics-erőforrásokra előfizetésenként.

| Erőforrás | Alapértelmezett korlát | Megjegyzések
| --- | --- | --- |
| Ingyenes munkaterületek száma előfizetésenként | 10 | Ez a korlát nem növelhető. |
| Fizetős munkaterületek száma előfizetésenként | – | Az erőforráscsoporton belül erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátot jelent. | 

>[!NOTE]
>2018. április 2. egy új előfizetést az új munkaterületek automatikusan használja a *GB* tarifacsomagot. Április 2. előtt létrehozott meglévő előfizetésekhez, vagy olyan előfizetést, amely egy meglévő nagyvállalati szerződésre való beléptetésről kötött továbbra is az új munkaterületekhez három tarifacsomag közül választhat. 
>

Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületet.

|  | Ingyenes | Standard | Prémium | Különálló | OMS | /GB |
| --- | --- | --- | --- | --- | --- |--- |
| Naponta összegyűjtött adatmennyiség |500 MB<sup>1</sup> |None |Nincsenek | Nincsenek | Nincsenek | None
| Adatmegőrzés időtartama |7 nap |1 hónap |12 hónap | 1 hónap<sup>2</sup> | 1 hónap<sup>2</sup>| 1 hónap<sup>2</sup>|

<sup>1</sup>amikor ügyfél eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap kezdetekor folytatódik. A napi elszámolás UTC-alapú.

<sup>2</sup>önálló, OMS, és GB díjszabások az Adatmegőrzés időtartama és 730 nap között lehet növelni.

| Kategória | Korlátok | Megjegyzések
| --- | --- | --- |
| Data Collector API | Az egyedi közzétételek maximális mérete 30 MB-ot.<br>A mezőértékek maximális mérete 32 KB. | A nagyobb mennyiségeket bontsa több közzétételre.<br>A 32 KB-nál hosszabb mezők csonkolva lesznek. |
| Keresési API | összesen 5 000 rekord nem összesített adatok adja vissza.<br>500 000 rekord az összesített adatok. | Összesített adatok tartalmazó kereséssel a `summarize` parancsot.
 
