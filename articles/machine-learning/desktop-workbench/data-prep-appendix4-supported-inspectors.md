---
title: Támogatott vizsgálók elérhető az Azure Machine Learning adat-előkészítési |} A Microsoft Docs
description: Ez a dokumentum elérhető az Azure Machine Learning adat-előkészítési vizsgálók teljes listáját tartalmazza.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ef5f6f3dc7ae0c555b2afe000b54c443313800f1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645735"
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Támogatott vizsgálók előkészítése az Azure Machine Learning adatelőnézet
Ez a dokumentum ismerteti az ebben az előzetes verzióban elérhető vizsgálók készletét.

## <a name="the-halo-effect"></a>A hatás effektus 
Néhány vizsgálók a hatás effektus támogatja. Ennek a hatásnak két különböző színekkel jelennek meg azonnal a módosítást vizuálisan átalakító használ. A szürke jelenti. a legújabb átalakítás előtti értéke, és a kék megjeleníti az aktuális értéket. Erről is engedélyezve van, és a beállítások menüben le van tiltva.

## <a name="graphical-filtering"></a>Grafikus szűrése 
Néhány a vizsgálók támogatja a szűrést az adatok a vizsgáló-szerkesztő használatával. A vizsgáló az-t egy szerkesztőben magában foglalja a grafikus elemek kiválasztását és az eszköztáron a vizsgáló ablak jobb felső részében segítségével szűrheti és leskálázása a kiválasztott értékek. 

## <a name="column-statistics"></a>Oszlopstatisztika
A numerikus oszlopok a vizsgáló számos különböző stats kapcsolatos oszlop. Statisztikák a következő mérési tartalmazza: 
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
Kiszámítja, és csak egy numerikus oszlop hisztogramja jeleníti meg. Az alapértelmezett gyűjtők száma Scott szabály használatával történik. Azonban a szabály felülbírálható beállításain keresztül.

A vizsgáló a hatás effektus támogatja.


### <a name="options"></a>Beállítások
- Gyűjtők minimális száma (vonatkozik, még ha alapértelmezett bucketing be van jelölve)
- Alapértelmezett számú gyűjtőbe (Scott szabály) 
- Hatás megjelenítése
- Kernel sűrűségű diagram területre (Gauss kernel) 
- Logaritmikus skála használata


### <a name="actions"></a>Műveletek
A vizsgáló támogatja a szűrést, gyűjtők, amelyek magukban foglalhatják az egyszeres vagy többszörös kiválasztási gyűjtők keresztül. Az előzőleg bemutatott szűrőket alkalmazhat.

## <a name="value-counts"></a>Értékek száma lehetőségre
A vizsgáló megadja a jelenleg kijelölt oszlop értékeit tábláját gyakorisága. Az alapértelmezett megjelenítést a felső hat értékekre van. A korlát bármilyen számot azonban módosíthatja. Beállíthatja a megjelenítendő felső helyett a lista aljáról számát is. A vizsgáló a hatás effektus támogatja.

### <a name="options"></a>Beállítások 
- Felső értékek száma
- Csökkenő
- Null vagy hibaértéket belefoglalása
- Hatás megjelenítése
- Logaritmikus skála használata


### <a name="actions"></a>Műveletek 
A vizsgáló támogatja a szűrést, sávok, amelyek magukban foglalhatják az egyszeres vagy többszörös kiválasztási sávok keresztül. Az előzőleg bemutatott szűrőket alkalmazhat.

## <a name="box-plot"></a>Box-diagram 
Numerikus oszlop box kanóc rajzot.

### <a name="options"></a>Beállítások 
- Oszlop szerint

## <a name="scatter-plot"></a>Pont
Pontdiagram két numerikus oszlopokhoz. Az adatok a lefelé mintavételezés a teljesítmény szempontjából. A beállítások felülbírálhatók a mintanagyság.

### <a name="options"></a>Beállítások  
- X tengely oszlop
- Y tengely oszlop
- Mintavételi méret
- Oszlop szerint


## <a name="time-series"></a>A Time series
Egy vonaldiagramot idő tudatosság az x tengelyen.

### <a name="options"></a>Beállítások
- Dátum oszlop
- Numerikus oszlop
- Mintavételi méret


### <a name="actions"></a>Műveletek
A vizsgáló támogatja a szűrést, egy kattintással és húzással jelölje be metódus a grafikonon tartományt keresztül. Miután elvégezte a kijelölést, a korábban leírt szűrőket alkalmazhat.


## <a name="map"></a>Térkép 
Egy térkép a pontokat, amelyeket ábrázolási, feltéve, hogy a szélességi és hosszúsági lett megadva. Szélesség először ki kell választani.

### <a name="options"></a>Beállítások
- Szélesség oszlop
- Hosszúsági oszlop
- A fürtszolgáltatás
- Oszlop szerint


### <a name="actions"></a>Műveletek
A vizsgáló támogatja a szűrést, keresztül pont kiválasztása a térképen. Nyomja le az **Ctrl** key, majd kattintson a és a pontok körüli négyzet kialakításához az egérrel húzza. Ezután alkalmazza a szűrőket az előzőleg ismertetett módon.

Gyorsan méretét a térkép a lehetséges pontok billentyű lenyomásával megjeleníthető az **E** a térkép bal oldalán.


## <a name="pattern-frequency"></a>A minta gyakorisága 

A vizsgáló a kijelölt oszlopban karakterlánc minták listáját tartalmazza. A minták szerepelnek, például a szintaxis egy reguláris kifejezést használ. A mintát viszi példát láthat a minta értékeiből. A minták, valamint a hozzávetőleges coverages százalékos tekintetében is látható.

![A minta vizsgáló képe](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Beállítások
- Felső értékek száma
- Csökkenő
- Hatás megjelenítése

### <a name="actions"></a>Műveletek
A vizsgáló támogatja a szűrést, megjelenített mintáknak megfelelően. Nyomja le az **Ctrl** kulcsát, és válassza ki a tematikus sávok minta vizsgáló. Ezután alkalmazza a szűrőket az előzőleg ismertetett módon. A felhasználó acion eredményeként egy speciális szűrési lépés megjelenik. Megtekintheti és módosíthatja a generált Python-kód szerkesztése lehetőséget a speciális szűrő lépéssel meghívásával.
