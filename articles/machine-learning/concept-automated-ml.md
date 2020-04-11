---
title: Mi az automatizált ML / AutoML
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy az Azure Machine Learning hogyan tud automatikusan kiválasztani egy algoritmust az Ön számára, és hogyan hozhat létre belőle egy modellt, hogy időt takarítson meg a modellnek leginkább megfelelő algoritmus kiválasztásához megadott paraméterek és feltételek használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 095561f02fdeff6688b78d69cc1becc4ee0f8901
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115216"
---
# <a name="what-is-automated-machine-learning"></a>Mi az automatizált gépi tanulás?

Az automatizált gépi tanulás, más néven automatizált gépi tanulási modell a gépi tanulási modell fejlesztés időigényes, iteratív feladatainak automatizálása. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy magas méretű, hatékonyságú és termelékenységű ML-modelleket építsenek, miközben fenntartják a modell minőséget. Az automatizált ML a [Microsoft Research részlegünk](https://arxiv.org/abs/1705.05355)áttörésén alapul.

A hagyományos gépi tanulási modell fejlesztése erőforrás-igényes, jelentős tartománytudást és időt igényel több tucat modell előállításához és összehasonlításához. Az automatizált gépi tanulással felgyorsíthatja a gyártásra kész GÉPI modellek nagy könnyedségével és hatékonysággal történő leküzdéséhez szükséges időt.

## <a name="when-to-use-automated-ml"></a>Mikor kell használni az automatikus ml-t?

Automatikus ml alkalmazása, ha azt szeretné, hogy az Azure Machine Learning betanítson és hangoljon be egy modellt a megadott célmetrika használatával. Az automatizált ml demokratizálja a gépi tanulási modell fejlesztési folyamatát, és lehetővé teszi a felhasználók számára, hogy adatelemzési szakértelmüktől függetlenül azonosítsák a végpontok között lévő gépi tanulási folyamatot bármilyen probléma esetén.

Az adatszakértők, elemzők és fejlesztők a különböző iparágakban automatizált ml-t használhatnak a következőkre:

+ Gépi tanulási megoldások megvalósítása széles körű programozási ismeretek nélkül
+ Takarítson meg időt és erőforrásokat
+ Az adatelemzés bevált módszereinek kihasználása
+ Agilis problémamegoldás biztosítása

Az alábbi táblázat a gyakori automatikus pénzmosási használati eseteket sorolja fel. 

Osztályozás| Idősorozat-előrejelzés | Regresszió
---|---|---
[Csalás felderítése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Értékesítési előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Cpu teljesítményének előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Marketing előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Igény-előrejelzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Hírcsoport-adatok besorolása](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Italgyártás előrejelzése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>Automatikus ml-kísérletek tervezése

Az **Azure Machine Learning**használatával az alábbi lépésekkel tervezheti meg és futtathatja az automatikus ml-es betanítási kísérleteket:

1. **Azonosítsa a** megoldandó ml-problémát: osztályozás, előrejelzés vagy regresszió

1. **Adja meg a címkézett betanítási adatok forrását és formátumát:** Numpy tömbök vagy Pandas adatkeret

1. **Konfigurálja a számítási célt a modellbetanításhoz,** például a [helyi számítógéphez, az Azure Machine Learning Computes-hoz, a távoli virtuális gépekhez vagy az Azure Databrickshez.](how-to-set-up-training-targets.md)  További információ [a távoli erőforrásokon történő](how-to-auto-train-remote.md)automatikus képzésről.

1. **Konfigurálja az automatikus gépi tanulási paramétereket,** amelyek meghatározzák, hogy hány ismétlések különböző modellek, hiperparaméter-beállítások, speciális előfeldolgozás/featurization, és milyen metrikákat kell tekinteni, amikor meghatározzák a legjobb modell.  Az Automatikus betanítási kísérlet beállításait az [Azure Machine Learning stúdióban](https://ml.azure.com)vagy [az SDK-ban](how-to-configure-auto-train.md)konfigurálhatja. 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Küldje el a betanítási futtatást.**

## <a name="how-automated-ml-works"></a>Az automatizált ml működése

A betanítás során az Azure Machine Learning számos párhuzamos folyamatot hoz létre, amelyek különböző algoritmusokat és paramétereket próbálnak ki. A szolgáltatás iterálja a szolgáltatás-beállításokkal párosított ml-algoritmusokon keresztül, ahol minden egyes ismétlés egy betanítási pontszámmal rendelkező modellt hoz létre. Minél magasabb a pontszám, annál jobb a modell tekinthető "illeszkedik" az adatokat.  Leáll, amint eléri a kísérletben meghatározott kilépési feltételeket. Az alábbi ábra ezt a folyamatot mutatja be. 

  ![Automatizált gépi tanulás](./media/concept-automated-ml/automl-concept-diagram2.png)


Azt is megvizsgálhatja a naplózott futtatási információk, amely tartalmazza a futtatás során összegyűjtött [metrikák.](how-to-understand-automated-ml.md) A betanítási futtatás egy`.pkl` Python szerializált objektumot (fájlt) hoz létre, amely tartalmazza a modellt és az adatok előfeldolgozását.

Míg a modellépület automatizált, azt is [megtudhatja, hogy milyen fontos vagy releváns funkciók](how-to-configure-auto-train.md#explain) a generált modellek.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozás

Minden automatizált gépi tanulási kísérletben az adatok at az alapértelmezett módszerekkel és opcionálisan a fejlett előfeldolgozással dolgozzuk fel.

> [!NOTE]
> Az automatizált gépi tanulás előfeldolgozási lépései (funkciónormalizálás, hiányzó adatok kezelése, szöveg átalakítása numerikussá stb.) az alapul szolgáló modell részévé válnak. Ha a modell t használja az előrejelzések, ugyanazokat az előzetes feldolgozási lépéseket alkalmazott betanítása során automatikusan alkalmazza a bemeneti adatokat.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozás (szabványos)

Minden automatizált gépi tanulási kísérletben az adatok automatikusan méretezve vagy normalizálva lesznek, hogy az algoritmusok jól teljesítsenek.  A modell betanítása során az alábbi skálázási vagy normalizálási technikák egyikét alkalmazza az egyes modellek.

|Normalizálás méretezése&nbsp;&&nbsp;| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | A jellemzők szabványosítása az átlag eltávolításával és az egységvarianciára való méretezéssel  |
| [MinMaxSkalár](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Átalakítja a funkciókat úgy, hogy az egyes funkciókat az adott oszlop minimális és maximális értékével skálázhatja  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az egyes funkciók méretezése a maximális abszolút értékkel |
| [RobusztusSkalár](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ez a Scaler a kvantilis tartományuk szerint |
| [Pem](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris méretcsökkentés az adatok egyedi érték szerinti felbontásával, hogy azt egy kisebb dimenziós térbe vetítse ki |
| [CsonkaSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor lineáris méretcsökkentést hajt végre csonkolt egyedi érték-bomlással (SVD). A PCA-val ellentétben ez a becslés nem állítja az adatokat az egyes számú érték-bomlás kiszámítása előtt, ami azt jelenti, hogy hatékonyan tud dolgozni a scipy.sparse mátrixokkal |
| [Ritkanormálító](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Minden egyes minta (azaz az adatmátrix minden egyes sora) legalább egy nem nulla összetevővel a többi mintától függetlenül átméreteződik, hogy a normája (l1 vagy l2) egynek feleljemeg. |

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozás: opcionális funkció

További speciális előfeldolgozás és funkciók is rendelkezésre állnak, például adatkorlátok, kódolás és átalakítások. [További információ arról, hogy mi a csomag.](how-to-use-automated-ml-for-ml-models.md#featurization) Engedélyezze ezt a beállítást a következőkkel:

+ Azure Machine Learning studio: **Engedélyezze az automatikus featurization** a **További konfiguráció megtekintése** szakaszban [ezekkel a lépésekkel.](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` Az [ `AutoMLConfig` osztály](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)megadása. 

## <a name="classification--regression"></a>Besorolás & regresszió

A besorolás és a regresszió a gépi tanulási feladatok leggyakoribb típusai. Mindkettő felügyelt tanulás, amelyben a modellek a betanítási adatok használatával tanulnak, és ezeket a tanulságokat alkalmazzák az új adatokra. Az Azure Machine Learning kifejezetten ezekhez a feladatokhoz kínál, például a mély neurális hálózati szövegfeaturizers besoroláshoz. További információ a [jellemzőbeállításokról.](how-to-use-automated-ml-for-ml-models.md#featurization) 

A besorolási modellek fő célja annak előrejelzése, hogy mely kategóriákba kerülnek az új adatok a betanítási adatokból származó tanulságok alapján. A gyakori besorolási példák közé tartozik a csalások észlelése, a kézírás-felismerés és az objektumfelismerés.  Tudjon meg többet, és tekintse meg az [automatizált gépi tanulás osztályozásának példáját.](tutorial-train-models-with-aml.md)

Eltérő besorolás, ahol az előre jelzett kimeneti értékek kategorikus, regressziós modellek előre numerikus kimeneti értékek független előrejelzők alapján. A regresszió, a cél az, hogy segítsen létrehozni a kapcsolatot a független előrejelző változók becslésével, hogy egy változó hatással van a többiek. Például, autó ára alapján funkciók, mint például, gáz kilométer, biztonsági minősítés, stb További információ, és példa a [regresszió az automatizált gépi tanulás](tutorial-auto-train-models.md).

## <a name="time-series-forecasting"></a>Idősorozat-előrejelzés

Az előrejelzések létrehozása minden vállalkozás szerves része, legyen szó bevételről, készletről, értékesítésről vagy vevői igényről. Az automatizált ml segítségével kombinálhatja a technikákat és a megközelítéseket, és ajánlott, kiváló minőségű idősorozat-előrejelzést kaphat.

Az automatizált idősorozat-kísérleteket többváltozós regressziós problémaként kezeli a rendszer. A múltbeli idősorok értékei "elfordulnak", hogy a regresszor további dimenzióivá váljanak más előrejelzőkkel együtt. Ez a megközelítés, ellentétben a klasszikus idősorozat módszerek, van egy előnye, természetesen magában foglalja a több kontextuális változók és azok kapcsolatát egymással a képzés során. Az automatikus ml egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet és az előrejelzési horizontok összes eleméhez. Így több adat áll rendelkezésre a modellparamétereinek becsléséhez, és lehetővé válik a láthatatlan adatsorok általánosítása.

Tudjon meg többet, és tekintse meg az automatikus gépi tanulás az [idősorozat-előrejelzés.](how-to-auto-train-forecast.md) Vagy tekintse meg az [energiaigény-jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) a speciális előrejelzési konfiguráció részletes kódpéldáiért, beleértve a következőket:

* ünnep felismerése és featurization
* idő- és DNN tanulók (Auto-ARIMA, Prophet, ForecastTCN)
* számos modell támogatja a csoportosítást
* gördülő eredetű keresztérvényesítés
* konfigurálható késések
* gördülőablak összesítő szolgáltatásai

## <a name="ensemble-models"></a><a name="ensemble"></a>Ensemble modellek

Az automatikus gépi tanulás támogatja az együttes modelleket, amelyek alapértelmezés szerint engedélyezve vannak. Az ensemble learning javítja a gépi tanulási eredményeket és a prediktív teljesítményt azáltal, hogy több modellt kombinál, nem pedig egyetlen modelleket. Az együttes iterációk jelennek meg, mint a végső iterációk a távon. Az automatizált gépi tanulás szavazási és halmozási együttesmódszereket is használ a modellek kombinálásához:

* **Szavazás**: az előre jelzett osztályvalószínűségek (besorolási feladatok esetén) vagy az előre jelzett regressziós célok (regressziós feladatok esetén) súlyozott átlaga alapján előrejelez.
* **Egymásra**rakható: egymásra egyesíti a heterogén modellek és a vonatok egy meta-modell alapján a kimenet az egyes modellek. A jelenlegi alapértelmezett metamodellek a logisztikairegresszió besorolási feladatokés ElasticNet a regressziós/előrejelzési feladatok.

A [Caruana együttes kiválasztási algoritmus](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) a rendezett együttes inicializálása használják annak eldöntésére, hogy mely modelleket használja az együttesen belül. Magas szinten ez az algoritmus inicializálja az együttest legfeljebb öt modellel, a legjobb egyéni pontszámokkal, és ellenőrzi, hogy ezek a modellek a legjobb pontszám 5%-os küszöbértékén belül vannak-e, hogy elkerüljék a gyenge kezdeti együttest. Ezután minden egyes együttes iterációhoz egy új modellt adnak hozzá a meglévő együtteshez, és kiszámítják a kapott pontszámot. Ha egy új modell javította a meglévő együttes pontszám, az együttes frissül, hogy tartalmazza az új modell.

Tekintse meg [az](how-to-configure-auto-train.md#ensemble) automatikus gépi tanulás alapértelmezett együttesbeállításainak módosításához.

## <a name="use-with-onnx"></a>Használata onnx-szal

Az Azure Machine Learning segítségével automatikus ML-t hozhat létre egy Python-modell, és az ONNX formátumba konvertálható. Miután a modellek ONNX formátumban vannak, különböző platformokon és eszközökön futtathatók. További információ az [ONNX-szel történő ML-modellek gyorsításáról.](concept-onnx.md)

Tekintse meg, hogyan konvertálhat ONNX formátumra [ebben a Jupyter notebook példában.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Ismerje meg, hogy az [ONNX mely algoritmusokat támogatja.](how-to-configure-auto-train.md#select-your-experiment-type)

Az ONNX futásidejű is támogatja a C#, így a c# alkalmazásokban automatikusan beépített modellt használhatja anélkül, hogy újrakódolnia kellene, vagy a REST-végpontok által bevezetett hálózati késéseket. További információ [az ONNX-modellek ONNX-futásidejű C# API-val való következtetéséről.](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md) 

## <a name="automated-ml-in-azure-machine-learning"></a>Automatizált ML az Azure Machine Learningben

Az Azure Machine Learning két élményt kínál az automatizált ml-es nyelvkörnyezethez

* A kód tapasztalt ügyfelek, [az Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* A korlátozott/nincs kódélményben az ügyfelek számára az Azure Machine Learning[https://ml.azure.com](https://ml.azure.com/)  

A következő összegzi az egyes tapasztalatok által támogatott magas szintű automatizált gépi tanulási képességeket.

<a name="parity"></a>

### <a name="experiment-settings"></a>Kísérlet beállításai 

A következő beállítások lehetővé teszik az automatikus gépelési kísérlet konfigurálását. 

| | Python SDK| Studio
----|:----:|:----:
Adatok felosztása vonatra/érvényesítési készletekre| ✓|✓
Támogatja a pénzmosási feladatokat: besorolás, regresszió és előrejelzés| ✓| ✓
Optimalizálás az elsődleges mutató alapján| ✓| ✓
Támogatja az AML-számítást számítási célként | ✓|✓
Előrejelzési horizont konfigurálása, célkésések & gördülő ablak|✓|✓
Kilépési feltételek megadása |✓|✓ 
Egyidejű ismétlések beállítása| ✓|✓
Oszlopok eldobása| ✓|✓
Blokk algoritmusok|✓|✓
Keresztellenőrzés |✓|✓
Támogatja az Azure Databricks-fürtök betanítását| ✓|
Tervezett szolgáltatásnevek megtekintése|✓|
Felvázolás összegzése| ✓|
Ünnepi featurization|✓|
Naplófájlok részletességi szintje| ✓|

### <a name="model-settings"></a>Modellbeállítások

Ezek a beállítások az automatikus gépi tanulási kísérlet eredményeképpen a legjobb modellre alkalmazhatók.

||Python SDK|Studio
----|:----:|:----:
A legjobb modell regisztráció| ✓|✓
A legjobb modell telepítés| ✓| ✓
A legjobb modell megmagyarázhatósága| ✓|✓
Szavazási együttes engedélyezése & veremegyüttes modellekhez| ✓|✓
A legjobb modell megjelenítése nem elsődleges mutató alapján|✓|
AZ ONNX-modell kompatibilitásának engedélyezése/letiltása|✓|
A modell tesztelése | ✓| |

### <a name="run-control-settings"></a>Vezérlőbeállítások futtatása

Ezek a beállítások lehetővé teszik a kísérletfuttatások és a gyermek futtatások áttekintését és vezérlését. 

||Python SDK| Studio
----|:----:|:----:
Összegző tábla futtatása| ✓|✓
Futtatás megszakítása| ✓|✓
Gyermekfuttatás megszakítása| ✓| ✓
Get védőkorlát| ✓|✓
Szünetfuttatás| ✓| 
Folytatás futtatása| ✓| 

## <a name="next-steps"></a>További lépések

Tekintse meg a példákat, és ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulással:

+ Kövesse az [oktatóanyagot: Regressziós modell automatikus betanítása az Azure Machine Learning segítségével](tutorial-auto-train-models.md)

+ Az automatikus betanítási kísérlet beállításainak konfigurálása:
  + Az Azure Machine Learning stúdióban [kövesse ezeket a lépéseket.](how-to-use-automated-ml-for-ml-models.md)
  + A Python SDK-val [kövesse ezeket a lépéseket.](how-to-configure-auto-train.md)

+ Ismerje meg, hogyan lehet automatikusan betanítási idősorozat-adatok használatával, [kövesse ezeket a lépéseket.](how-to-auto-train-forecast.md)

+ Próbálja ki a [Jupyter Notebook-mintákat az automatizált gépi tanuláshoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Az automatikus ml más Microsoft-megoldásokban is elérhető, például [ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsightban,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI-ban](https://docs.microsoft.com/power-bi/service-machine-learning-automated) és [SQL Serverben](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
