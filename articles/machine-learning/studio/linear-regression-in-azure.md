---
title: Elemzések áttelepítése Excelből
titleSuffix: Azure Machine Learning Studio
description: A lineáris regressziós modellek összehasonlítása Excelben és Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7db66f6f4efa5e48f2af9380115de8bcfb75cb86
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67786675"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Elemzések migrálása az Excelből a Azure Machine Learning Studioba

> *Kate Baroni* és a *ben Boater* a Microsoft adatelemzési központjában található vállalati megoldás-építészek. Ebből a cikkből megtudhatják, hogyan telepíthetik a meglévő regressziós elemzési csomagot egy felhőalapú megoldásba Azure Machine Learning Studio használatával.

## <a name="goal"></a>Cél

A projekt két célt indított: 

1. A prediktív elemzések segítségével javíthatja a szervezet havi bevételi prognózisának pontosságát 
2. A Azure Machine Learning Studio használatával erősítse meg, optimalizálja, növelje sebességét, és méretezheti az eredményeket. 

Mint sok vállalat, a szervezetünk egy havi bevétel-előrejelző folyamaton halad át. Az üzleti elemzők kis csoportja a Azure Machine Learning Studio használatával lett feldolgozva a folyamat támogatásához és az előrejelzés pontosságának javításához. A csapat több hónapot töltött le több forrásból származó adatok gyűjtésével és az adatattribútumok statisztikai elemzésen keresztüli futtatásával, amely a szolgáltatások értékesítési előrejelzésére vonatkozó fontos attribútumokat azonosítja. A következő lépés a statisztikai regressziós modellek prototípusának megkezdése az Excelben tárolt adattípusokhoz. Néhány héten belül egy Excel regressziós modell volt, amely a jelenlegi és a pénzügyi előrejelzési folyamatokat is végrehajtotta. Ez lett az alapterv előrejelzési eredménye. 

Ezután a következő lépés a prediktív elemzések átköltöztetése a studióba, hogy megtudja, hogyan javítható a Studió a prediktív teljesítményben.

## <a name="achieving-predictive-performance-parity"></a>Prediktív teljesítmény-paritás elérése
Első prioritásunk a Studio és az Excel regressziós modelljei közötti paritás elérése volt. Mivel ugyanaz az adat és a kiképzési és tesztelési célú felosztás is azonos, az Excel és a Studio között is meg kellett valósítani a prediktív teljesítmény-paritást. Eredetileg nem sikerült. Az Excel-modell elvégezte a Studio modellt. A hiba oka a Studio alapeszköz-beállításának hiánya volt. A Studio termékkel rendelkező csapattal való szinkronizálás után az adatkészletekhez szükséges alapbeállítás jobb megismerése és a két modell között elért paritás. 

### <a name="create-regression-model-in-excel"></a>Regressziós modell létrehozása az Excelben
Az Excel-regresszió az Excel Analysis ToolPak standard lineáris regressziós modelljét használta. 

A kiszámított érték *abszolút%-os hibát jelent* , és a modell teljesítményének mértékét használta. 3 hónapig tartott egy munkamodellen az Excel használatával. Nagy mennyiségű tanulást indítottunk a Studio-kísérletbe, ami végső soron a követelmények ismeretében hasznos volt.

### <a name="create-comparable-experiment-in-studio"></a>Hasonló kísérlet létrehozása a Studióban
A következő lépéseket követve hozhatjuk létre a kísérletet a Studióban: 

1. Az adatkészlet feltöltése CSV-fájlként a studióba (nagyon kis fájl)
2. Létrehozott egy új kísérletet, és használta a [Select Columns elemet az adatkészlet][select-columns] modulban az Excelben használt adatszolgáltatások kiválasztásához 
3. A [felosztott][split] adatmodul ( *relatív kifejezés* mód) használatával osztja el az adatokat ugyanabba a betanítási adatkészletbe, mint amit az Excelben végzett 
4. Kísérletezett a [lineáris regressziós][linear-regression] modullal (csak az alapértelmezett beállításokkal), dokumentálva, és az eredmények összehasonlítása az Excel regressziós modelljével

