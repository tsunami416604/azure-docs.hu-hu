---
title: "Adatgyűjtést és a csoport az tudományos folyamata életciklus - Azure szakasza ismertetése |} Microsoft Docs"
description: "A célok, a feladatok és a termékek esetében az adatgyűjtést és a adattudomány projektek állapotának ismertetése"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: bfdc6339d8247f3acdf3b7797035e155ef0c1fd1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

Ez a cikk ismerteti a célokat, feladatok és az adatgyűjtést és ismertetése szakasza a csapat adatok tudományos folyamat (TDSP) társított termékek esetében. Ez a folyamat egy ajánlott életciklussal, amely segítségével a adattudomány projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

   1. **Üzleti ismertetése**
   2. **Adatok megszerzését és ismertetése**
   3. **Modellezési**
   4. **Üzembe helyezés**
   5. **Ügyfelek**

A TDSP életciklus vizuális ábrázolását itt található: 

![TDSP életciklusa](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Létrehoznak egy tiszta, kiváló minőségű adatok amelyek kapcsolatát a cél változók értendő. Az adatkészlet keresse meg a megfelelő analytics környezetben, készen áll a modell.
* Az adatok feldolgozási folyamat, amely frissíti, és az adatok rendszeresen pontszámaihoz megoldási fejlesztéséhez.

## <a name="how-to-do-it"></a>Menete
Az ebben a szakaszban leírt három fő feladat van:

   * **Az adatok** a cél elemzési környezetbe.
   * **Az adatokba** annak megállapításához, hogy a data quality a kérdés megválaszolásához megfelelő. 
   * **Állítson be egy adatok folyamat** új vagy rendszeresen pontozása céljából frissítette az adatokat.

### <a name="ingest-the-data"></a>Az adatok
Helyezze át az adatokat az adatforrás helyének analytics műveletek, például a képzés és előrejelzéseket futtató célhelyeket állítsa be a folyamatot. Technikai részletek és az adatokat a különböző Azure data services rendszerrel azon beállítások: [adatok betöltése az analytics tárolási környezeteket](ingest-data.md). 

### <a name="explore-the-data"></a>Az adatok vizsgálata
A modell betanításához előtt az adatok egy hang ismertetése kifejlesztésére lesz szükség. Valós adatkészletek gyakran zajos, értékek hiányoznak, vagy más eltérések állomással rendelkezzenek. Adatainak összefoglalója és a képi megjelenítés segítségével az adatok minőségének naplózni, és adja meg az adatok feldolgozására, ahhoz, hogy készen áll a modellezési szükséges információkat. Ez a folyamat gyakran ismétlődő.

TDSP biztosít nevű automatizált segédprogram [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), megjelenítheti az adatokat, és készítse elő az adatok összesítő jelentések segítségével. Azt javasoljuk, hogy először a kezdeti adatok ismertetése az interaktív módon nem kódolási fejlesztése érdekében adatokba IDEAR indítása. Majd az adatok feltárása és a képi megjelenítéshez tartozó egyéni kódot is írhat. Az adatok tisztítása útmutatóért lásd: [készíti elő az adatok továbbfejlesztett feladatok gépi tanulás](prepare-data.md).  

Miután elégedett a kitisztítanak adatok minőségének, a következő lépéssel jobb megértése érdekében a mintákat, amelyek az adatok rejlő. Ennek segítségével válassza ki, és a cél egy megfelelő prediktív modellek fejlesztése. Ellenőrizze, hogy bizonyító adatok milyen mértékben csatlakoztatva az adatok nem a cél. Ellenőrizze, hogy van-e elegendő adatot előre a modellezési lépések együtt mozognak. Ebben az esetben a folyamat be nem gyakran ismétlődő. Szükség lehet a pontosabb vagy több megfelelő adataival, hogy választhasson kezdetben az előző szakaszban azonosított adatkészlet az új adatforrások keresése. 

### <a name="set-up-a-data-pipeline"></a>Állítson be egy adatok folyamat
A kezdeti adatfeldolgozást és az adatok tisztítása kívül általában kell állítson be olyan folyamatot, amely új adatok pontozása vagy egy folyamatban lévő tanulási folyamat részeként rendszeresen adatok frissítése. Ehhez a adatok folyamat vagy munkafolyamat beállítása. A [tárolt adatok mozgatása a helyszíni SQL Server-példány az Azure SQL Database az Azure Data Factoryvel](move-sql-azure-adf.md) cikk ad arról, hogyan állíthatja be az adatcsatorna [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Ebben a szakaszban egy, a adatok folyamatának megoldásarchitektúra fejlesztéséhez. A folyamat a következő szakasza az adatok tudományos projekt párhuzamosan fejleszt. Attól függően, hogy az üzleti igényeknek és a meglévő rendszerek, amelybe ez a megoldás integrálva van a korlátozásait a folyamat a következők egyike lehet: 

   * Batch-alapú
   * Adatfolyam- vagy a valós idejű 
   * A hibrid 

## <a name="artifacts"></a>Összetevők
A következők a céljai legyenek ebben a szakaszban:

   * [Minőségi jelentés](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Ez a jelentés tartalmaz ezekkel az adatokat, minden egyes attribútum és a cél, kapcsolatai változó rangsorolási és még sok más. A [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) TDSP részét gyorsan hozhat létre, ez a jelentés táblázatos adatok meghiúsító, például a CSV-fájl vagy egy relációs tábla a megadott eszköz. 
   * **Megoldás architektúrája**: A megoldásarchitektúra lehet egy diagramot, vagy az adatok leírása a következő feldolgozási sorban, amikor pontozási futtatásával vagy az új adatok előrejelzéseket a modell létrehozása után. A kimenetátirányítási újratanítása a modellt új adatok alapján is tartalmaz. A dokumentum tárolása a [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory a TDSP directory struktúra sablon használatakor.
   * **Ellenőrzőpont döntési**: teljes körű szolgáltatásokat termékgondozó csoportja és a modell létrehozásának megkezdése előtt is újra kiértékelje a projekt meghatározásához. a várt értéket elméletben a minden esetben hasznos, a folytatáshoz elegendő. Lehet, például lehet készen áll a folytatáshoz további adatokat gyűjteni, vagy szakítsa a projekt, az adatok nem létezik a kérdésre válaszolnia kell.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a TDSP életciklusát lépésre mutató hivatkozásokat:

   1. [Üzleti ismertetése](lifecycle-business-understanding.md)
   2. [Adatok megszerzését és ismertetése](lifecycle-data.md)
   3. [Modellezési](lifecycle-modeling.md)
   4. [Üzembe helyezés](lifecycle-deployment.md)
   5. [Ügyfelek](lifecycle-acceptance.md)

Teljes-végpontok forgatókönyvek, amelyek azt mutatják, meghatározott forgatókönyvek esetén a folyamat lépései a Microsoft biztosítja. A [példa forgatókönyvek](walkthroughs.md) a cikk ismerteti a miniatűr leírások és hivatkozások forgatókönyvek listája. A forgatókönyvek bemutatják, hogyan lehet a felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

Hogyan hajthat végre a lépéseket az Azure Machine Learning Studio használó TDSPs példákért lásd [a TDSP használja az Azure Machine Learning segítségével](http://aka.ms/datascienceprocess).
