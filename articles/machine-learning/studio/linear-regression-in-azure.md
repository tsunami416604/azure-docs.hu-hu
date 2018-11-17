---
title: Lineáris regresszió használata a Machine Learning |} A Microsoft Docs
description: Az Excel és az Azure Machine Learning Studióban lineáris regressziós modellek összehasonlítása
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 0e7004cca1d64270a2b48ea1e41c74b3e7555317
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823775"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Lineáris regresszió használata az Azure Machine Learning termékben
> *Kate Baroni* és *Ben Boatman* vannak a vállalati Microsoft Data Insights kiváló Center a megoldástervezők. Ebben a cikkben bemutatják egy meglévő regressziós elemzési suite egy felhőalapú megoldás segítségével az Azure Machine Learning-ba való migrálás tapasztalataikról. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Cél
A projekt lépések két célok elérésére: 

1. Prediktív elemzés használatával a szervezet havi bevétel előrejelzése pontosságának javítása 
2. Az Azure Machine Learning segítségével győződjön meg arról, optimalizálja, növelheti a sebességet, az eredmények hatékonyságát és méretét. 

Számos vállalat, például a szervezet számára végighalad egy havi bevétel előrejelzési folyamat. Az üzleti elemzők kis csoportja volt biztosítja az Azure Machine Learning segítségével a folyamat támogatásához, és az előrejelzési pontosság növeléséhez. A csapat néhány hónappal a különböző forrásokból származó adatok gyűjtése és azonosítása legfőbb attribútumai a szolgáltatások értékesítési előrejelzés statisztikai elemzés keresztül az adatok attribútumok futtató töltött. A következő lépés az adatok az Excelben a prototípus-készítés statisztikai regressziós modellek megkezdéséhez volt. Az Excel regressziós modellt, amely a jelenlegi és a folyamatok előrejelzés pénzügyi volt outperforming kellett néhány héten belül. Ez lett az alapvető előrejelzési eredményt. 

Ezután meggyőződtünk a következő lépéssel, ismerje meg, hogyan javíthatja a Machine Learning a prediktív teljesítményét az Azure Machine Learning a prediktív elemzési helyezi.

## <a name="achieving-predictive-performance-parity"></a>Prediktív teljesítmény paritásos elérése
Az első elsőbbséget volt a Machine Learning és az Excel regressziós modellek paritása eléréséhez. Adja meg ugyanazokat az adatokat, és az adott felosztás betanítására és tesztelésére az adatokat, szerettünk volna túllépni az Excel és a Machine Learning prediktív teljesítmény paritása eléréséhez. Kezdetben nem sikerült. Az Excel-modell outperformed Machine Learning-modellhez. A hiba történt egy megértése a Machine Learning alapvető eszköz beállítás hiánya miatt. A Machine Learning termékért felelős csoport szinkronban után azt szerzett beállítása az adatkészletekhez szükséges alap jobban megértette, és a két modell közti paritásos érhető. 

### <a name="create-regression-model-in-excel"></a>Regressziós modell létrehozásához az Excelben
Az Excel regressziós az Excel Analysis ToolPak található standard lineáris regressziós modellt használja. 

Azt a számított *átlagos abszolút % hiba* és a teljesítményadatok részeként használja, a modellt. Ahhoz, hogy eljusson az Excel használatával működő modell 3 hónap végrehajtásának. A Microsoft tudomására nagy része a Machine Learning Studio-kísérletet, amely végső soron a követelményeinek ismertetése a hasznos volt be a learning.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Az Azure Machine Learning összehasonlítható kísérlet létrehozása
Hogy követni ezeket a lépéseket a kísérlet létrehozása a Machine Learning Studio: 

1. Az adatkészlet feltöltött csv-fájlként Machine Learning Studio (igen kis méretű fájl)
2. Új kísérlet hoztunk létre, és a [Select Columns in Dataset] [ select-columns] modult, válassza ki a ugyanazon adatok szolgáltatásokat használt az Excelben 
3. Használja a [Split Data] [ split] modul (az *relatív kifejezés* módban), az adatok ossza az azonos képzési adathalmazok alapján, mivel az Excelben 
4. Végzett a kísérletezést a [lineáris regressziós] [ linear-regression] modul (alapértelmezett beállításai csak), dokumentált, és az eredményeket az Excelben regressziós modellt a képest

