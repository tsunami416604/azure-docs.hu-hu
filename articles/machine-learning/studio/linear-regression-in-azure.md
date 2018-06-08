---
title: A gépi tanulás lineáris regressziós alkalmazásával |} Microsoft Docs
description: Az Excel programban, és az Azure Machine Learning Studióban lineáris regressziós modell összehasonlítása
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 162fc96c44db3c92103e12922de14b543daec9bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836225"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Lineáris regresszió használata az Azure Machine Learning termékben
> *Kate Baroni* és *Ben Boatman* vállalati megoldás fejlesztők a Microsoft Data elemzések kiváló Center vannak. Ebben a cikkben leírt tapasztalataikat áttelepítését egy meglévő regressziós elemzés suite Azure Machine Learning segítségével felhőalapú megoldásokhoz. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Cél
A projekt két célok szem előtt tartásával használatába: 

1. A prediktív elemzés segítségével a szervezet havi bevétel leképezések pontosságának javítása 
2. Azure Machine Learning segítségével győződjön meg róla, optimalizálása, sebesség növelése és a skála az eredmények. 

Számos vállalat, például a szervezet végig kell vinnie a havi előrejelzési folyamat bevétel. Az üzleti elemzők kis csoport lett biztosítja Azure Machine Learning segítségével támogatása és az előrejelzési pontosság növeléséhez. A csapat a különböző forrásokból származó adatok gyűjtésére, és az adatok attribútumok fut kapcsolódó szolgáltatások értékesítési előrejelzések kulcsattribútum azonosító statisztikai elemzése révén több hónappal töltött. A következő lépés az adatok az Excelben prototípusának statisztikai regressziós modell megkezdéséhez volt. Az Excel regressziós modell, amely a jelenlegi és a folyamatok előrejelzés pénzügyi lett outperforming volt néhány héten belül. Az alapkonfiguráció előrejelzés eredmény ez vált. 

Azt majd szükség volt a következő lépés a prediktív elemzés áthelyezése Azure Machine Learning segítségével, hogy megtudja, hogyan Machine Learning a prediktív teljesítményre javíthatja.

## <a name="achieving-predictive-performance-parity"></a>Paritás prediktív teljesítmény elérése érdekében
Az első elsőbbséget volt a Machine Learning és Excel regressziós modell paritása eléréséhez. A modell betanítására és tesztelésére adatok ugyanazokat az adatokat, és az adott felosztás tekintve meg akartunk eléréséhez, az Excel és a Machine Learning a prediktív teljesítmény paritása. Eredetileg nem sikerült. Az Excel-modell outperformed a gépi tanulási modell. A sikertelenség ismertetése a Machine Learning alap eszköz beállítás hiánya miatt. A Machine Learning termékért felelős csoport szinkronban után azt kra beállítása szükséges az adatkészletek alap szerzett, és a két modell paritása érhető. 

### <a name="create-regression-model-in-excel"></a>Regressziós modell létrehozása az Excel programban
Az Excel regressziós a szabványos lineáris regressziós modellt, az Excel Analysis ToolPak használt. 

Azt a számított *átlagos abszolút % hiba* és a modell teljesítmény mértékként használni. 3 hónapos egy működő modell Excelben elérésének tartott. Jelenleg nincsenek a nagy részét a Machine Learning Studio kísérlet, amely végső soron a hasznos követelményeinek ismertetése az volt a tanulás.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Hasonló kísérlet létrehozása az Azure gépi tanulás
A lépéseket a kísérletben a Machine Learning Studióban azt követni: 

1. A dataset feltöltött csv-fájlként a Machine Learning Studio (nagyon kis fájl)
2. Új kísérlet létrehozott és használt a [Select Columns in Dataset] [ select-columns] használható Excel azonos adatok szolgáltatásainak modul 
3. Használja a [Split Data] [ split] modul (a *relatív kifejezés* mód) számára az adatok felosztani a azonos képzési adatkészletek, mivel az Excel programban 
4. A kikísérletezett a [lineáris regressziós] [ linear-regression] modul (alapértelmezett beállításai csak), dokumentált, és az eredményeket az Excel regressziós modell képest

