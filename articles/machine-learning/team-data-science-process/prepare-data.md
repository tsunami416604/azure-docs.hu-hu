---
title: "Tisztítsa meg és készítse elő az adatokat az Azure Machine Learning |} Microsoft Docs"
description: "Előre feldolgozzák a, és hogy felkészítse az gépi tanulás adatait."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: bdf659ec-4881-4324-8b9c-747cbfa0c3cd
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: cfaccad0a7d81950d80486dcb0d9e6520deab9b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Az adatok bővített gépi tanulásra való előkészítésének feladatai
Előzetesen feldolgozni, és az adatok tisztítása, amelyek általában kell elvégzése előtt a DataSet adatkészlet nem használható hatékonyan gépi tanulás fontos feladatokat. Nyers adatok gyakran zajos vagy nem megbízható, és előfordulhat, hogy értékek hiányzik. Ilyen adatok használata a modellezési félrevezető eredményeket hozhat létre. Ezeket a feladatokat a csapat adatok tudományos folyamat (TDSP) a részét képezik, és általában hajtsa végre egy kezdeti feltárása a DataSet adatkészlet felderítése, és tervezze meg a szükséges előzetes feldolgozás segítségével. Részletes utasítások a TDSP folyamathoz, lásd: a leírt lépéseket a [Team adatok tudományos folyamat](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Előzetes feldolgozás és a tisztítási feladatok, például az adatok feltárása feladat végrehajthatók széles környezetekben, például az SQL vagy a Hive vagy az Azure Machine Learning Studio és a különböző eszközök és nyelven, például az R vagy Python, attól függően, hol tárolja az adatokat, és hogyan van formázva. Mivel TDSP iteratív ideiglenesek, ezek a feladatok végrehajthatók, különböző a munkafolyamat a folyamat lépéseit.

Ez a cikk be különböző adatfeldolgozási fogalmakat és feladatokat, amelyek előtt vagy után az Azure Machine Learning adatok bevitele végezhető.

Például az adatok feltárása és előzetes feldolgozás Azure Machine Learning studio belül történik, a [előzetesen feldolgozni az adatokat az Azure Machine Learning Studióban](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videó.

## <a name="why-pre-process-and-clean-data"></a>Miért előre feldolgozzák, és adatait?
Valós adatokat különböző forrásokból gyűjtött, és folyamatokat, és tartalmazhat szabálytalanságok, vagy hogy az adatkészlet adatai sérültek. A tipikus data quality problémák merülnek fel a következők:

* **Hiányos**: adatok attribútumot, vagy a hiányzó értékeket tartalmazó hiányzik.
* **Zajos**: adatok hibás rekordok vagy kiugró tartalmaz.
* **Inkonzisztens**: adatok ütköző rekordok vagy azok az eltérések tartalmaz.

Minőségi adatok minőségi prediktív modelleket előfeltétele. "Kimenő szemétgyűjtési a szemétgyűjtési" elkerüléséhez és adatok minőségének javítása, és ezért a teljesítmény modell, rendkívül fontos adatok állapotfigyelő képernyő adatproblémákat korai, és adja meg a megfelelő adatok feldolgozása és tisztítási lépések elvégzéséhez.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Mik azok a néhány tipikus data állapotfigyelő képernyő alkalmazott?
Adatok általános minőségének azt is ellenőrizze, hogy ellenőrzése:

* Hány **rekordok**.
* Hány **attribútumok** (vagy **szolgáltatások**).
* Az attribútum **adattípusok** (névleges, sorszám vagy folyamatos).
* Hány **hiányzó értékek**.
* **-Kód szabályosságának** az adatok.
  * Ha az adatok TSV vagy a fürt megosztott kötetei szolgáltatás, ellenőrizze, hogy az oszlop elválasztóinak és a sor elválasztók megfelelően mindig külön oszlopok és sorok.
  * Ha az adatok HTML vagy XML formátumú, ellenőrizze, hogy az adatok helyes formátumú-e alapján a megfelelő előírásokról.
  * Elemzés is szükség lehet ahhoz, hogy a strukturált információk kinyerése félig strukturált vagy strukturálatlan adatokon.
* **Inkonzisztens rekordok**. Ellenőrizze, hogy értékek használata engedélyezett. például ha az adatokban student GPA, ellenőrizze, hogy a kijelölt tartományban van a GPA tegyük fel például 0 ~ 4.

Ha megtalálta az adatokat, problémái **lépések feldolgozása** van szükség, amelyek gyakran magában foglalja a hiányzó értékeket, az adatok normalizálási, diszkretizálási tisztítás, távolítsa el vagy cserélje le a szöveget feldolgozási beágyazott karakterek, ami hatással lehet a adatok igazításának, vegyes adattípusok közös mezők és mások.

**Az Azure Machine Learning formátumú táblázatos adatokat felhasználva**.  Ha az adatok már táblázatos formában, előtti adatfeldolgozási közvetlenül az Azure Machine Learning a Machine Learning Studio hajtható végre.  Ha adatok nem táblázatos, XML, az szóbeli elemzése lehet szükség ahhoz, hogy az adatok átalakítása táblázatos formában.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Mik azok az adatok előzetes feldolgozás jelentős feladatokat?
* **Adatok tisztítása**: Töltse ki vagy hiányzó értékek észlelése és zajos adatok és kiugró.
* **Adatok átalakítása**: normalizálása az adatok dimenziók és a zaj csökkentése érdekében.
* **Adatok csökkentési**: rekordok vagy könnyebben adatkezeléssel attribútumait.
* **Adatok diszkretizálási**: folytonos attribútumok átalakítása könnyű használatra kategorikus attribútumok bizonyos machine learning metódusával.
* **Szöveg tisztítás**: távolítsa el a beágyazott karaktereket, így előfordulhat, hogy az adatok hibás illesztés hibákat, például tabulátorral tagolt adatfájl, a beágyazott lapjaira Embedded új sort, amelyben a megszakadhat a rekordok stb.

Az alábbi részek adatfeldolgozási lépés néhány.

## <a name="how-to-deal-with-missing-values"></a>Hogyan kell foglalkozni a hiányzó értékeket?
Hiányzó értékek kezelésére, érdemes először a probléma meghatározásához az az oka a hiányzó értékeket, jobb leíró. Hiányzó érték kezelése tipikus megoldások a következők:

* **Törlés**: hiányzó értéket tartalmazó rekordok eltávolítása
* **Üres helyettesítő**: hiányzó értékek cserélje le egy üres értéket: például azt, *ismeretlen* kategorikus vagy 0 numerikus értékeket.
* **Helyettesítés jelenti**: Ha a hiányzó adatok numerikus, cserélje le a hiányzó értékeket a középérték.
* **Gyakran használják a helyettesítés**: Ha a hiányzó adatok kategorikus, cserélje le a hiányzó értékeket a leggyakoribb elem
* **Regressziós helyettesítés**: egy regressziós módszerrel cserélje le a hiányzó értékeket közleményében szerepelt értékekkel.  

## <a name="how-to-normalize-data"></a>Hogyan optimalizálására adatokat?
Adatok normalizálási újra arányosan numerikus értékeket a megadott tartományon. Népszerű adatok normalizálási módszerek a következők:

* **Minimális-maximális normalizálási**: lineárisan széles átalakítására, 0 és 1, ahol a minimális értéke 0 és maximális értékének 1-re van méretezhető közötti fel.
* **Z-pontszám normalizálási**: adatok és szórásnál alapuló méretezési: az adatok és a középérték közötti különbség a szórás osztás.
* **Decimális skálázás**: az adatok méretezni a tizedesjel attribútumérték áthelyezésével.  

## <a name="how-to-discretize-data"></a>Hogyan diszkretizálásához adatokat?
Adatok folyamatos érték átalakítása névleges attribútumot, vagy az intervallumok által is diszkretizálható. Bizonyos értelemben az ezzel a következők:

* **A Dobozolás egyenlő szélességű**: az összes lehetséges értékek attribútum felosztani N csoportok azonos méretűnek és rendelhet hozzá értéket, amely egy van bin számát.
* **A Dobozolás egyenlő magasságú**: felosztani minden lehetséges értékek attribútum N csoportok, annyi példányt tartalmazó, majd rendelhet hozzá értéket, amely egy van bin számát.  

## <a name="how-to-reduce-data"></a>Hogyan csökkenthető a adatokat?
A könnyebb adatkezelési adatméret csökkentése különböző módszer áll rendelkezésre. Attól függően, hogy az adatok mérete és a tartomány az alábbi eljárások alkalmazhatók:

* **Jegyezze fel a mintavétel**: a rekordok Sample, és csak az adatok közül választhat a reprezentatív részhalmazát.
* **Mintavételi attribútum**: válassza ki az egyik legfontosabb attribútumok csak egy részét az adatokból.  
* **Összesítési**: az adatok felosztani a csoportok és a számokat az egyes csoportok tárolására. Egy étterem lánc az elmúlt 20 évben napi bevétel számát például összesíthetők havi bevétel adatok méretének csökkentése érdekében.  

## <a name="how-to-clean-text-data"></a>Hogyan szöveg adatait?
**Táblázatos adatok szövegmezők** oszlopok igazítás és/vagy rekord határok befolyásoló karaktereket tartalmazhatnak. Például lapok ágyazva egy tabulátorral tagolt fájl OK oszlop hibás illesztés hibákat, és a beágyazott új sor karaktereket törés rekord sorokat. Helytelen szöveg kódolási kezelési szöveg írás/olvasás közben információk elvesztését, véletlen bevezetése olvashatatlan karaktereket, például null értékeket, és előfordulhat, hogy is befolyásolják szöveg elemzése vezet. Gondos elemzése és -szerkesztő lehet szükség ahhoz, hogy a szövegmezők megfelelő igazítás és/vagy a szöveg strukturálatlan és félig strukturált adatok kivonat strukturált adatok törlése.

**Az adatok feltárása** egy korai képet kaphat az adatokat biztosít. Ezzel a lépéssel adatproblémákat számos fedetlen lehet, és megfelelő módszerek alkalmazhatók hárítsa el ezeket a problémákat.  Fontos, mint mi a probléma forrását, és hogyan a probléma be kérdéseket. Ez segít eldöntheti, hogy a az adatok feldolgozása lépésekre, amelyek kell tenni azok megoldása. Milyen típusú insights egy szándékozik az adatok származik adatfeldolgozási részéről az erőfeszítés rangsorolására is használható.

## <a name="references"></a>Referencia
> *Adatbányászat: Elvekről és technikákról*, harmadik Edition Morgan Kaufmann 2011 Jiawei Han, Micheline Kamber és Jian Pei
> 
> 

