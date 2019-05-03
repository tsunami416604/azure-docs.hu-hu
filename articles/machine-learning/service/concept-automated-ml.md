---
title: Automatizált gépi Tanulási algoritmus kiválasztása és hangolás
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt, hogy időt takaríthat meg a paramétereket, és az Ön által megadott feltételeknek, a legjobb algoritmust határozza meg a modellhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cba01b5ad5c2cfea32f1faa6cb67c5ce7ee98fcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024902"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Automatizált gépi tanulás, más néven AutoML, lehetővé teszi az adatszakértők, az elemzők és a fejlesztők számára a nagy skálázást, a hatékonyság és a termelékenység gépi Tanulási modelleket építhetnek a fenntartási modellminőség mellett. 

Automatizált ML összeállít egy gépi Tanulási modelleket intelligensen, automatikusan kiválasztja a tanítási modell készletét, és javasol Önnek a legmegfelelőbb gazdagépet. Hagyományos machine learning-modell fejlesztői erőforrás-igényes jelentős szakterület ismeretének és létrehozására, és hasonlítsa össze a modellek tucat időt igénylő. Automatizált ML használatával fogjuk gyorsítsa fel az éles használatra kész, gépi Tanulási modelleket első nagyszerű egyszerű és hatékony kezeléséhez szükséges időt.

A színfalak mögött a betanítási adatok venni egy meghatározott célhoz szolgáltatással, és intelligensen iterálni alapján gépi Tanulási algoritmusok és a szolgáltatások kiválasztása kombinációit keresztül. Ezt követően képzési pontszámok alapján a legjobb illesztett modell azonosított és ajánlott Önnek. 

Továbbra is a kísérletet, és mi történik az átláthatóság hozzáféréssel rendelkeznek. Kísérletezéssel töltött időt, pontosságának vagy tevékenységsort, például alapján célok és megkötések megadása. Láthatja minden egyes létrehozott modell a betanítási folyamat minden alkalommal, és a legtöbb befolyásos funkciók egy adott modell a kísérlethez.

## <a name="how-automated-ml-works"></a>Hogyan automatizált ML működése

Használatával **Azure Machine Learning szolgáltatás**, tervezését és a Machine Learning betanítási automatizált kísérletek futtassa az alábbi lépéseket:

1. **Gépi tanulás probléma azonosítása** kell megoldani: besorolási, -előrejelzés vagy regressziós
   
1. **Adja meg a forrás- és a címkézett betanítási adatok formátumát**: Numpy tömbök vagy Pandas dataframe

1. **Konfigurálja a számítási célnak modell betanítása**, mint például a [helyi számítógépen, az Azure Machine Learning kiszámítja, távoli virtuális gépek vagy az Azure Databricks](how-to-set-up-training-targets.md).  További információ az automatikus képzési [távoli erőforrás](how-to-auto-train-remote.md).

1. **Konfigurálja az automatikus machine learning-paraméterek** amelyek meghatározzák, hogy hány ismétlések keresztül különböző modell, hiperparaméter beállításait, speciális előfeldolgozása/featurization, és milyen metrikákat, és tekintse meg a legjobb modellt meghatározásakor.  Automatikus betanítási kísérlet beállításait konfigurálhatja [az Azure Portalon](how-to-create-portal-experiments.md) vagy [az SDK-val](how-to-configure-auto-train.md).

1. **Küldje el a betanítási Futtatás.** 