### <a name="review-initial-results"></a>Tekintse át a kezdeti eredmények
Először az Excel-modell egyértelműen outperformed a Machine Learning Studio modell: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Teljesítmény | | |
| <ul style="list-style-type: none;"><li>R-négyzet igazítva</li></ul> |0.96 |– |
| <ul style="list-style-type: none;"><li>Variációs <br />Meghatározása</li></ul> |– |0.78<br />(kis pontosság) |
| Mean Absolute Error |$9. 5M |$ 19.4 M |
| Mean Absolute Error (%) |6.03% |12.2% |

Amikor miatt a folyamat, és az eredmények a fejlesztők és adatszakértőkön a Machine Learning csapat, néhány hasznos tipp gyorsan kapott. 

* Ha a [lineáris regressziós] [ linear-regression] a Machine Learning Studióban modul, a két módszer találhatók:
  * Online színátmenetes módszeren: Több alkalmasak lehetnek a nagyobb méretű problémák
  * A szokványos legkevésbé négyzetes: Ez a mód az sokan úgy tekintenek a amikor lineáris regressziós nélkül. Kisebb adatkészletek esetében a szokásos legkevésbé négyzetes lehet egy több optimális választás.
* Fontolja meg a teljesítmény javítása érdekében a 2. szintű exportügyletek súly paraméter tökéletesítse. Alapértelmezés szerint 0,001 van állítva, de a kis adatkészletnél azt állítsa 0,005 teljesítmény javítása érdekében. 

### <a name="mystery-solved"></a>Orvosolhatók titokzatos!
A javaslatok alkalmazásakor azt a Machine Learning Studióban, az Excel alkalmazással azonos alapteljesítményének érhető el: 