### <a name="review-initial-results"></a>Tekintse át a kezdeti eredményei
Először az Excel-modell egyértelműen outperformed a Machine Learning Studio-modell: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Teljesítmény | | |
| <ul style="list-style-type: none;"><li>R-négyzet igazítva</li></ul> |0.96 |– |
| <ul style="list-style-type: none;"><li>Együttható <br />Meghatározása</li></ul> |– |0.78<br />(kis pontossági) |
| Mean Absolute Error |$9. 5M |$ 19.4 M |
| Mean Absolute Error (%) |6.03% |12.2% |

Problémába ütköztünk a folyamat és az eredmények és az adatelemzők által a Machine Learning-csapat, amikor azok gyorsan megadott néhány hasznos tippeket. 

* Használatakor a [lineáris regressziós] [ linear-regression] modul a Machine Learning Studióban, a két módszer találhatók:
  * Online színátmenet Grádiens: Lehet, megfelelőbbek, nagyobb léptékben kapcsolatos problémák
  * Szokásos legkisebb négyzetek: Ez a legtöbb ember úgy, ha azok hall, lineáris regresszió mód. A kis adatkészletekhez a szokásos legkisebb négyzetek több optimális választás lehet.
* Vegye figyelembe, hogy a teljesítmény javítása érdekében L2 Regularizációs súly paraméter finomhangolása. Alapértelmezés szerint 0,001 van beállítva, de a kisméretű adatkészlet esetében azt állítsa 0,005 teljesítmény javítása érdekében. 

### <a name="mystery-solved"></a>Megoldott titokzatos!
A javaslatok alkalmazásakor azt ugyanazon alapteljesítményének a Machine Learning Studióban, az Excel használatával érhető el: 

