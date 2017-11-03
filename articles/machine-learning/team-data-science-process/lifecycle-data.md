---
title: "Adatok megszerzését és Team adatok tudományos folyamat életciklus - Azure szakasza ismertetése |} Microsoft Docs"
description: "A célok, a feladatok és a termékek esetében az adatgyűjtést és tudományos adatok projektjeikbe ismertetése szakasza."
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
ms.openlocfilehash: eea3c357ebf6ad920c0ddebdb979aa07aece4794
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

Ez a témakör bemutatja a célok, feladatok, és a társított termékek esetében a **adatgyűjtést és ismertetése szakasz** az Team tudományos folyamat. Ez a folyamat egy ajánlott életciklussal, amely segítségével az adatok tudományos projektek struktúra biztosítja. Az életciklus, projektek általában hajtható végre, gyakran ismételt fő szakaszait vázolja fel:

* **Üzleti ismertetése**
* **Adatok megszerzését és ismertetése**
* **Modellezési**
* **Üzembe helyezés**
* **Ügyfelek**

Íme egy vizuális ábrázolását a **Team adatok tudományos folyamat életciklus**. 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Célok
* Tiszta, kiváló minőségű dataset amelynek kapcsolatok a cél változók tisztában vannak a megfelelő analytics környezetben, készen áll a modell található.
* Az adatok folyamatának frissítse és adatok pontozása rendszeresen megoldási fejlesztettek ki.

## <a name="how-to-do-it"></a>Menete
Az ebben a szakaszban leírt három fő feladat van:

* **Az adatok** a cél elemzési környezetbe.
* **Az adatokba** annak megállapításához, hogy a data quality a kérdés megválaszolásához megfelelő. 
* **Állítson be egy adatok folyamat** új vagy rendszeresen pontozása céljából frissítette az adatokat.

### <a name="21-ingest-the-data"></a>2.1 viszi be az adatokat
Állítsa be a folyamat az adatok áthelyezése Forráshelyek analytics műveletek hova képzési célhelyeket és előrejelzéseket hajthatnak végre. Technikai részletei és a különböző Azure data services rendszerrel ehhez azon beállítások: [adatok betöltése az analytics tárolási környezeteket](ingest-data.md). 

### <a name="22-explore-the-data"></a>2.2 ásna az adatokban
A modell betanításához előtt az adatok egy hang ismertetése kifejlesztésére lesz szükség. Valós adatkészletek általában zajos vagy értékek hiányzik, vagy más eltérések állomással rendelkezzenek. Adatainak összefoglalója és a képi megjelenítés segítségével az adatok minőségének naplózási, és adja meg az adatok feldolgozására, ahhoz, hogy készen áll a modellezési szükséges információkat. Ez a folyamat gyakran ismétlődő.

TDSP biztosít egy nevezik automatizált segédprogram [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) megjelenítheti az adatokat, és készítse elő az adatok összesítő jelentések segítségével. Azt javasoljuk, hogy először a kezdeti adatok ismertetése az interaktív módon nem kódolási fejlesztése és az adatok feltárása és a képi megjelenítés egyéni kód írását, majd az adatokba IDEAR kezdve. Az adatok tisztítása útmutatóért lásd: [készíti elő az adatok továbbfejlesztett feladatok gépi tanulás](prepare-data.md).  

Ha elégedett a kitisztítanak adatok minőségének, a következő lépéssel jobb megértése érdekében jellemzőek, amelyek segítenek az adatok mintázatokat válassza ki, és a cél egy megfelelő prediktív modellek fejlesztése. Keresse meg a bizonyító adatok számára az adatok milyen mértékben csatlakoztatva van a cél, és hogy nincs elegendő adat előre a modellezési lépések együtt mozognak. Ebben az esetben a folyamat be nem gyakran ismétlődő. Szükség lehet a pontosabb vagy több megfelelő adataival, hogy az adatkészlet kezdetben az előző szakaszban azonosított kiegészítheti az új adatforrások keresése.  

### <a name="23-set-up-a-data-pipeline"></a>2.3 adatok adatcsatorna beállítása
A kezdeti adatfeldolgozást és az adatok tisztítása kívül általában kell állítson be olyan folyamatot, amely új adatok pontozása vagy egy folyamatban lévő tanulási folyamat részeként rendszeresen adatok frissítése. Ezt megteheti a adatok folyamat vagy munkafolyamat beállításával. Íme egy [példa](move-sql-azure-adf.md) , hogyan állíthat be az adatcsatorna [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Az adatok feldolgozási folyamat megoldási fejlesztése ebben a szakaszban. A folyamat a következő szakaszokra tudományos adatok projekt párhuzamosan is fejlesztése. A feldolgozási sor batch-alapú vagy streaming/real-egyszeri vagy lehet a hibrid attól függően, hogy az üzleti igényeknek és a meglévő rendszerek, amelybe ez a megoldás integrálva van a korlátozásait. 

## <a name="artifacts"></a>Összetevők
A következők a céljai legyenek ebben a szakaszban.

* [**Minőségi jelentés**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Ez a jelentés ezekkel az adatokat, minden egyes attribútum és a cél, közötti kapcsolatokat tartalmaz változó rangsorolási stb. A [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) TDSP részét gyorsan hozhat létre, ez a jelentés például egy CSV-fájl vagy egy relációs tábla bármely táblázatos adatkészlethez megadott eszköz. 
* **Megoldás architektúrája**: Ez lehet egy diagram vagy a leírást a pontozási futtatásához használt adatok folyamat vagy az új adatok előrejelzéseket a modell létrehozása után. A kimenetátirányítási újratanítása a modellt új adatok alapján is tartalmaz. A dokumentum tárolása a [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory a TDSP directory struktúra sablon használatakor.
* **Ellenőrzőpont döntési**: a szolgáltatás teljes termékgondozó csoportja és a modell létrehozásának megkezdése előtt is újra kiértékelje a projekt meghatározásához. a várt értéket elméletben a minden esetben hasznos, a folytatáshoz elegendő. Lehet, például lehet készen áll a folytatáshoz további adatokat gyűjteni, vagy szakítsa a projekt, az adatok nem létezik a kérdésre válaszolnia kell.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban az életciklus az Team tudományos folyamat minden lépése mutató hivatkozásokat:

* [1. Üzleti ismertetése](lifecycle-business-understanding.md)
* [2. Adatok megszerzését és ismertetése](lifecycle-data.md)
* [3. Modellezési](lifecycle-modeling.md)
* [4. Központi telepítés](lifecycle-deployment.md)
* [5. Ügyfelek](lifecycle-acceptance.md)

Végpont forgatókönyvek, amelyek azt mutatják, a folyamat lépései teljes **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) témakör. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása.  

Az adatok tudományos folyamatban lépések végrehajtása, amelyek használják az Azure Machine Learning Studióban, tekintse meg a [Azure ML](http://aka.ms/datascienceprocess) képzési.