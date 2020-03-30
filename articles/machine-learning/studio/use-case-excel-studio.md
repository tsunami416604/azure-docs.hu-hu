---
title: Elemzés áttelepítése az Excelből
titleSuffix: ML Studio (classic) - Azure
description: Lineáris regressziós modellek összehasonlítása az Excelben és az Azure Machine Learning Studióban (klasszikus)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217800"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Elemzés áttelepítése az Excelből az Azure Machine Learning Studio-ba (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* és *Ben Boatman* a Microsoft Data Insights Kiválósági Központjának vállalati megoldástervezői. Ebben a cikkben ismertetik a meglévő regresszió-elemzési csomag áttelepítésének élményét egy felhőalapú megoldásba az Azure Machine Learning Studio (klasszikus) használatával.

## <a name="goal"></a>Cél

Projektünk két célt tmisszon szem előtt tartva indult: 

1. A prediktív elemzésekkel javíthatja szervezetünk havi bevételi előrejelzései pontosságát 
2. Az Azure Machine Learning Studio (klasszikus) használatával megerősítheti, optimalizálhatja, növelheti a sebességet és méretezhet eredményeket. 

Sok más vállalkozáshoz hasonlóan szervezetünk is havi bevétel-előrejelzési folyamaton megy keresztül. Üzleti elemzőink kis csapata az Azure Machine Learning Studio (klasszikus) használatával támogatta a folyamatot és javíthatja az előrejelzés pontosságát. A csapat több hónapot töltött azzal, hogy több forrásból adatokat gyűjtsön, és statisztikai elemzéssel futassa le az adatattribútumokat, amelyek azonosítják a szolgáltatások értékesítésének előrejelzéséhez kapcsolódó kulcsfontosságú attribútumokat. A következő lépés az volt, hogy megkezdjük a statisztikai regressziós modellek prototípusát az Excel adataiban. Néhány héten belül volt egy Excel regressziós modellünk, amely felülmúlta az aktuális mezőt és finanszírozta az előrejelzési folyamatokat. Ez lett az alapérték-előrejelzés eredménye. 

Ezután megtettük a következő lépést, hogy a prediktív elemzésünket áthelyeztük a Studio -ra (klasszikus), hogy megtudjuk, hogyan javíthatja a Studio (klasszikus) a prediktív teljesítményt.

## <a name="achieving-predictive-performance-parity"></a>Prediktív teljesítményparitás elérése
Az első prioritás a Studio (klasszikus) és az Excel regressziós modellek közötti paritás elérése volt. Tekintettel ugyanazokra az adatokra, és ugyanazt a felosztást a betanítási és tesztelési adatokra, az Excel és a Studio (klasszikus) közötti prediktív teljesítményparitást akartunk elérni. Kezdetben kudarcot vallottunk. Az Excel-modell felülmúlta a Studio (klasszikus) modellt. A hiba oka az volt, hogy nem értették az alapeszköz-beállítást a Studio (klasszikus) alkalmazásban. A Studio (klasszikus) termékcsapatával való szinkronizálás tkövetően jobban megismertük az adatkészleteinkhez szükséges alapbeállítást, és elértük a két modell közötti paritást. 

### <a name="create-regression-model-in-excel"></a>Regressziós modell létrehozása az Excelben
Az Excel regressziós használt szabványos lineáris regressziós modell található az Excel Analysis ToolPak. 

Kiszámítottuk *az átlagos abszolút % hiba értéket,* és a modell teljesítménymérőjeként használtuk. 3 hónapba telt, hogy megérkezzen egy működő modellhez az Excel használatával. Hoztuk sok a tanulás Studio (klasszikus) kísérlet, amely végül hasznos volt a megértés követelményeknek.

### <a name="create-comparable-experiment-in-studio-classic"></a>Hozzon létre összehasonlítható kísérletet a Studio -ban (klasszikus)
Követtük ezeket a lépéseket, hogy hozzon létre a kísérlet Studio (klasszikus): 

1. Feltöltötte az adatkészletet, mint egy csv fájlt Studio (klasszikus) (nagyon kis fájl)
2. Új kísérlet létrehozása, és az Oszlopok kijelölése az [Adatkészlet modulban][select-columns] az Excelben használt adatfunkciók kiválasztásához 
3. Az [Adatok felosztása][split] modult használta *(relatív kifejezés* móddal) az adatok felosztásához ugyanazokra a betanítási adatkészletekre, mint az Excelben 
4. Kísérletezett a [lineáris regressziós][linear-regression] modullal (csak az alapértelmezett beállításoknál), dokumentált, és összehasonlította az eredményeket az Excel regressziós modellel

