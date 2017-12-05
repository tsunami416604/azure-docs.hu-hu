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
ms.openlocfilehash: 51a1b65446a1a0db93c21378f156b608a38ef817
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
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
- Maximum
- Átlag
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


## <a name="pattern-frequency"></a>Minta gyakorisága 

Ez inspector a kijelölt oszlopban karakterlánc minták listáját tartalmazza. A minta egy reguláris kifejezés szintaxisa például értékét. A minta rámutató a példákat mutat a minta értékeiből. A mintákat, valamint a hozzávetőleges coverages százalékos tekintetében is látható.

![A minta inspector képe](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Beállítások
- A felső érték
- Csökkenő
- Halo megjelenítése

### <a name="actions"></a>Műveletek
Ez inspector támogatja a szűrést, a megjelenő minták alapján. Nyomja meg a **Ctrl** billentyűt, majd válassza ki a kitöltött sávok mintát inspector. Ezután alkalmazza az előzőekben leírt szűrők. A felhasználó acion eredményeként egy speciális szűrési lépés jelenik meg. Tekintse meg, és módosíthatja a Python kódgenerálás meghívása a speciális szűrési lépés szerkesztése lehetőséget.