[![Automatizált Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Betanítás, során az Azure Machine Learning szolgáltatás számos különböző algoritmusok és paraméterek próbálja folyamatok hoz létre. Miután a kilépési feltételeket a kísérletben definiált elér megszűnik. 

A naplózott futtatási információk, amely tartalmazza a futtatás során gyűjtött metrikák is ellenőrizheti. A betanítási Futtatás hoz létre egy Python-szerializált objektumot (`.pkl` fájl), amely tartalmazza a modell és az adatok előfeldolgozása.


Modell létrehozása automatikus, miközben is [megtudhatja, hogyan fontos és releváns szolgáltatások vannak](how-to-configure-auto-train.md#explain) a létrehozott modellek. 

> [!VIDEO https://www.youtube.com/embed/l8c-4iDPE0M]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Előfeldolgozása

Minden automatikus machine learning-kísérlet, az alapértelmezett a módszerekkel, és igény szerint – speciális előfeldolgozása az adatok előre feldolgozott.

### <a name="automatic-preprocessing-standard"></a>Automatikus előfeldolgozása (szokásos)
Az adatok minden automatikus machine learning-kísérlet, az automatikusan horizontálisan vagy normalized jól algoritmusok segítségével.  Során modell betanítása és a egy, a következő skálázás vagy a normalizálási módszerek lépnek érvénybe, mindegyik modellt.

|Skálázás&nbsp;&&nbsp;normalizálási| Leírás |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Szolgáltatások szabványosíthatja eltávolításával az átlag, és méretezési lehetőségek érhetők el egység variancia  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Szolgáltatások alakítja át az egyes szolgáltatások által az adott oszlop minimális és maximális méretezése  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    Az abszolút maximális értékét minden egyes szolgáltatás méretezése |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   A Scaler funkciók azok ki osztóérték tartomány szerint |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | A projekt azt egy alacsonyabb dimenzionális terület, az adatokat egyetlen érték felbontása segítségével lineáris dimenziócsökkentést | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    Ez átalakító lineáris dimenziócsökkentést jedné csonkolt érték idősorfelbontási (SVD) segítségével hajtja végre. Ellentétesen PEM ez estimator nem center az adatokat az egyes számú értéket idősorfelbontási számítástechnika előtt. Ez azt jelenti, hogy együtt tudjon működni az scipy.sparse mátrixok hatékonyan | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Mindegyik minta (vagyis az adatok mátrixban minden egyes sorára) legalább egy nem nulla értékű összetevővel van átméretezése más minták függetlenül, hogy az előírt (1 vagy 2.) egy egyenlő | 

### <a name="advanced-preprocessing-optional-featurization"></a>Speciális előfeldolgozása: nem kötelező featurization

További speciális előfeldolgozása és featurization is elérhetők, például a hiányzó értékeket imputálási, kódolási és átalakításokat. [További tudnivalók a milyen featurization megtalálható](how-to-create-portal-experiments.md#preprocess). Ez a beállítás engedélyezése:
+ Az Azure Portalon: Válassza a **Preprocess** a jelölőnégyzet a **speciális beállítások** [ezek a lépések](how-to-create-portal-experiments.md). 
+ Python SDK: Adjon meg `"preprocess": True` számára a [ `AutoMLConfig` osztály](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Ensemble modellek

Ensemble modellek az automatizált machine learning segítségével betaníthatja az [Caruana ensemble kijelölés algoritmust rendezett Ensemble inicializálási](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning machine learning eredményeket és prediktív teljesítménye növelhető a weboldalak százmilliárdjainak biztosítják a szolgáltatás egyetlen modellek használatával számos modellt. A Futtatás utolsó ismétlése ensemble ismétléseinek jelenik meg.

## <a name="use-with-onnx-in-c-apps"></a>Használja az ONNX- C# alkalmazások

Az Azure Machine Learning segítségével automatizált gépi Tanulási modell létrehozása a Python, és annak az ONNX-formátumra alakítja át. Az ONNX-modul támogatja C#, így használhatja a az automatikusan létrehozott modell az C# alkalmazások anélkül, hogy nem kell újraprogramozás, vagy a hálózati késések, amelyek REST-végpontokat. Próbálja ki ezt a folyamatot például [a Jupyter notebookot az](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>A Microsoft közötti automatikus gépi tanulás

Automatizált ML is érhető el a többi Microsoft-megoldások például:
+ A Visual Studio és a Visual Studio Code használatával .NET-alkalmazások [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/what-is-mldotnet)
+ [A HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), ahol ki lehet terjeszteni az automatizált gépi Tanulási tanítanak a Spark HDInsight-fürtök párhuzamosan. 
+ [A Power bi-ban](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>További lépések

Példák, és ismerje meg, hogyan hozhat létre automatizált Machine Learning használatával:

+ Kövesse a [oktatóanyag: Az Azure automatikus Machine Learning egy osztályozási modell automatikusan betanítása](tutorial-auto-train-models.md)

+ Adja meg az automatikus betanítási kísérlet beállításait: 
   + Az Azure portal felületén [ezekkel a lépésekkel](how-to-create-portal-experiments.md).
   + A Python SDK-val [ezekkel a lépésekkel](how-to-configure-auto-train.md).
  
 + Ismerje meg, hogyan train, idősorozat-adatok használatával automatikus [ezekkel a lépésekkel](how-to-auto-train-forecast.md).

+ Próbálja ki [Jupyter Notebook minták](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
