---
title: Adatgyűjtés és adatértelmezés a csoportos adatelemzési folyamat
description: A célok, feladatok és az adatgyűjtés és a data-adatelemzési projektek ismertetése szakasza a termékek
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c4acb3b779821193949c69d440edaafec5e3d803
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474054"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Adatgyűjtés és a csoportos adatelemzési folyamat ismertetése szakasza

Ez a cikk ismerteti a célokat, feladatok és az adatok beszerzése és megértése fázis, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Az üzleti igények felmérése**
   2. **Adatgyűjtés és adatértelmezés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Felhasználói elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* A célként megadott változókat, amelyek kapcsolatát érthető tiszta, magas minőségű adatkészlet létrehozásához. Keresse meg az adatkészlet a megfelelő elemzési környezet így modell készen áll.
* Egy megoldás architektúrája, amely frissíti, és az adatokat rendszeresen pontszámmodell adatfolyamat fejleszthet.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban leírt három fő feladat:

   * **Az adatok betöltését** a cél elemzési környezetbe.
   * **Az adatok** annak megállapításához, hogy a data quality megfelelő a választ a kérdésre. 
   * **Egy adatfolyamat beállítása** pontszámot rendelni az új vagy rendszeresen frissülnek az adatok.

### <a name="ingest-the-data"></a>Az adatok betöltése
Állítsa be a folyamat az adatok áthelyezése az adatforrás helyének a célhelyek analytics műveletek, például a képzés és az előrejelzések futtatásához. Technikai részletek és a lehetőségek, hogyan helyezheti át az adatokat a különböző Azure-adatszolgáltatások, lásd: [adatok betöltése a tárolási környezetekbe elemzés céljából](ingest-data.md). 

### <a name="explore-the-data"></a>Az adatok vizsgálata
Mielőtt-modellek betanításához az adatait eredményes ismeretekkel fejlesztéshez szükséges. Valós adatkészletek általában zajos, hiányoznak az értékek, vagy számos egyéb eltérések rendelkezik. Adatok összegzési és vizualizációs segítségével naplózása az adatok minőségét, és adja meg az adatok feldolgozására, mielőtt készen áll a modellezési szükséges információkat. Ez a folyamat gyakran meghatározási.

TDSP biztosít nevű automatikus segédprogram [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), megjelenítheti az adatokat, és készítse elő az adatokat összegző jelentéseket. Azt javasoljuk, hogy először a IDEAR, először az adatok kezdeti adatok megértését, interaktív módon kódolás nélkül fejlesztéséhez. Majd az adatok feltárása és képi megjelenítés egyéni kódot is írhat. Az adatok tisztítása, tekintse át [feladatok készíti elő az adatok bővített gépi tanulás](prepare-data.md).  

Ha elégedett a tisztított adatok minőségét, a következő lépés az a, amelyek az adatokban rejlő mintázatok jobb megértéséhez. Ennek segítségével válassza ki, és a cél egy megfelelő prediktív modellek fejlesztése. Tekintse meg az, hogy hogyan bizonyíték jó csatlakozású az adatokat, hogy a cél. Majd vizsgálja meg, hogy van-e elegendő adatot előrelépés a további teendőkről modellezéshez. Ez a folyamat újra, gyakran iteratív. Előfordulhat, hogy meg kell keresnie és pontosabb több megfelelő adataival, mivel megvédi a az adatkészlet kezdetben az előző szakaszban azonosított új adatforrásokat. 

### <a name="set-up-a-data-pipeline"></a>Egy adatfolyamat beállítása
A kezdeti betöltési és az adatok tisztítása kívül általában kell beállítani egy folyamatot, hogy az új adatok pontozásához, vagy frissítheti az adatokat rendszeresen egy folyamatban lévő tanulási folyamat részeként. Ehhez adatok folyamattá vagy munkafolyamattá beállítása szerint. A [adatok áthelyezése a helyszíni SQL Server-példány az Azure SQL Database az Azure Data Factoryvel](move-sql-azure-adf.md) cikk biztosít a példa bemutatja, hogyan állíthatja be a folyamat [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Ebben a szakaszban egy adatfolyamat megoldás architektúrája, fejlesztése. A folyamat következő szakaszában az adatelemzési projektjéhez párhuzamos fejleszt. Attól függően, és az üzleti igényeknek megfelelően, amelybe ez a megoldás integrálva van a meglévő rendszerek korlátait a folyamat a következő egyike lehet: 

   * Batch-alapú
   * Streamelés és valós idejű 
   * Hibrid 

## <a name="artifacts"></a>Összetevők
Az alábbiakban a termékek ebben a szakaszban:

   * [Adatok minőségi jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Ez a jelentés összefoglaló adatokat, mindegyik attribútum és a cél, a kapcsolatokat tartalmaz változó rangsorolás és egyebek. A [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) TDSP részét is gyorsan létrehozhatja a jelentés minden táblázatos adatkészlet, például a CSV-fájl vagy egy relációs tábla megadott eszköz. 
   * **Megoldásarchitektúra**: A megoldásarchitektúra lehet egy diagram vagy a leírást a pontozási futtatásához használt adatfolyamat vagy előrejelzéseket új adatokat a modell létrehozása után. A folyamat újratanítása a modellt az új adatok alapján is tartalmaz. A dokumentum a Store a [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) könyvtár a TDSP directory struktúra sablon használatakor.
   * **Ellenőrzőpont döntési**: A szolgáltatás teljes termékgondozó csoportja és a modell létrehozásának megkezdése előtt, a projekt annak megállapításához, hogy a várt érték elméletben a minden esetben hasznos, a folytatáshoz elegendő is kiértékeli. Előfordulhat például lehet készen áll a folytatáshoz kell további adatok gyűjtése, illetve a projekt abandon, mert az adatok nem létezik a választ a kérdésre.

## <a name="next-steps"></a>További lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Az üzleti igények felmérése](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és adatértelmezés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Felhasználói elfogadás](lifecycle-acceptance.md)

Azt adja meg a teljes, végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a folyamat összes lépését. A [példa forgatókönyvek](walkthroughs.md) a cikk a forgatókönyvek miniatűr leírások és hivatkozások listáját tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

Hogyan hajtható végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TSDP használata az Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
