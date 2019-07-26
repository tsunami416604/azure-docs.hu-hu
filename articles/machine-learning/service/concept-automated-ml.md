---
title: Mi az az automatizált ML/automl
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy Azure Machine Learning szolgáltatás hogyan tud automatikusan kiválasztani egy algoritmust, és létrehoz egy modellt, hogy időt takarítson meg az Ön által megadott paraméterek és feltételek használatával, hogy kiválassza a modellhez legmegfelelőbb algoritmust.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: c563278a9d23810a5e6f0adc8082c8cfc5a0510c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358856"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Az automatizált gépi tanulás, más néven autoML, az időigényes automatizálási folyamat, a gépi tanulási modellek fejlesztésének ismétlődő feladatai. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy a modell minőségének fenntartása mellett nagy mennyiségű, hatékonyságú és termelékenységű ML-modellt építsenek.

A hagyományos gépi tanulási modell fejlesztése erőforrás-igényes, és jelentős tartományi ismereteket és időt igényel, hogy több tucat modellt hozzon létre és hasonlítson össze. Alkalmazzon automatikus ML-t, ha azt szeretné, hogy a Azure Machine Learning egy modell betanítását és finomhangolását a megadott cél metrika használatával. A szolgáltatás ezután megismétli az ML-algoritmusokat a funkciók kiválasztásával párosítva, ahol minden egyes iteráció egy modellt hoz létre egy képzési pontszámmal. Minél magasabb a pontszám, annál jobb lesz a modellnek az adataihoz igazodni.

Az automatizált gépi tanulással felgyorsíthatja az éles használatra kész ML-modellek gyors és hatékony beszerzéséhez szükséges időt.

## <a name="when-to-use-automated-ml"></a>Mikor kell használni az automatikus ML-t

A gépi tanulási modell fejlesztési folyamata automatizált ML-ezáltal demokratikussá teszi, és lehetővé teszi a felhasználók számára, hogy az adatelemzési szakértelmük alapján minden probléma esetén azonosíthatók legyenek a teljes gépi tanulási folyamatok.

Az adatszakértők, az elemzők és a fejlesztők az egész iparágban az automatizált ML-ket használhatják:

+ Gépi tanulási megoldások megvalósítása kiterjedt programozási ismeretek nélkül
+ Időt és erőforrásokat takaríthat meg
+ Az adatelemzési ajánlott eljárások kihasználása
+ Gyors problémamegoldás

## <a name="how-automated-ml-works"></a>Az automatizált ML működése

A **Azure Machine learning szolgáltatás**használatával a következő lépésekkel megtervezheti és futtathatja az automatizált ml-betanítási kísérleteket:

1. **Azonosítsa a megoldandó problémát** : besorolás, előrejelzés vagy regresszió

1. A címkézett betanítási **adatmennyiség forrásának és formátumának meghatározása**: NumPy tömbök vagy pandák dataframe

1. **Konfigurálja a számítási célt a modell**betanításához, például a [helyi számítógép, a Azure Machine learning számítások, a távoli virtuális gépek vagy a Azure Databricks](how-to-set-up-training-targets.md).  Ismerje meg a [távoli erőforrások](how-to-auto-train-remote.md)automatizált képzését.

1. **Konfigurálja az automatikus gépi tanulás paramétereit** , amelyek meghatározzák, hogy a különböző modellek, a hiperparaméter-beállítások, a speciális előfeldolgozási/featurization, valamint a legjobb modell meghatározásakor milyen mérőszámokat kell megvizsgálni.  Megadhatja az automatikus betanítási kísérlet beállításait [Azure Portal](how-to-create-portal-experiments.md) vagy [az SDK-val](how-to-configure-auto-train.md).

1. **A betanítási Futtatás elküldése.**

  ![Automatizált gépi tanulás](./media/how-to-automated-ml/automl-concept-diagram2.png)

A betanítás során a Azure Machine Learning szolgáltatás számos párhuzamos folyamatot hoz létre, amelyek különböző algoritmusokat és paramétereket próbálnak ki. Ekkor leáll, ha eléri a kísérletben meghatározott kilépési feltételeket.

Megvizsgálhatja a naplózott futtatási adatokat is, amelyek a Futtatás során összegyűjtött [mérőszámokat tartalmazzák](how-to-understand-automated-ml.md) . A betanítási kísérlet egy Python szerializált objektumot`.pkl` (fájlt) hoz létre, amely tartalmazza a modellt és az adatfeldolgozást.

