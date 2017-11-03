---
title: "Támogatott ellenőrök érhető el az Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum érhető el az Azure Machine Learning adatok előkészítése ellenőrök teljes listáját tartalmazza."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 35d7c04f245e93d8cc795dca7c01c2bab5a14eb8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Az Azure Machine Learning data előkészítése Preview ellenőrök támogatott
Ez a dokumentum ismerteti az előzetes verzióban elérhető ellenőrök készletét.

## <a name="the-halo-effect"></a>A halo hatása 
Néhány ellenőrök halo hatásának támogatja. Erre két különböző színek használatával mutatja azonnal a módosítást vizuálisan átalakító. A szürke a legújabb átalakítás előtti értékét jelöli, és a kék jeleníti meg az aktuális értékét. Ez a hatás engedélyezve, és le van tiltva, a beállítások menüben.

## <a name="graphical-filtering"></a>Grafikus szűrése 
Az ellenőrök egyesek adatok szűrését a inspector használatával szerkesztésére. Magában foglalja a inspector használatával szerkesztésére, grafikus elemek kijelölése, és a inspector ablak jobb felső részén eszköztár segítségével szűrni a bejövő vagy kimenő kiválasztott értékét. 

## <a name="column-statistics"></a>Oszlop statisztikai adatainak
Numerikus oszlopoknál a inspector számos különböző statisztikák az oszlop kapcsolatban. Statisztika a következő mérési tartalmazza: 
- Minimális
- Alacsonyabb KVARTILIS
- Középérték
- Felső KVARTILIS
- Maximális
- Átlagos
- Szórás


### <a name="options"></a>Beállítások 
- None

## <a name="histogram"></a>Hisztogram 
Kiszámítja, és egyetlen numerikus oszlopban hisztogram jeleníti meg. Az alapértelmezett számú gyűjtőbe Scott szabály használatával történik. Azonban a szabály a beállítások segítségével felülbírálható.

Ez Inspector halo hatásának támogatja.


### <a name="options"></a>Beállítások
- Gyűjtők minimális száma (vonatkozik, még ha alapértelmezett bucketing be van jelölve)
- Alapértelmezett számú gyűjtőbe (Scott szabály) 
- Halo megjelenítése
- Kernel sűrűség rajzot átfedő (Gauss kernel) 


### <a name="actions"></a>Műveletek
Ez inspector támogatja a szűrést, gyűjtők, amelyek magukban foglalhatják egyetlen vagy többszörös kiválasztási gyűjtők keresztül. Az előzőleg bemutatott szűrőket alkalmazhat.

## <a name="value-counts"></a>Érték száma
Ez inspector jeleníti a jelenleg kiválasztott oszlop értékeinek gyakoriságát. Alapértelmezés szerint megjelenik a felső hat értékekre van. A korlát bármennyi, azonban módosíthatja. Beállíthatja a képernyőt a felső helyett a lista aljáról száma is. Ez inspector halo hatásának támogatja.

### <a name="options"></a>Beállítások 
- A felső érték
- Csökkenő
- Null vagy hibaértéket tartalmazza
- Halo megjelenítése


### <a name="actions"></a>Műveletek 
Ez inspector támogatja a szűrést, sávok, amelyek magukban foglalhatják egyetlen vagy többszörös kiválasztási sávok keresztül. Az előzőleg bemutatott szűrőket alkalmazhat.

## <a name="box-plot"></a>Dobozdiagram 
Egy bajusz Dobozdiagram egy numerikus oszlopot.

### <a name="options"></a>Beállítások 
- Oszlop szerint

## <a name="scatter-plot"></a>Pontdiagram rajzot
Két numerikus oszlopoknál pont rajzot. Az adatok a teljesítményre vonatkozó megfontolásból lefelé-mintát. A beállítások felülbírálhatók a minta mérete.

### <a name="options"></a>Beállítások  
- Az x tengely oszlop
- Y tengely oszlop
- Minta mérete
- Oszlop szerint


## <a name="time-series"></a>A Time series
Egy vonaldiagramot idő tájékoztatáshoz az x tengelyen.

### <a name="options"></a>Beállítások
- Oszlop dátuma
- Numerikus oszlopot
- Minta mérete


### <a name="actions"></a>Műveletek
Ez inspector támogatja a szűrést, egy kattintással és húzással jelölje be a módszerrel a válasszon ki egy tartományt a grafikonon. Kijelölés befejezése után alkalmazza az előzőekben leírt szűrők.


## <a name="map"></a>Térkép 
Olyan térképet pontokkal ábrázolt, feltéve, hogy a szélességi és hosszúsági van megadva. A földrajzi hosszúság először meg kell adni.

### <a name="options"></a>Beállítások
- A földrajzi hosszúság oszlop
- A földrajzi hosszúság értéke oszlop
- Csoportosítás
- Oszlop szerint


### <a name="actions"></a>Műveletek
Ez inspector támogatja a szűrést, pont kiválasztása a térképen keresztül. Nyomja meg a **Ctrl** billentyűt, majd és az egérrel a pontok körül négyzet kialakításához. Ezután alkalmazza az előzőekben leírt szűrők.

A leképezés csak a lehetséges pontok megjelenítése billentyűkombináció lenyomásával gyorsan méretét a **E** a térkép bal oldalán.
