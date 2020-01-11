---
title: Mi az az automatizált ML/AutoML
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja ki a Azure Machine Learning automatikusan egy algoritmust, és létrehoz egy modellt, hogy időt takarítson meg az Ön által megadott paraméterek és feltételek használatával, hogy kiválassza a modellhez legmegfelelőbb algoritmust.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: 778b369e08ff6b0c6e4075c5a8d3d2a234bde70e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894887"
---
# <a name="what-is-automated-machine-learning"></a>Mi az az automatizált gépi tanulás?

Az automatizált gépi tanulás, más néven automatizált ML, az időigényes automatizálási folyamat, a gépi tanulási modellek fejlesztésének ismétlődő feladatai. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy a modell minőségének fenntartása mellett nagy mennyiségű, hatékonyságú és termelékenységű ML-modellt építsenek. Az automatikus ML a [Microsoft kutatási részlegének](https://arxiv.org/abs/1705.05355)áttörésén alapul.

A hagyományos gépi tanulási modell fejlesztése erőforrás-igényes, és jelentős tartományi ismereteket és időt igényel, hogy több tucat modellt hozzon létre és hasonlítson össze. Alkalmazzon automatikus ML-t, ha azt szeretné, hogy a Azure Machine Learning egy modell betanítását és finomhangolását a megadott cél metrika használatával. A szolgáltatás ezután megismétli az ML-algoritmusokat a funkciók kiválasztásával párosítva, ahol minden egyes iteráció egy modellt hoz létre egy képzési pontszámmal. Minél magasabb a pontszám, annál jobb lesz a modellnek az adataihoz igazodni.

Az automatizált gépi tanulással felgyorsíthatja az éles használatra kész ML-modellek gyors és hatékony beszerzéséhez szükséges időt.

## <a name="when-to-use-automated-ml"></a>Mikor kell használni az automatikus ML-t

A gépi tanulási modell fejlesztési folyamata automatizált ML-ezáltal demokratikussá teszi, és lehetővé teszi a felhasználók számára, hogy az adatelemzési szakértelmük alapján minden probléma esetén azonosíthatók legyenek a teljes gépi tanulási folyamatok.

Az adatszakértők, az elemzők és a fejlesztők az egész iparágban az automatizált ML-ket használhatják:

+ Gépi tanulási megoldások megvalósítása kiterjedt programozási ismeretek nélkül
+ Időt és erőforrásokat takaríthat meg
+ Az adatelemzési ajánlott eljárások kihasználása
+ Gyors problémamegoldás

Az alábbi táblázat a gyakori automatikus ML-használati eseteket sorolja fel. 

Osztályozás| Idősorozat-előrejelzés | Regresszió
---|---|---
[Csalások észlelése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Értékesítések előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU-teljesítmény előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Marketing-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Kereslet-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Hírcsoport-adatbesorolás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Italok üzemi előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>Az automatizált ML működése

A **Azure Machine learning**használatával a következő lépésekkel megtervezheti és futtathatja az automatizált ml-betanítási kísérleteket:

1. **Azonosítsa a megoldandó problémát** : besorolás, előrejelzés vagy regresszió

1. A **címkézett betanítási adatmennyiség forrásának és formátumának meghatározása**: NumPy tömbök vagy pandák dataframe

1. **Konfigurálja a számítási célt a modell betanításához**, például a [helyi számítógép, a Azure Machine learning számítások, a távoli virtuális gépek vagy a Azure Databricks](how-to-set-up-training-targets.md).  Ismerje meg a [távoli erőforrások](how-to-auto-train-remote.md)automatizált képzését.

1. **Konfigurálja az automatikus gépi tanulás paramétereit** , amelyek meghatározzák, hogy a különböző modellek, a hiperparaméter-beállítások, a speciális előfeldolgozási/featurization, valamint a legjobb modell meghatározásakor milyen mérőszámokat kell megvizsgálni.  Megadhatja az automatikus betanítási kísérlet beállításait [Azure Machine learning Studióban](https://ml.azure.com)vagy [az SDK-val](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **A betanítási Futtatás elküldése.**

  ![Automatizált gépi tanulás](./media/concept-automated-ml/automl-concept-diagram2.png)

A betanítás során Azure Machine Learning több párhuzamos folyamatot hoz létre, amelyek különböző algoritmusokat és paramétereket próbálnak ki. Ekkor leáll, ha eléri a kísérletben meghatározott kilépési feltételeket.

Megvizsgálhatja a naplózott futtatási adatokat is, amelyek a Futtatás során összegyűjtött [mérőszámokat tartalmazzák](how-to-understand-automated-ml.md) . A betanítási kísérlet egy Python szerializált objektumot (`.pkl` fájlt) hoz létre, amely tartalmazza a modellt és az adatfeldolgozást.

A modell létrehozása automatizálható, és azt is [megtudhatja, milyen fontos vagy releváns funkciók vannak](how-to-configure-auto-train.md#explain) a generált modellekhez.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozás

Minden automatizált gépi tanulási kísérlet során az adatai az alapértelmezett módszerekkel és opcionálisan a speciális előfeldolgozással vannak feldolgozva.

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozásának lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos előfeldolgozási lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozás (standard)

Az automatizált gépi tanulási kísérletek során az adatok automatikusan méretezhetők vagy normalizálva vannak, hogy az algoritmusok jól elvégezhetők legyenek.  A modellek betanítása során a rendszer az alábbi skálázási vagy normalizáló technikák egyikét alkalmazza az egyes modellekre.

|Méretezés&nbsp;&&nbsp;normalizálás| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szabványosítási funkciók az átlag és a skálázás egységbeli eltérésének eltávolításával  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Az egyes szolgáltatások méretezésével átalakítja a szolgáltatásokat az adott oszlop minimális és maximális értékével  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az egyes szolgáltatások méretezése a maximális abszolút értékkel |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |A skálázási funkciók a quantile-tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris dimenzióját-csökkentés az adatértékek számának kibontásával a projekthez egy alacsonyabb dimenziós területre |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor lineáris dimenzióját-csökkentést végez a csonkolt számú érték lebomlásával (SVD). A PCA-vel ellentétben ez a kalkulátor nem helyezi középpontba az adatmennyiséget, mielőtt feldolgozza az egyes SciPy. a ritka mátrixok hatékonyan működnek. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | A rendszer minden mintát (azaz az Adatmátrix minden sorát) legalább egy nullától eltérő összetevővel együtt átméretezi, hogy a norma (L1 vagy L2) eggyel egyenlő legyen. |

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozás: opcionális featurization

További speciális előfeldolgozási és featurization is elérhetők, például az adatguardrails, a kódolás és az átalakítások. [További információ arról, hogy milyen featurization tartalmaz](how-to-create-portal-experiments.md#preprocess). A beállítás engedélyezése a következővel:

+ Azure Machine Learning Studio: az [alábbi lépésekkel](how-to-create-portal-experiments.md)kiválaszthatja a **featurization beállításainak megtekintése** a **konfigurációs Futtatás** szakaszban.

+ Python SDK: `"feauturization": auto' / 'off' / FeaturizationConfig` megadása a [`AutoMLConfig` osztályhoz](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

## <a name="prevent-over-fitting"></a>Túlterhelések megakadályozása

A gépi tanulás túlterhelése akkor fordul elő, ha egy modell túl jól illeszkedik a betanítási adathoz, ezért nem lehet pontosan előre jelezni a láthatatlan tesztelési eredményeket. Más szóval a modell egyszerűen megjegyezte a betanítási adatmintákat és a zajt, de nem elég rugalmas ahhoz, hogy valós adatelemzéseket készítsen. A legtöbbször is felmerülő esetekben a túlterhelt modell azt feltételezi, hogy a betanítás során a funkció értékének kombinációja mindig ugyanazt a kimenetet eredményezi a célhoz. 

A túlzott méretezés elkerülésének legjobb módja, ha a következő módszerekkel kapcsolatos ajánlott eljárásokat követi:

* Több betanítási információ használata és a statisztikai torzulások kiküszöbölése
* Cél szivárgásának megelőzése
* Kevesebb funkció használata
* **Regularizációs és hiperparaméter optimalizálás**
* **A modell összetettségi korlátai**
* **Több ellenőrzés**

Az automatikus ML-ben a fenti első három elem az Ön által **megvalósított legjobb gyakorlat**. Az utolsó három félkövérrel szedett elem az ajánlott eljárás, amely az automatizált, a túlterhelések elleni védelem érdekében alapértelmezés szerint **megvalósítható** . Az automatizált ML-től eltérő beállításokban mind a hat ajánlott eljárás a következő lehet, hogy elkerülje a túlzottan illeszkedő modelleket.

### <a name="best-practices-you-implement"></a>Az Ön által megvalósított ajánlott eljárások

A **több adat** használata a legegyszerűbb és a lehető legjobb módszer a túlzott méretezés megelőzésére, és a hozzáadott bónusz általában növeli a pontosságot. Ha több adathalmazt használ, a modell nehezebbé válik a pontos mintázatok memorizálása érdekében, és olyan megoldások elérésére kényszerül, amelyek rugalmasabbak a további feltételek kielégítéséhez. Fontos továbbá a **statisztikai torzulások**felismerése is, hogy a betanítási adatai ne tartalmazzanak olyan elszigetelt mintákat, amelyek nem léteznek az élő előrejelzési adataiban. Ez a forgatókönyv nehezen oldható meg, mert előfordulhat, hogy nem lehet túlságosan összekapcsolni a vonat-és a tesztelési készletek között, de az élő tesztelési adathoz képest előfordulhat, hogy túl is illik.

A célzott szivárgás egy hasonló probléma, ahol előfordulhat, hogy nem jelenik meg a betanítási és a tesztelési készletek közötti túlterhelés, hanem az előrejelzési időpontban jelenik meg. A cél szivárgás akkor fordul elő, ha a "Cheats" modell a betanítás során olyan információhoz fér hozzá, amelyeknek általában nem kell előrejelzési időpontban lennie. Ha például a probléma a hétfő alapján várhatóan megjósolható, hogy Mennyibe kerül az áru díja, de az egyik funkció, amely véletlenül a csütörtöki adatokból származik, a modellnek nem lesz előrejelzési ideje, mert nem látja a jövőben. A cél szivárgás egyszerű tévedés, de gyakran a probléma szokatlanul nagy pontossága jellemzi. Ha a készlet árának előrejelzését és a modell 95%-os pontosságú betanítását kísérli meg, akkor valószínű, hogy valahol a funkciók között kell elszivárogni.

A funkciók eltávolítása a túlzottan illeszkedő megoldásokkal is segíthet, mivel megakadályozza, hogy a modell túl sok mezőt használjon a konkrét minták memorizálása érdekében, ami rugalmasabb lehet. A mennyiségi mérés nehéz lehet, de ha eltávolíthatja a funkciókat, és megtarthatja ugyanazt a pontosságot, valószínűleg rugalmasabban tette a modellt, és csökkentette a túlzott méretezés kockázatát.

### <a name="best-practices-automated-ml-implements"></a>Ajánlott eljárások automatizált ML-eszközökhöz

A regularizációs egy költséghatékony funkció minimalizálása a komplex és a túlzottan felszerelt modellek szankcionálására. A regularizációs függvények különböző típusai vannak, de általánosságban mind a modellnek, valamint az eltérésnek és a bonyolultságnak a büntetése. Az automatikus ML az L1 (lasszó), az L2 (Ridge) és a ElasticNet (L1 és L2) kombinációt használja különböző kombinációkban különböző modell-hiperparaméter beállításokkal, amelyek szabályozzák a terhelést. Egyszerű feltételek esetén az automatikus ML változó a modell szabályozása és a legjobb eredmény kiválasztásával változhat.

Az automatikus ML emellett explicit módon meggátolja a modell bonyolultságának korlátozását, hogy megakadályozza a túlzott méretezést. A legtöbb esetben ez a megvalósítás kifejezetten a döntési fa-vagy erdő-algoritmusokhoz tartozik, ahol az egyes faszerkezetek maximális mélysége korlátozott, és az erdőben vagy az Ensemble-technikákban felhasznált fák teljes száma korlátozott.

A többszörös ellenőrzés (CV) a teljes betanítási adat több részhalmazának betanítása, valamint az egyes alkészleteken található modellek képzésének folyamata. Az elképzelés az, hogy egy modell "szerencsés" lehet, és nagy pontossággal rendelkezik egy részhalmazsal, de a modell számos részhalmazának használatával nem éri el ezt a nagy pontosságot minden alkalommal. Az önéletrajz használatakor meg kell adnia egy érvényesítési Holdout adatkészletet, meg kell adnia az önéletrajz betöltését (az alkészletek számát) és az automatikus ML-t, majd a modell betanításával és a hiperparaméterek beállítása finomhangolásával csökkentheti az ellenőrzési csoport hibáját Az egyik CV-dobás túl nagy méretű lehet, de a sok közül többen is csökkenti annak a valószínűségét, hogy a végső modellje túlterhelt. A kompromisszum az, hogy az önéletrajz hosszabb időt és így nagyobb költségeket eredményez, mivel a modell betanítása helyett egyszer betanítjuk az egyes *n* CV-alkészletekre.

> [!NOTE]
> Alapértelmezés szerint nincs engedélyezve a kereszt-ellenőrzés. az automatikus ML-beállításokban kell konfigurálni. Ha azonban az önéletrajz konfigurálva van, és egy érvényesítési adatkészletet adtak meg, a folyamat automatizálható.

### <a name="identifying-over-fitting"></a>Túlillesztés azonosítása

Vegye figyelembe a következő betanított modelleket és a hozzájuk kapcsolódó betanítási és tesztelési pontosság.

| Modell | Vonat pontossága | Teszt pontossága |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C# | 99,9% | 45% |

Az **a**modellt figyelembe véve gyakori tévhit, hogy ha a láthatatlan adatokon a teszt pontossága alacsonyabb, mint a betanítási pontosság, a modell túl van szerelve. A teszt pontosságának azonban mindig kisebbnek kell lennie, mint a kiképzés pontossága, és a túlzottan illeszkedő és a megfelelő illeszkedéshez való különbségtétel nem *sokkal* kevésbé pontos. 

Az **a** és **B**modellek összehasonlításakor a Model **a** jobb modell, mert a teszt pontossága nagyobb, és bár a tesztelési pontosság valamivel alacsonyabb, mint 95%, nem jelent jelentős különbséget, amely a túlzottan illeszkedő megoldásra utal. Nem választhatja a **B** modellt egyszerűen azért, mert a vonat és a teszt pontosság egymáshoz közelednek.

A **C** modell a túlzott illesztések egyértelmű esetét jelöli. a betanítás pontossága nagyon magas, de a teszt pontossága nem a magashoz közel van. Ez a különbségtétel szubjektív, de a probléma és az adatok ismerete, valamint a hibák számának elfogadható. 

## <a name="time-series-forecasting"></a>Idősoros előrejelzés

Az előrejelzések az üzleti tevékenység szerves részét képezik, függetlenül attól, hogy bevételi, leltározási, értékesítési vagy vevői igények. Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet.

Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak.

További információ: az [automatikus gépi tanulásra vonatkozó példa az idősorozat-előrejelzéshez](how-to-auto-train-forecast.md). Vagy tekintse meg az [energia igényét bemutató jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) részletes kód példákkal a speciális előrejelzési konfigurációhoz, beleértve a következőket:

* üdülés észlelése és featurization
* idősorozat-és DNN-tanulók (Auto-ARIMA, próféta, ForecastTCN)
* számos modell támogatja a csoportosítást
* gördülő-eredetű kereszt-ellenőrzés
* konfigurálható késések
* a gördülő ablak összesített funkciói

## <a name="ensemble"></a>Ensemble-modellek

Az automatizált gépi tanulás támogatja az Ensemble-modelleket, amelyek alapértelmezés szerint engedélyezve vannak. A Ensemble learning a gépi tanulási eredmények és a prediktív teljesítmény növelésével több modellt kombinálhat egyetlen modell használatával. Az együttes ismétlések a Futtatás utolsó ismétlései jelennek meg. Az automatizált gépi tanulás mind a szavazási, mind a halmozási módszert használja a modellek kombinálásával:

* **Szavazás**: előre jelezhető az előrejelzett osztály valószínűségének súlyozott átlaga (besorolási feladatoknál) vagy előrejelzett regressziós célok alapján (regressziós feladatokhoz).
* **Halmozás**: a halmozás kombinálja a különböző-modelleket, és az egyes modellek kimenete alapján egy meta-modellt is betanít. A jelenlegi alapértelmezett meta-modellek a besorolási feladatokhoz és a ElasticNet a regresszió/előrejelzési feladatokhoz LogisticRegression.

A rendezett Ensemble inicializálásával eldöntheti, hogy mely modelleket kívánja használni az Ensemble-ban, a [Caruana Ensemble kiválasztási algoritmusa](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) . Ez az algoritmus magas szinten inicializálja az összevonást akár öt modellel a legjobb egyéni pontszámokkal, és ellenőrzi, hogy ezek a modellek a legjobb pontszámot követő 5%-os küszöbértéken belül vannak-e a gyenge kezdeti együttesek elkerüléséhez. Ezután minden egyes Ensemble-iterációhoz új modellt adnak hozzá a meglévő együtteshez, az eredményül kapott pontszámot pedig kiszámítjuk. Ha egy új modell javította a meglévő Ensemble-pontszámot, a rendszer frissíti az Ensemble-t, hogy tartalmazza az új modellt.

Lásd: [útmutató](how-to-configure-auto-train.md#ensemble) az alapértelmezett Ensemble beállításainak módosításához az automatikus gépi tanulásban.

## <a name="imbalance"></a>Kiegyensúlyozatlan adathalmazok

A rendszer a gépi tanulási besorolási forgatókönyvek esetében általában az adatokon alapuló, nem kiegyensúlyozott adatokra hivatkozik, és olyan adatokra utal, amelyek az egyes osztályokba tartozó megfigyelések aránytalan arányát tartalmazzák. Ez az egyensúlyhiány a modell pontosságának hamisan érzékelt pozitív hatását eredményezheti, mivel a bemeneti adatok az egyik osztályhoz képest elfogultak, ami azt eredményezi, hogy a betanított modell a torzítást utánozza. 

A Machine learning-munkafolyamatok egyszerűsítésének céljaként az automatikus ML beépített képességekkel rendelkezik, amelyek segítenek a kiegyensúlyozatlan adatmennyiségek, például a 

- Egy **súlyozási oszlop**: az automatikus ml a súlyozott oszlopot bemenetként támogatja, ami az adatokban lévő sorok súlyozását eredményezi, ami akár több vagy kevesebb "fontos" osztályt is tehet.

- Az automatikus ML által használt algoritmusok megfelelően kezelhetik az akár 20:1-es egyensúlyhiányt, ami azt jelenti, hogy a leggyakoribb osztály 20 alkalommal több sort tartalmaz az adatmennyiségnél, mint a legkisebb közös osztály.

### <a name="identify-models-with-imbalanced-data"></a>A kiegyensúlyozatlan adattal rendelkező modellek azonosítása

Mivel a besorolási algoritmusokat általában pontossággal értékelik ki, a modell pontossági pontszámának ellenőrzése jó módszer annak azonosítására, hogy az érintett adatok nem egyensúlyban vannak-e. Valóban nagy pontossággal vagy nagyon alacsony pontossággal rendelkezett bizonyos osztályok esetében?

Emellett az automatikus ML-futtatások automatikusan létrehozzák a következő diagramokat, amelyek segítségével megismerheti a modell besorolásának helyességét, és azonosíthatja a kiegyensúlyozatlan adatok által potenciálisan érintett modelleket.

Diagram| Leírás
---|---
[Zavart mátrix](how-to-understand-automated-ml.md#confusion-matrix)| Kiértékeli a helyesen kategorizált címkéket az adatok tényleges címkéjén. 
[Pontosság – visszahívás](how-to-understand-automated-ml.md#precision-recall-chart)| Kiértékeli a helyes feliratok arányát az adatokban található címkézett példányok arányával. 
[ROC-görbék](how-to-understand-automated-ml.md#roc)| Kiértékeli a helyes feliratok arányát a hamis pozitív feliratok arányával.

### <a name="handle-imbalanced-data"></a>Kiegyensúlyozatlan adatmennyiség kezelése 

Az alábbi módszerek további lehetőségeket biztosítanak az automatikus ML-n kívüli, kiegyensúlyozatlan adatmennyiség kezelésére. 

- Újramintavételezés még az osztályra is, akár a kisebb osztályok mintavételezésével vagy a nagyobb osztályok mintavételezésével. Ezek a módszerek szaktudást igényelnek a feldolgozáshoz és az elemzéshez.

- Használjon olyan teljesítmény-mérőszámot, amely jobban bánik a kiegyensúlyozatlan adatokkal. Az F1 pontszám például a precizitás és a visszahívás súlyozott átlaga. A pontosság mértéke az osztályozó pontossága – az alacsony pontosság azt jelzi, hogy a téves pozitív érték nagy számú hamis pozitív--,, míg a visszahívási mérték egy osztályozó teljessége – az alacsony visszahívás nagy számú hamis negatív értéket jelez. 

## <a name="use-with-onnx-in-c-apps"></a>Használat a ONNX C# alkalmazásokban

A Azure Machine Learning használatával a Python-modell létrehozásához és a ONNX-formátumra való átalakításához használhatja az automatikus ML-t. Az ONNX Runtime támogatja C#, így az C# alkalmazásokban automatikusan létrehozott modell újrakódolás vagy a REST-végpontok által bevezethető hálózati késések nélkül is használható. Próbálja ki ezt a folyamatot ebben a [Jupyter-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizált ML a Microsofton belül

Az automatikus ML más Microsoft-megoldásokban is elérhető, például:

|Integráció|Leírás|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatikus modell kiválasztása és betanítása .NET-alkalmazásokban a Visual Studio és a Visual Studio Code ML.NET automatizált ML (előzetes verzió) használatával.|
|[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|A HDInsight-fürtökön párhuzamosan végezheti el az automatizált ML-betanítási feladatok felskálázását a Sparkban.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Gépi tanulási modellek meghívása közvetlenül Power BI (előzetes verzió).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Új gépi tanulási modelleket hozhat létre SQL Server 2019 big data-fürtök adatain keresztül.|

## <a name="next-steps"></a>Következő lépések

Tekintse át a példákat, és Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulás használatával:

+ Kövesse az [oktatóanyagot: regressziós modell automatikus betanítása az Azure automatizált Machine learning](tutorial-auto-train-models.md)

+ Konfigurálja az automatikus betanítási kísérlet beállításait:
  + A Azure Machine Learning Studióban [kövesse ezeket a lépéseket](how-to-create-portal-experiments.md).
  + A Python SDK használatával hajtsa [végre az alábbi lépéseket](how-to-configure-auto-train.md).

+ Ismerje meg, hogyan [végezheti](how-to-auto-train-forecast.md)el az automatikus betanítást az idősorozat-adatkészletek használatával.

+ Próbálja ki [Jupyter notebook mintákat az automatizált gépi tanuláshoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