### <a name="review-initial-results"></a>Kezdeti eredmények áttekintése
Eleinte az Excel-modell egyértelműen felülmúlta a Studio modellt: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Teljesítmény | | |
| <ul style="list-style-type: none;"><li>Kiigazított R négyzet</li></ul> |0.96 |– |
| <ul style="list-style-type: none;"><li>Együttható <br />Meghatározása</li></ul> |– |0.78<br />(kis pontosság) |
| Abszolút átlagos hiba |9\.5 M USD |19.4 M USD |
| Átlagos abszolút hiba (%) |6.03% |12.2% |

Ha a Machine Learning csapat fejlesztői és adatszakértői által készített folyamatát és eredményeit is felhasználta, a rendszer gyorsan nyújt hasznos tippeket. 

* Ha a Studio [lineáris regressziós][linear-regression] modulját használja, két módszert biztosítunk:
  * Online átmenetes Leereszkedés: A nagyobb méretű problémák megfelelőbbek lehetnek
  * Legkisebb négyzetek: Ez a módszer a legtöbb ember úgy gondolja, hogy mikor hallják a lineáris regressziót. Kis adatkészletek esetében a szokványos legkisebb négyzetek optimális választást is igénybe vehetnek.
* A teljesítmény növelése érdekében érdemes megfontolni az L2 Regularizációs súlyozási paraméterének finomhangolását. Alapértelmezés szerint a 0,001 értékre van állítva, de a kis adatkészletnél a teljesítmény növelése érdekében a 0,005-es értékre van állítva. 

### <a name="mystery-solved"></a>Rejtély megoldva!
A javaslatok alkalmazása során ugyanezt az alapteljesítményt valósították meg a Studióban, mint az Excel programban: 

