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
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755759"
---
Az alábbi korlátok vonatkoznak az egyes Log Analytics-erőforrásokra előfizetésenként:

| Erőforrás | Alapértelmezett korlát | Megjegyzések
| --- | --- | --- |
| Ingyenes munkaterületek száma előfizetésenként | 10 | Ez a korlát nem növelhető. |
| Fizetős munkaterületek száma előfizetésenként | – | Az erőforráscsoporton belüli erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátozza | 

>[!NOTE]
>2018. április 2. egy új előfizetést az új munkaterületek automatikusan használja a *GB* tarifacsomagot.  Április 2. előtt létrehozott meglévő előfizetésekhez, vagy egy meglévő EA-regisztrációhoz kötött előfizetéshez továbbra is az új munkaterületekhez három tarifacsomag közötti választáshoz. 
>

Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületekre:

|  | Ingyenes | Standard | Prémium | Különálló | OMS | /GB |
| --- | --- | --- | --- | --- | --- |--- |
| Naponta összegyűjtött adatmennyiség |500 MB<sup>1</sup> |None |None | None | None | None
| Adatmegőrzés időtartama |7 nap |1 hónap |12 hónap | 1 hónap<sup>2</sup> | 1 hónap<sup>2</sup>| 1 hónap<sup>2</sup>|

<sup>1</sup> Ha az ügyfél eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú.

<sup>2</sup> önálló, OMS, és GB díjszabások az Adatmegőrzés időtartama és 730 nap között lehet növelni.

| Kategória | Korlátok | Megjegyzések
| --- | --- | --- |
| Data Collector API | Az egyedi közzétételek maximális mérete 30 MB<br>A mezőértékek maximális mérete 32 KB | A nagyobb mennyiségeket bontsa több közzétételre<br>A 32 KB-nál hosszabb mezők csonkolva lesznek. |
| Keresési API | 5000 visszaadott rekord a nem összesített adatok esetében<br>500 000 rekord az összesített adatok esetében | Az összesített adatok a `summarize` parancsot tartalmazó kereséssel lekért adatok
 