### <a name="review-initial-results"></a>A kezdeti eredmények áttekintése
Először az Excel modell egyértelműen felülmúlta a Studio (klasszikus) modellt: 

|  | Excel | Studio (klasszikus) |
| --- |:---:|:---:|
| Teljesítmény | | |
| <ul style="list-style-type: none;"><li>Korrigált R-négyzet</li></ul> |0.96 |N/A |
| <ul style="list-style-type: none;"><li>Együttható <br />Meghatározása</li></ul> |N/A |0,78<br />(kis pontosság) |
| Átlagos abszolút hiba |$9.5Millió |$ 19.4M |
| Átlagos abszolút hiba (%) |6.03% |12.2% |

Amikor a machine learning-csapat fejlesztői és adattudósai futtattuk a folyamatot és az eredményeket, gyorsan hasznos tippeket adtak. 

* Ha a [Studio (klasszikus) lineáris regressziómodulját][linear-regression] használja, két módszer áll rendelkezésre:
  * Online gradiens süllyedés: lehet alkalmasabb a nagyobb léptékű problémák
  * Rendes legkisebb négyzetek: Ez az a módszer, a legtöbb ember gondol, amikor meghallják lineáris regresszió. Kis adatkészletek esetén a szokásos legkisebb négyzetek optimálisabb választást jelentenek.
* Fontolja meg az L2 regularization Weight paraméter finomhangolását a teljesítmény javítása érdekében. Alapértelmezés szerint 0,001-re van állítva, de a kis adatkészletünk esetében 0,005-re állítjuk a teljesítmény javítása érdekében. 

### <a name="mystery-solved"></a>Rejtély megoldva!
Amikor alkalmaztuk a javaslatokat, ugyanazt az alapteljesítményt értük el a Studio -ban (klasszikus), mint az Excel-ben: 