A modell létrehozása automatizálható, és azt is megtudhatja, [milyen fontos vagy releváns funkciók vannak](how-to-configure-auto-train.md#explain) a generált modellekhez.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozás

Minden automatizált gépi tanulási kísérlet során az adatai az alapértelmezett módszerekkel és opcionálisan a speciális előfeldolgozással vannak feldolgozva.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozás (standard)

Az automatizált gépi tanulási kísérletek során az adatok automatikusan méretezhetők vagy normalizálva vannak, hogy az algoritmusok jól elvégezhetők legyenek.  A modellek betanítása során a rendszer az alábbi skálázási vagy normalizáló technikák egyikét alkalmazza az egyes modellekre.

|&nbsp;Méretezésnormalizálása&&nbsp;| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szabványosítási funkciók az átlag és a skálázás egységbeli eltérésének eltávolításával  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Az egyes szolgáltatások méretezésével átalakítja a szolgáltatásokat az adott oszlop minimális és maximális értékével  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Az egyes szolgáltatások méretezése a maximális abszolút értékkel |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |A skálázási funkciók a quantile-tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris dimenzióját-csökkentés az adatértékek számának kibontásával a projekthez egy alacsonyabb dimenziós területre |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor lineáris dimenzióját-csökkentést végez a csonkolt számú érték lebomlásával (SVD). A PCA-vel ellentétben ez a kalkulátor nem teszi lehetővé az adatközpontok megfogalmazását az egyes számokból származó érték kibontása előtt. Ez azt jelenti, hogy a SciPy. a ritka mátrixok hatékonyan használhatók |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | A rendszer minden mintát (azaz az Adatmátrix minden sorát) legalább egy nullától eltérő összetevővel együtt átméretezi a többi mintától függetlenül, így annak normája (L1 vagy L2) eggyel egyenlő |

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozás: opcionális featurization

További speciális előfeldolgozási és featurization is elérhetők, például a hiányzó értékek: imputálási, kódolás és átalakítások. [További információ arról, hogy milyen featurization tartalmaz](how-to-create-portal-experiments.md#preprocess). A beállítás engedélyezése a következővel:

+ Azure Portal: A [fenti lépésekkel](how-to-create-portal-experiments.md)válassza az előfeldolgozás jelölőnégyzetet a **speciális beállításokban** .

+ Python SDK: `"preprocess": True` [Az osztálymegadása`AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Idősoros előrejelzés
Az előrejelzések az üzleti tevékenység szerves részét képezik, függetlenül attól, hogy bevételi, leltározási, értékesítési vagy vevői igények. Az automatikus ML-vel kombinálhatja a technikákat és a megközelítéseket, és egy ajánlott, magas színvonalú idősorozat-előrejelzést is igénybe vehet. 

Az automatikus idősorozat-kísérletet többváltozós regressziós problémaként kezeli a rendszer. A korábbi idősorozat-értékek "Pivotal", hogy további dimenziókat regressor a többi előrejelzővel együtt. Ez a klasszikus idősorozat-módszerekkel ellentétben az egyik előnye, hogy természetesen több kontextusos változót is magában foglal, és a képzés során egymáshoz fűződő kapcsolataikat. Az automatizált ML egyetlen, de gyakran belsőleg elágazó modellt tanul az adatkészlet összes eleméhez és előrejelzési horizontokhoz. Így több adat érhető el a modell paramétereinek becsléséhez és az általánosításhoz, hogy a láthatatlan adatsorozatok elérhetővé válnak. 

További információ: az [automatikus gépi tanulásra vonatkozó](how-to-auto-train-forecast.md)példa az idősorozat-előrejelzéshez.

## <a name="ensemble-models"></a>Ensemble-modellek

Az Ensemble-modelleket az automatikus gépi tanulással is betaníthatja az [Caruana Ensemble kiválasztási algoritmusával a rendezett Ensemble inicializálásával](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). A Ensemble learning a gépi tanulási eredmények és a prediktív teljesítmény növelésével több modellt is használhat egyetlen modell helyett. A Ensemble iteráció a Futtatás utolsó ismétlése jelenik meg.

## <a name="use-with-onnx-in-c-apps"></a>Használat a ONNX C# alkalmazásokban

A Azure Machine Learning használatával a Python-modell létrehozásához és a ONNX-formátumra való átalakításához használhatja az automatikus ML-t. Az ONNX Runtime támogatja C#, így az C# alkalmazásokban automatikusan létrehozott modell újrakódolás vagy a REST-végpontok által bevezethető hálózati késések nélkül is használható. Próbálja ki ezt a folyamatot ebben a [Jupyter-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatizált ML a Microsofton belül

Az automatikus ML más Microsoft-megoldásokban is elérhető, például:

|Integráció|Leírás|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatikus modell kiválasztása és betanítása .NET-alkalmazásokban a Visual Studio és a Visual Studio Code ML.NET automatizált ML (előzetes verzió) használatával.|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|A HDInsight-fürtökön párhuzamosan végezheti el az automatizált ML-betanítási feladatok felskálázását a Sparkban.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Gépi tanulási modellek meghívása közvetlenül Power BI (előzetes verzió).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Új gépi tanulási modelleket hozhat létre SQL Server 2019 big data-fürtök adatain keresztül.|

## <a name="next-steps"></a>További lépések

Tekintse át a példákat, és Ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulás használatával:

+ Kövesse az [oktatóanyagot: Regressziós modell automatikus betanítása az Azure automatizált Machine Learning](tutorial-auto-train-models.md)

+ Konfigurálja az automatikus betanítási kísérlet beállításait:
  + Azure Portal felületen hajtsa [végre ezeket a lépéseket](how-to-create-portal-experiments.md).
  + A Python SDK használatával hajtsa [végre az alábbi lépéseket](how-to-configure-auto-train.md).

+ Ismerje meg, hogyan [végezheti](how-to-auto-train-forecast.md)el az automatikus betanítást az idősorozat-adatkészletek használatával.

+ Próbálja ki [Jupyter notebook mintákat az automatizált gépi tanuláshoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
