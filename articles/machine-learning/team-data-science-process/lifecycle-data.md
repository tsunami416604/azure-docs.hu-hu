---
title: Adatgyűjtés és adatértelmezés a csoportos adatelemzési folyamat
description: A célok, feladatok és az adatgyűjtés és a data-adatelemzési projektek ismertetése szakasza a termékek
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720503"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Adatgyűjtés és a csoportos adatelemzési folyamat ismertetése szakasza

Ez a cikk ismerteti a célokat, feladatok és az adatok beszerzése és megértése fázis, a csoportos adatelemzési folyamat (TDSP) társított le. Ez a folyamat egy ajánlott életciklussal, amely használatával a data-adatelemzési projektek strukturálása biztosít. Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

   1. **Üzleti ismeretek**
   2. **Adatgyűjtés és-megértés**
   3. **Modellezés**
   4. **Üzembe helyezés**
   5. **Ügyfél-elfogadás**

A TDSP életciklus ábrázolása a következő: 

![TDSP-életciklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* A célként megadott változókat, amelyek kapcsolatát érthető tiszta, magas minőségű adatkészlet létrehozásához. Keresse meg az adatkészlet a megfelelő elemzési környezet így modell készen áll.
* Egy megoldás architektúrája, amely frissíti, és az adatokat rendszeresen pontszámmodell adatfolyamat fejleszthet.

## <a name="how-to-do-it"></a>Megtudhatja, hogyan teheti
Nincsenek az ebben a szakaszban leírt három fő feladat:

   * **Az adatgyűjtést** a cél elemzési környezetbe.
   * **Fedezze fel az** adatmennyiséget, és állapítsa meg, hogy az adatminőség megfelelő-e a kérdés megválaszolásához. 
   * **Állítson be egy adatfolyamatot** az új vagy rendszeresen frissített adatértékek kiértékeléséhez.

### <a name="ingest-the-data"></a>Az adatok betöltése
Állítsa be a folyamat az adatok áthelyezése az adatforrás helyének a célhelyek analytics műveletek, például a képzés és az előrejelzések futtatásához. Az adatok különböző Azure-adatszolgáltatásokkal való áthelyezésével kapcsolatos technikai részletekért lásd: [adatok betöltése tárolási környezetbe elemzés céljából](ingest-data.md). 

### <a name="explore-the-data"></a>Az adatok vizsgálata
Mielőtt-modellek betanításához az adatait eredményes ismeretekkel fejlesztéshez szükséges. Valós adatkészletek általában zajos, hiányoznak az értékek, vagy számos egyéb eltérések rendelkezik. Adatok összegzési és vizualizációs segítségével naplózása az adatok minőségét, és adja meg az adatok feldolgozására, mielőtt készen áll a modellezési szükséges információkat. Ez a folyamat gyakran meghatározási.

A TDSP egy [ideaer](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)nevű automatizált segédprogramot biztosít az adatmegjelenítéshez és az adatösszegző jelentések előkészítéséhez. Azt javasoljuk, hogy először a IDEAR, először az adatok kezdeti adatok megértését, interaktív módon kódolás nélkül fejlesztéséhez. Majd az adatok feltárása és képi megjelenítés egyéni kódot is írhat. Az adatok tisztításával kapcsolatos útmutatásért lásd: [feladatok az adatok előkészítéséhez a továbbfejlesztett gépi tanuláshoz](prepare-data.md).  

Ha elégedett a tisztított adatok minőségét, a következő lépés az a, amelyek az adatokban rejlő mintázatok jobb megértéséhez. Ez az adatelemzés segít a célnak megfelelő prediktív modell kiválasztásában és fejlesztésében. Tekintse meg az, hogy hogyan bizonyíték jó csatlakozású az adatokat, hogy a cél. Majd vizsgálja meg, hogy van-e elegendő adatot előrelépés a további teendőkről modellezéshez. Ez a folyamat újra, gyakran iteratív. Előfordulhat, hogy meg kell keresnie és pontosabb több megfelelő adataival, mivel megvédi a az adatkészlet kezdetben az előző szakaszban azonosított új adatforrásokat. 

### <a name="set-up-a-data-pipeline"></a>Egy adatfolyamat beállítása
A kezdeti betöltési és az adatok tisztítása kívül általában kell beállítani egy folyamatot, hogy az új adatok pontozásához, vagy frissítheti az adatokat rendszeresen egy folyamatban lévő tanulási folyamat részeként. A pontozás egy adatfolyamattal vagy munkafolyamattal is elvégezhető. Az [adatok áthelyezése egy helyszíni SQL Server-példányból a Azure SQL Databaseba Azure Data Factory](move-sql-azure-adf.md) cikkből megtudhatja, hogyan állíthat be egy folyamatot [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)használatával. 

Ebben a szakaszban egy adatfolyamat megoldás architektúrája, fejlesztése. A folyamat következő szakaszában az adatelemzési projektjéhez párhuzamos fejleszt. Az üzleti igényektől és a meglévő rendszerek korlátaitól függően, amelyekben a megoldás integrálva van, a folyamat az alábbi lehetőségek egyike lehet: 

   * Batch-alapú
   * Streamelés és valós idejű 
   * Hibrid 

## <a name="artifacts"></a>Összetevők
Az alábbiakban a termékek ebben a szakaszban:

   * [Adatminőségi jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Ez a jelentés az adatösszegzéseket, az egyes attribútumok és a cél, a változó rangsorolás és egyéb kapcsolatok közötti kapcsolatokat tartalmazza. A TDSP részeként nyújtott [ideaer](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) eszköz gyorsan létrehozhatja ezt a jelentést bármilyen táblázatos adathalmazon, például egy CSV-fájlban vagy egy kapcsolati táblában. 
   * **Megoldás-architektúra**: a megoldás architektúrája lehet az adatfolyamatok egy diagramja vagy leírása, amellyel a modell létrehozása után az új adatsorokra vonatkozó pontozási vagy előrejelzések futtathatók. A folyamat újratanítása a modellt az új adatok alapján is tartalmaz. Tárolja a dokumentumot a [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) könyvtárában a TDSP Directory-struktúra sablonjának használatakor.
   * **Ellenőrzőpont-döntés**: Mielőtt megkezdené a teljes körű fejlesztést és a modell kialakítását, a projekt újraértékelésével megállapíthatja, hogy a várt érték elegendő-e a folytatáshoz. Előfordulhat például lehet készen áll a folytatáshoz kell további adatok gyűjtése, illetve a projekt abandon, mert az adatok nem létezik a választ a kérdésre.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusának minden lépése mutató hivatkozásokat:

   1. [Üzleti ismeretek](lifecycle-business-understanding.md)
   2. [Adatgyűjtés és-megértés](lifecycle-data.md)
   3. [Modellezés](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfél-elfogadás](lifecycle-acceptance.md)

Teljes körű bemutatókat biztosítunk, amelyek bemutatják a folyamat összes lépését adott forgatókönyvek esetében. A [példákat](walkthroughs.md) bemutató cikk a hivatkozásokat és a miniatűr leírásait tartalmazza. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

A Azure Machine Learning Studiot használó TDSPs lépéseinek végrehajtásával kapcsolatos Példákért lásd: [a TDSP használata Azure Machine learning használatával](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