|  | Excel | Studio (kezdeti) | Studio legkisebb négyzetek használatával |
| --- |:---:|:---:|:---:|
| Címkézett érték |Tényleges (numerikus) |Azonos |Azonos |
| Tanuló |Az Excel -> Data Analysis -> regresszió |Lineáris regresszió. |Lineáris regresszió |
| Learner beállításai |– |Alapértelmezés |szokásos legkisebb négyzetek<br />L2 = 0.005 |
| Adatkészlet |26 sort, 3 funkciók, 1 címkét. Az összes numerikus. |Azonos |Azonos |
| Split: Train |Az Excel tanított először 18 sort, a legutóbbi 8 sorok tesztelve. |Azonos |Azonos |
| Split: teszt |Excel-alkalmazása a legutóbbi 8 regressziós képlet |Azonos |Azonos |
| **Teljesítmény** | | | |
| R-négyzet igazítva |0.96 |– | |
| Coefficient of Determination |– |0.78 |0.952049 |
| Mean Absolute Error |$9. 5M |$ 19.4 M |$9. 5M |
| Mean Absolute Error (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Emellett az Excel együttható képest is a szolgáltatás súlyok az Azure betanított modell:

|  | Az Excel együttható | Az Azure szolgáltatás súlyok |
| --- |:---:|:---:|
| INTERCEPT/eltérés |19470209.88 |19328500 |
| A szolgáltatása |0.832653063 |0.834156 |
| B szolgáltatás |11071967.08 |11007300 |
| A szolgáltatás C |25383318.09 |25140800 |

## <a name="next-steps"></a>További lépések
Szerettünk volna a Machine Learning web service Excelből felhasználásához. Az üzleti elemzők támaszkodnak az Excel és a egy módszert hívja meg a Machine Learning webszolgáltatás egy Excel-adatok a sort, és azok az előre jelzett érték térjen vissza az Excel volt szükségünk ahhoz. 

Is szerettünk volna a modell optimalizálása érdekében a beállításokat és a Machine Learning Studióban elérhető algoritmusok használatával.

### <a name="integration-with-excel"></a>Az Excel-integráció
A megoldás volt, a Machine Learning regressziós modell üzembe helyezése a webszolgáltatás létrehozásával a betanított modellből. Néhány percen belül a web service lett létrehozva, és azt nevezzük közvetlenül az Excelből becsült bevétel értéket sikerült. 

A *Web Services-irányítópult* szakasz tartalmaz egy letölthető Excel-munkafüzet. A munkafüzet tartalmaz a webes API-t és a séma adatait beágyazott előre formázott. Amikor rákattint *Excel-munkafüzet letöltése*, megnyitja a munkafüzetet, és mentheti a helyi számítógépen. 

![][1]

Nyissa meg a munkafüzetet, és másolja az előre meghatározott paraméterek a kék paraméter szakasz alább látható módon. Miután megadta a paramétereket, az Excel hívja a Machine Learning web Service és az előre jelzett értékek zöld szakaszban megjeleníti az előre jelzett pontozott címkék. A munkafüzet továbbra is előrejelzéseket paraméterek alapján az összes sor elemek a megadott paraméterek alapján a betanított modell létrehozásához. Ez a funkció használatáról további információkért lásd: [használ az Azure Machine Learning Web Service az Excelből](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimalizálás és további kísérletek
Most, hogy az alapterv kellett az Excel-modell, költöztünk előre a Machine Learning lineáris regressziós modell optimalizálása. A modul használtuk [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] javítása a kezdeti adatok a kijelölt elemeket, és azt kommunikációnkhoz érhet el a teljesítmény fokozása 4.6 %-os Mean Absolute Error. A későbbi projektek használjuk ezt a szolgáltatást, amely menthető USA hét iterálás adatattribútumokat található a megfelelő bizonyos használandó modellezési funkcióit a. 

Ezután tervezzük további algoritmusok például belefoglalása [Bayes] [ bayesian-linear-regression] vagy [súlyozott döntési fák] [ boosted-decision-tree-regression] a kísérletben összehasonlítása teljesítmény. 

Ha azt szeretné, regressziós kísérletezhet, és próbálkozzon jó adathalmaz olyan numerikus attribútumok rengeteg energiát hatékonyság regressziós mintaadatkészlettel. Az adatkészlet a Machine Learning Studio mintaadatkészleteinek részeként van megadva. Tanulási modulok segítségével fűtés betöltése vagy hűtéssel terhelés előrejelzése. Az alábbi táblázatban a különböző regressziós teljesítményének összehasonlítását megtanulja az energiahatékonyságot adatkészlet előrejelzésére a cél változó Cooling terhelés alapján: 

| Modell | Mean Absolute Error | Root mean-készlet négyzet hiba | Relatív abszolút hiba | Relatív négyzet hiba | Coefficient of Determination |
| --- | --- | --- | --- | --- | --- |
| Gyorsított döntési fa |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineáris regresszió (gradiens módszeres) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neurális hálózat regresszió |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineáris regresszió (szokványos legkisebb négyzetek) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Kulcs Takeaways
Már megtanultuk sokkal által a futó Excel regressziós, és az Azure Machine Learning-kísérleteket párhuzamosan. A modell létrehozása az Excel programban, és hasonlítsa össze a Machine Learning használatával [lineáris regressziós] [ linear-regression] USA megtudhatja, hogy az Azure Machine Learning, és hogy felderített fejlesztése érdekében adatokat segített Kijelölés és a modell teljesítményét. 

Is találhatók, hogy tanácsos használandó [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] jövőbeli előrejelzési projektek felgyorsításához. Szolgáltatás kiválasztása hatására az adatok egy továbbfejlesztett modell létrehozhat a Machine Learning jobb általános teljesítményt. 

Lehetővé teszi a prediktív elemzési előrejelzése a Machine Learning Excel elviselhető transfer jelentősen növelheti sikeresen biztosítani az eredményeket egy széles körű üzleti felhasználó közönség számára lehetővé teszi. 

## <a name="resources"></a>További források
Íme néhány forrás, hogy használatával a regressziós: 

* Regresszió az Excelben. Ha soha nem próbálta regressziós az Excelben, ez az oktatóanyag segítségével egyszerűen: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Előrejelzés regressziós vs. Tyler Chessman írtam egy blog a cikk elmagyarázza, hogyan time series előrejelzési az Excelben, amely lineáris regressziós modell futtatása egy jó kezdőknek leírását tartalmazza. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Szokásos legkisebb négyzetek lineáris regresszió: Hibái, problémák és Alkalmazásmegoldásokra. A bevezetés és regressziós veszik górcső alá: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