|  | Excel | Studio (kezdeti) | Studio – minimális négyzetek |
| --- |:---:|:---:|:---:|
| Címkézett érték |Tényleges adatok (numerikus) |azonos |azonos |
| Learner |Excel – > adatelemzés – > regresszió |Lineáris regresszió. |Lineáris regresszió |
| Tanulói beállítások |– |Alapértelmezés |szokásos legkisebb négyzetek<br />L2 = 0.005 |
| Adathalmaz |26 sor, 3 funkció, 1 címke. Az összes numerikus érték. |azonos |azonos |
| Felosztása Betanítás |Az első 18 sorban betanított Excel az utolsó 8 sorban tesztelt. |azonos |azonos |
| Felosztása Tesztelés |Az utolsó 8 sorra alkalmazott Excel regressziós képlet |azonos |azonos |
| **Teljesítmény** | | | |
| Kiigazított R négyzet |0.96 |– | |
| Meghatározási együttható |– |0.78 |0.952049 |
| Abszolút átlagos hiba |9\.5 M USD |19.4 M USD |9\.5 M USD |
| Átlagos abszolút hiba (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Emellett az Excel-együtthatók az Azure-ban betanított modellben is összehasonlítva vannak a funkciók súlyozásával:

|  | Excel-együtthatók | Azure-funkciók súlyozása |
| --- |:---:|:---:|
| Feltartóztatás/torzítás |19470209.88 |19328500 |
| A szolgáltatás |0.832653063 |0.834156 |
| B szolgáltatás |11071967.08 |11007300 |
| C funkció |25383318.09 |25140800 |

## <a name="next-steps"></a>További lépések
A Machine Learning webszolgáltatást az Excelben szeretnénk felhasználni. Az üzleti elemzők az Excel programon alapulnak, és a Machine Learning webszolgáltatást az Excel-adatok sorával kell meghívni, és az előre jelzett értéket vissza kell adni az Excelnek. 

A modellt a Studióban elérhető lehetőségek és algoritmusok használatával is optimalizálni szeretnénk.

### <a name="integration-with-excel"></a>Integráció az Excelrel
A megoldásunk az volt, hogy működővé tenni a Machine Learning regressziós modellt egy webszolgáltatás a betanított modellből való létrehozásával. Néhány percen belül a webszolgáltatás létrejött, és közvetlenül az Excelből hívhatjuk, hogy visszaállítson egy előre jelzett bevételi értéket. 

A webszolgáltatások irányítópultja szakasz egy letölthető Excel-munkafüzetet tartalmaz. A munkafüzet előre formázva van a webszolgáltatási API-val és a séma-információval ágyazva. Ha az *Excel-munkafüzet letöltése*elemre kattint, megnyílik a munkafüzet, és mentheti a helyi számítógépre. 

![Excel-munkafüzet letöltése a Web Services irányítópultról](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Nyissa meg a munkafüzetet, és másolja az előre definiált paramétereket a kék paraméter szakaszba az alábbi módon. A paraméterek megadása után az Excel meghívja a Machine Learning webszolgáltatást, és az előre jelzett pontszámú feliratok a zöld előre jelzett értékek szakaszban jelennek meg. A munkafüzet továbbra is előrejelzéseket hoz létre a paraméterek alapján a betanított modelltől függően. További információ a funkció használatáról: [Azure Machine learning webszolgáltatás használata az Excelből](consuming-from-excel.md). 

![Az üzembe helyezett webszolgáltatáshoz csatlakozó sablon Excel-munkafüzet](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalizálás és további kísérletek
Most, hogy az Excel-modellel rendelkezünk egy alapkonfigurációval, a Machine Learning lineáris regressziós modell optimalizálására törekszünk. A modul [Filter-alapú funkciójának][filter-based-feature-selection] kiválasztásával fejlesztjük a kezdeti adatelemek kiválasztását, és a teljesítmény növelése érdekében a 4,6%-os abszolút hibát eredményezett. A jövőbeli projektekhez ezt a funkciót fogjuk használni, amely az adatattribútumok segítségével megközelítheti az USA-beli heteket, hogy megtalálja a modellezéshez használandó szolgáltatások megfelelő készletét. 

A következő lépésben további algoritmusokat is megtervezünk, például a [Bayes][bayesian-linear-regression] vagy a megnövelt [döntési fákat][boosted-decision-tree-regression] a kísérletben a teljesítmény összehasonlításához. 

Ha a regresszióval kísérletezni szeretne, egy jó adatkészlet kipróbálható az energiahatékonysági regressziós minta adatkészlet, amely számos numerikus attribútummal rendelkezik. Az adatkészlet a Studióban található minta adatkészletek részeként van megadva. Különböző tanulási modulok használatával előre jelezheti a betöltést vagy a hűtési terhelést. Az alábbi diagram a különböző regressziók teljesítményének összehasonlítását mutatja be az energiahatékonysági adatkészlet előrejelzésével a megcélzott változó hűtési terheléséhez képest: 

| Modell | Abszolút átlagos hiba | Legfelső szintű négyzetes hiba | Relatív abszolút hiba | Relatív négyzetes hiba | Meghatározási együttható |
| --- | --- | --- | --- | --- | --- |
| Megnövelt döntési fa |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineáris regresszió (átmenetes leereszkedés) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neurális hálózat típusú regresszió |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineáris regresszió (szokásos legkisebb négyzetek) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Kulcs elvihető
Sokat tanultam az Excel regressziós és Studio-kísérletek párhuzamos futtatásával. Az alapmodell létrehozása az Excelben, és a Machine Learning [lineáris regressziót][linear-regression] használó modellekhez való összehasonlítás segített a Studióban, és felderítettük az adatválaszték és a modell teljesítményének javítására vonatkozó lehetőségeket. 

Azt is javasoljuk, hogy a szűrésen alapuló [funkció][filter-based-feature-selection] kiválasztásával gyorsítsa fel a jövőbeli előrejelzési projekteket. Ha a funkciók kijelölését alkalmazza az adataira, a jobb általános teljesítmény érdekében létrehozhat egy továbbfejlesztett modellt a Studióban. 

A studióból az Excel programba való prediktív elemzési előrejelzések átadásának lehetősége lehetővé teszi, hogy a nagyvállalati felhasználók számára az eredmények sikeres megadásának képessége jelentősen növekedjen. 

## <a name="resources"></a>További források
Íme néhány forrás, amely segít a regresszióval való együttműködésben: 

* Regresszió az Excelben. Ha még soha nem próbálta meg a regressziót az Excelben, az oktatóanyag megkönnyíti az alábbiakat:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regresszió vs előrejelzés. Tyler Chess írt egy blogot, amely elmagyarázza, hogyan teheti meg az idősorozat-előrejelzést az Excelben, amely jó kezdő leírást tartalmaz a lineáris regresszióról. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Szokásos legkisebb négyzetek lineáris regresszió: Hibák, problémák és buktatók. A regresszió bevezetését és megvitatását: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