|  | Excel | Studio (kezdeti) | Legkevesebb négyzetes keresztüli rendelkező Studio |
| --- |:---:|:---:|:---:|
| Címkézett érték |Tényleges (numerikus) |Azonos |Azonos |
| Tanuló |Excel -> adatok elemzési regressziós -> |Lineáris regressziós. |Lineáris regressziós |
| Tanuló beállítások |– |Alapértelmezés |a szokványos legkevésbé négyzetes<br />L2 = 0.005 |
| Adatkészlet |26 sorok, 3 funkciókat, 1 címke. Az összes numerikus. |Azonos |Azonos |
| Vegyes: vonat |Excel az első 18 sorban, a legutóbbi 8 sorok vizsgálni képezni. |Azonos |Azonos |
| Vegyes: teszt |A legutóbbi 8 sorokon alkalmazott Excel regressziós képlet |Azonos |Azonos |
| **Teljesítmény** | | | |
| R-négyzet igazítva |0.96 |– | |
| Együttható |– |0.78 |0.952049 |
| Mean Absolute Error |$9. 5M |$ 19.4 M |$9. 5M |
| Mean Absolute Error (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Emellett az Excel együttható képest is a szolgáltatás súlyok az Azure betanított modell:

|  | Excel együttható | Az Azure-funkciót súlyozás |
| --- |:---:|:---:|
| INTERCEPT/eltérés |19470209.88 |19328500 |
| A szolgáltatása |0.832653063 |0.834156 |
| B szolgáltatás |11071967.08 |11007300 |
| A szolgáltatás C |25383318.09 |25140800 |

## <a name="next-steps"></a>További lépések
A Machine Learning webszolgáltatásba az Excelből felhasználásához meg akartunk. Az üzleti elemzők Excel alapulnak, és hogy változtatnunk oly módon, az Excel adatok egy sort a Machine Learning webszolgáltatás hívására és térjen vissza az előre jelzett érték Excel. 

Is meg akartunk optimalizálja a modellt, a beállítások és a Machine Learning Studióban elérhető algoritmusok használata.

### <a name="integration-with-excel"></a>Integráció az Excel használatával
Volt a megoldás, hogy azok a Machine Learning regressziós modell által a betanított modell egy webszolgáltatás-bővítmény létrehozása. Néhány percen belül a webszolgáltatás hozta létre, és azt nevezzük közvetlenül az Excelből a bevétel előre jelzett érték visszaadása sikertelen. 

A *Web Services irányítópult* rész tartalmaz egy letölthető Excel-munkafüzet. A munkafüzet a webszolgáltatások API és a séma adatait a beágyazott előre formázott tartalmaz. Amikor rákattint *töltse le az Excel-munkafüzet*, megnyitja a munkafüzetet, és mentheti a helyi számítógépen. 

![][1]

Nyissa meg a munkafüzetet, és másolja az előre definiált paraméterek a kék paraméter szakasz alább látható módon. A paraméterek megadott, miután Excel hívja a Machine Learning webszolgáltatás, és az előre jelzett pontozott címkék jelennek majd meg az előre jelzett értékek zöld szakaszban. A munkafüzet továbbra is a betanított modell az összes sor alatt paraméterek alapján paraméterek Előrejelzés létrehozásához. Ezzel a szolgáltatással kapcsolatos további információkért lásd: [fel az Azure Machine Learning webszolgáltatásba az Excelből](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimalizálás, és további kísérletek
Most, hogy az alapterv volt az Excel-modellt, helyeztük azokat, amelyek a Machine Learning lineáris regressziós modell optimalizálása érdekében. A modul használtuk [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] javítása a kezdeti adatokat a kijelölt elemek és segített nekünk a teljesítmény fokozása 4.6 % elérése Mean Absolute Error. A későbbi projektek a szolgáltatás, amely tudta menteni velünk hét a modellezési használandó szolgáltatások megfelelő halmazát található adatok attribútumok iteráció használjuk. 

Ezután tervezzük például további algoritmusok belefoglalása [bayes-féle] [ bayesian-linear-regression] vagy [súlyozott döntési fák] [ boosted-decision-tree-regression] való vetik össze a kísérletben. 

Ha azt szeretné, regressziós kísérletezhet, próbálja meg helyes adathalmaz olyan numerikus attribútumok sok energia hatékonyságát regressziós minta adatkészletet. A DataSet adatkészlet részeként a mintaként használható adathalmazt a Machine Learning Studióban valósul meg. Tanulási modulok számos segítségével melegítés betöltése vagy hűtési terhelés előrejelzése. Az alábbi táblázatban a különböző regressziós teljesítmény összehasonlítása megtanulja a energiahatékonyság dataset becslése a cél változó Cooling terhelés alapján: 

| Modell | Mean Absolute Error | Legfelső szintű közepét négyzet hiba | Relative Absolute Error | Relatív négyzet hiba | Együttható |
| --- | --- | --- | --- | --- | --- |
| Súlyozott döntési fája |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineáris regressziós (átmenetes módszeren) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neurális hálózat regressziós |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineáris regressziós (szokásos legkevésbé négyzetes) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Kulcs Takeaways
Azt tanult sokkal által Excel fut regresszióhoz, és az Azure Machine Learning kísérleteket párhuzamosan. A modell létrehozása az Excel programban, és összehasonlítja használata a Machine Learning modellek [lineáris regressziós] [ linear-regression] segített, ismerje meg az Azure Machine Learning, és azt felderített lehetőségek adatok kiválasztását és modell teljesítmény javítása érdekében. 

Is található, hogy tanácsos használandó [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] annak érdekében, jövőbeli előrejelzés projektek. Szolgáltatás kiválasztása alkalmaz az adatok, létrehozhat egy továbbfejlesztett modell Machine Learning jobb összesített teljesítményt. 

A prediktív elemzési az előrejelzés a Machine Learning Excel elviselhető átviteli képessége lehetővé teszi, hogy jelentősen növelheti az lehetősége sikeresen találatokat adjon vissza a széles körű üzleti felhasználói közönség számára. 

## <a name="resources"></a>További források
Íme néhány forrás, a regressziós együttműködve segít: 

* Az Excel programban regressziós. Ha soha nem próbálta regressziós az Excel programban, ez az oktatóanyag segítségével egyszerűen: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Az előrejelzés regressziós vs. Tyler Chessman megírt a blog cikke ismertető time series előrejelzési az Excel programban, amely lineáris regressziós helyes kezdő leírása tartalmazza. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* A szokványos legkevésbé négyzetes lineáris regressziós: Hibái, problémák és nehézségek. Bevezetés és regressziós értékelése: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

