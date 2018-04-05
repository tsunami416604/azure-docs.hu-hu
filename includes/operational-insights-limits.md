---
title: fájl belefoglalása
description: fájl belefoglalása
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 03/29/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 29256b3bcfedb7fe5045ff4c6c3842eb25e00a28
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
Az alábbi korlátok vonatkoznak az egyes Log Analytics-erőforrásokra előfizetésenként:

| Erőforrás | Alapértelmezett korlát | Megjegyzések
| --- | --- | --- |
| Ingyenes munkaterületek száma előfizetésenként | 10 | Ez a korlát nem növelhető. |
| Fizetős munkaterületek száma előfizetésenként | – | Az erőforráscsoporton belüli erőforrások száma és az előfizetésenkénti erőforráscsoportok száma korlátozza | 

>[!NOTE]
>2018. április 2. frissítésétől új munkaterületeket, egy új előfizetés automatikusan használja a */ GB* árképzési terv.  Meglévő előfizetések. április 2 előtt létrehozott, vagy olyan előfizetést, amelyet volt kötve a meglévő EA beléptetési folytathatja a választás az új munkaterületek három árképzési szinteket. 
>

Az alábbi korlátok vonatkoznak az egyes Log Analytics-munkaterületekre:

|  | Ingyenes | Standard | Prémium | Különálló | OMS | /GB |
| --- | --- | --- | --- | --- | --- |--- |
| Naponta összegyűjtött adatmennyiség |500 MB<sup>1</sup> |None |None | Nincs | None | Nincs
| Adatmegőrzés időtartama |7 nap |1 hónap |12 hónap | 1 hónap<sup>2</sup> | 1 hónap<sup>2</sup>| 1 hónap<sup>2</sup>|

<sup>1</sup> Ha az ügyfél eléri az 500 MB-os napi adatátviteli korlátot, az adatelemzés leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú.

<sup>2</sup> az Adatmegőrzés időtartama a különálló, OMS és / GB díjszabások 730 nap növelhető.

| Kategória | Korlátok | Megjegyzések
| --- | --- | --- |
| Data Collector API | Az egyedi közzétételek maximális mérete 30 MB<br>A mezőértékek maximális mérete 32 KB | A nagyobb mennyiségeket bontsa több közzétételre<br>A 32 KB-nál hosszabb mezők csonkolva lesznek. |
| Keresési API | 5000 visszaadott rekord a nem összesített adatok esetében<br>500 000 rekord az összesített adatok esetében | Az összesített adatok a `measure` parancsot tartalmazó kereséssel lekért adatok
 