|  | Excel | Stúdió (klasszikus) (kezdő) | Stúdió (klasszikus) w / Legkisebb négyzetek |
| --- |:---:|:---:|:---:|
| Címkézett érték |Tényleges adatok (numerikus) |Ugyanaz |Ugyanaz |
| Tanuló |Excel -> adatelemzés -> regresszió |Lineáris regresszió. |Lineáris regresszió |
| Tanulói beállítások |N/A |Alapértelmezések |rendes legkisebb négyzetek<br />L2 = 0,005 |
| Adatkészlet |26 sor, 3 funkció, 1 címke. Mind numerikus. |Ugyanaz |Ugyanaz |
| Split: Vonat |Az Excel az első 18 sorban betanított, az utolsó 8 sorban tesztelve. |Ugyanaz |Ugyanaz |
| Split: Teszt |Az utolsó 8 sorra alkalmazott Excel regressziós képlet |Ugyanaz |Ugyanaz |
| **Teljesítmény** | | | |
| Korrigált R-négyzet |0.96 |N/A | |
| Meghatározási együttható |N/A |0,78 |0.952049 |
| Átlagos abszolút hiba |$9.5Millió |$ 19.4M |$9.5Millió |
| Átlagos abszolút hiba (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Emellett az Excel-együtthatók az Azure betanított modelljében a jellemzősúlyokkal is jól összehasonlítva:

|  | Excel-együtthatók | Az Azure szolgáltatás súlyozása |
| --- |:---:|:---:|
| Elfogás/torzítás |19470209.88 |19328500 |
| A szolgáltatása |0.832653063 |0.834156 |
| B szolgáltatás |11071967.08 |11007300 |
| C szolgáltatás |25383318.09 |25140800 |

## <a name="next-steps"></a>Következő lépések
Azt akartuk, hogy használja a Machine Learning webszolgáltatás az Excel.We wanted to consume the Machine Learning web service within Excel. Üzleti elemzőink az Excelre támaszkodnak, és szükségünk volt egy módra, hogy felhívjuk a Machine Learning webszolgáltatást egy sor Excel-adattal, és visszaadjuk az előre jelzett értéket az Excelnek. 

Azt is akarta, hogy optimalizálja a modell segítségével a lehetőségek és algoritmusok elérhető Studio (klasszikus).

### <a name="integration-with-excel"></a>Integráció az Excellel
A megoldás az volt, hogy működőképessé a Machine Learning regressziós modell a betanított modell webszolgáltatás létrehozásával. Néhány percen belül létrejött a webszolgáltatás, és közvetlenül az Excelből hívhatjuk, hogy visszaadjuk az előre jelzett bevételi értéket. 

A *Webszolgáltatások irányítópultja* szakasz egy letölthető Excel-munkafüzetet tartalmaz. A munkafüzet előre formázott a webszolgáltatás API-val és a beágyazott sémainformációkkal. Ha az *Excel-munkafüzet letöltése gombra*kattint, megnyílik a munkafüzet, és mentheti a helyi számítógépre. 

![Excel-munkafüzet letöltése a WebServices irányítópultjáról](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Nyissa meg a munkafüzetet, másolja az előre definiált paramétereket a kék paraméter szakaszba az alábbi módon. A paraméterek megadása után az Excel felhívja a Machine Learning webszolgáltatás, és az előre jelzett pontozott címkék jelennek meg a zöld előre jelzett értékek szakaszban. A munkafüzet továbbra is előrejelzéseket hoz létre a paraméterekhez a paraméterek alapján a Paraméterek csoportban megadott összes sorelemhez. A szolgáltatás használatáról az Azure [Machine Learning webszolgáltatás használata az Excelből című témakörben](consuming-from-excel.md)olvashat bővebben. 

![Sablon Excel-munkafüzet, amely csatlakozik az üzembe helyezett webszolgáltatáshoz](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimalizálás és további kísérletek
Most, hogy az Excel-modellel megvolt az alapkonfiguráció, továbbléptünk a Machine Learning lineáris regressziós modelljének optimalizálása érdekében. A [szűrőalapú funkcióválasztást][filter-based-feature-selection] használtuk a kezdeti adatelemek kiválasztásának javítására, és ez segített 4,6%-os átlagos abszolút hiba teljesítményjavuláselérésében. A jövőbeli projektek fogjuk használni ezt a funkciót, amely mentheti meg nekünk hét iteráció révén adatattribútumok, hogy megtalálják a megfelelő funkciókat használni modellezés. 

Ezután azt tervezzük, hogy további algoritmusok, mint [a Bayes-i vagy][bayesian-linear-regression] [a Boosted Decision Trees][boosted-decision-tree-regression] a kísérletünkben, hogy összehasonlítsuk a teljesítményt. 

Ha a regresszióval szeretne kísérletezni, egy jó adatkészletet kell kipróbálnia az Energiahatékonyságre való regressziós minta adatkészlet, amely sok numerikus attribútummal rendelkezik. Az adatkészlet a Studio (klasszikus) mintaadatkészleteinek részeként érhető el. Számos tanulási modul segítségével megjósolhatja a fűtési terhelést vagy a hűtési terhelést. Az alábbi táblázat a különböző regressziós adatok teljesítmény-összehasonlítása a Hűtési terhelés célváltozóra előrejelzett energiahatékonysági adatkészlethez képest: 

| Modell | Átlagos abszolút hiba | Négyzetátlagos négyzetes hiba | Relatív abszolút hiba | Relatív négyzethiba | Meghatározási együttható |
| --- | --- | --- | --- | --- | --- |
| Kiemelt döntési fa |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Lineáris regresszió (gradiens esés) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neurális hálózat típusú regresszió |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Lineáris regresszió (szokásos legkisebb négyzetek) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Kulcs elvihető ételek
Sokat tanultunk az Excel regressziós és studio (klasszikus) kísérletek párhuzamos futtatásából. Az alapmodell excelben való létrehozása és a Machine Learning [lineáris regressziós][linear-regression] modellekkel való összehasonlítása segített a Studio (klasszikus) tanulásában, és lehetőségeket fedeztünk fel az adatok kiválasztásának és a modell teljesítményének javítására. 

Azt is megállapítottuk, hogy tanácsos [a szűrőalapú funkcióválasztást][filter-based-feature-selection] használni a jövőbeli előrejelzési projektek felgyorsításához. Ha szolgáltatásválasztást alkalmaz az adatokra, továbbfejlesztett modellt hozhat létre a Studio (klasszikus) alkalmazásban, jobb általános teljesítménnyel. 

A prediktív analitikus előrejelzés studio (klasszikus) rendszerszinten történő átvitelének lehetősége lehetővé teszi a sikeres eredmények sikeres biztosításának lehetőségét a széles körű üzleti felhasználói közönség számára. 

## <a name="resources"></a>Források
Íme néhány forrás a regressziós munkához: 

* Regresszió az Excelben. Ha még soha nem próbálta a regressziót az Excelben, ez az oktatóanyag megkönnyíti:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regresszió vs előrejelzés. Tyler Chessman írt egy blog cikket elmagyarázza, hogyan kell csinálni idősorozat előrejelzés Excel, amely tartalmaz egy jó kezdő leírása lineáris regresszió. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Rendes legkisebb négyzetek lineáris regresszió: hibák, problémák és buktatók. A regresszió bevezető és vita: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

