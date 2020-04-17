---
title: Az Azure Stream Analytics integrálása az Azure Machine Learningszolgáltatással
description: Ez a cikk bemutatja, hogyan integrálható az Azure Stream Analytics-feladat az Azure Machine Learning-modellek.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 07fa72f086b676723279ee4b8efd927beb2692f0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481973"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Az Azure Stream Analytics integrálása az Azure Machine Learning (előzetes verzió) szolgáltatással

Gépi tanulási modelleket valósíthat meg felhasználó által definiált függvényként (UDF) az Azure Stream Analytics-feladatokban, hogy valós idejű pontozási és előrejelzéseket hajtson végre a streamelési bemeneti adatokon. [Az Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) lehetővé teszi, hogy bármilyen népszerű nyílt forráskódú eszközt, például a Tensorflow- t, a scikit-learn-t vagy a PyTorch-ot használjon a modellek előkészítéséhez, betanításához és üzembe helyezéséhez.

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el. Ezt a funkciót az Azure Portalon csak a [Stream Analytics portál előzetes hivatkozásával](https://aka.ms/asaportalpreview)érheti el. Ez a funkció a [Visual Studio Stream Analytics eszközeinek](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)legújabb verziójában is elérhető.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt gépi tanulási modellt ad hozzá függvényként a Stream Analytics-feladathoz, hajtsa végre az alábbi lépéseket:

1. Az Azure Machine Learning használatával [webszolgáltatásként telepítheti a modellt.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. A pontozási [parancsfájlnak rendelkeznie kell mintabemenetekkel és kimenetekkel,](../machine-learning/how-to-deploy-and-where.md#example-entry-script) amelyeket az Azure Machine Learning egy sémaspecifikáció létrehozásához használ. A Stream Analytics a sémát használja a webszolgáltatás függvényaláírásának megértéséhez.

3. Győződjön meg arról, hogy a webszolgáltatás elfogadja és visszaadja a JSON szerializált adatait.

4. Telepítse a modellt az [Azure Kubernetes szolgáltatás](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) nagy méretű éles környezetben. Ha a webszolgáltatás nem tudja kezelni a feladatból érkező kérések számát, a Stream Analytics-feladat teljesítménye leromlott, ami hatással van a késésre. Az Azure Container Instances-on telepített modellek ma nem támogatottak, de az elkövetkező hónapokban válnak elérhetővé.

## <a name="add-a-machine-learning-model-to-your-job"></a>Gépi tanulási modell hozzáadása a feladathoz

Az Azure Machine Learning-függvényeket közvetlenül az Azure Portalon keresztül adhat hozzá a Stream Analytics-feladathoz.

1. Nyissa meg a Stream Analytics-feladatot az Azure Portalon, és válassza a **Funkciók** lehetőséget a **Feladattopológia**területen. Ezután válassza az **Azure ML-szolgáltatás** a **+ Add** legördülő menüben.

   ![Azure ML UDF hozzáadása](./media/machine-learning-udf/add-azureml-udf.png)

2. Töltse ki az **Azure Machine Learning Service függvényűrlapot** a következő tulajdonságértékekkel:

   ![Azure ML UDF konfigurálása](./media/machine-learning-udf/configure-azureml-udf.png)

Az alábbi táblázat ismerteti az Azure ML-szolgáltatás függvényeinek egyes tulajdonságait a Stream Analytics ben.

|Tulajdonság|Leírás|
|--------|-----------|
|Függvény aliasa|Írjon be egy nevet a függvény meghívásához a lekérdezésben.|
|Előfizetés|Az Azure-előfizetése..|
|Azure ML-munkaterület|Az Azure Machine Learning-munkaterület, amelyet a modell webszolgáltatásként való üzembe helyezéséhez használt.|
|Központi telepítés|A modellt üzemeltető webszolgáltatás.|
|Függvény aláírása|A webszolgáltatás aláírása az API sémaspecifikációjából származik. Ha az aláírás nem töltődik be, ellenőrizze, hogy a pontozási parancsfájlban megadott-e mintabemenetet és kimenetet a séma automatikus létrehozásához.|
|A párhuzamos kérelmek száma partíciónként|Ez egy speciális konfiguráció a nagy léptékű átviteli sebességgel való optimalizáláshoz. Ez a szám a feladat egyes partícióiról a webszolgáltatásnak küldött egyidejű kérelmeket jelöli. A hat streamelési egységgel (SU) és az alacsonyabb egy partícióval rendelkező feladatok hoz nak létre. A 12 SUs-szal rendelkező feladatok két partícióval rendelkeznek, 18 SUs három partícióval rendelkezik, és így tovább.<br><br> Például ha a feladat két partíciót, és ezt a paramétert négyre állítja, nyolc egyidejű kérelmek et a feladatból a webszolgáltatás. A nyilvános előzetes verzió ban ez az érték alapértelmezés szerint 20 lesz, és nem frissíthető.|
|Kötegek maximális száma|Ez egy speciális konfiguráció a nagy léptékű átviteli sebességű optimalizálásához. Ez a szám a webszolgáltatásnak küldött egyetlen kérelemben kötegelt események maximális számát jelöli.|

## <a name="supported-input-parameters"></a>Támogatott bemeneti paraméterek

Amikor a Stream Analytics-lekérdezés meghívja az Azure Machine Learning UDF-et, a feladat létrehoz egy JSON-szerializált kérelmet a webszolgáltatásszámára. A kérelem egy modell-specifikus sémán alapul. A [séma automatikus létrehozásához](../machine-learning/how-to-deploy-and-where.md)meg kell adnia egy mintabemenetet és kimenetet a pontozási parancsfájlban. A séma lehetővé teszi, hogy a Stream Analytics a JSON szerializált kérelmet bármely támogatott adattípusok, például numpy, pandák és PySpark létrehozásához. Több bemeneti események kötegelhető konként egyetlen kérelemben.

A következő Stream Analytics-lekérdezés egy példa az Azure Machine Learning UDF meghívására:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

A Stream Analytics csak egy paraméter t támogatja az Azure Machine Learning-függvényekhez. Előfordulhat, hogy elő kell készítenie az adatokat, mielőtt átadná azt a gépi tanulási UDF bemenetként.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Több bemeneti paraméter átadása az UDF-nek

A gépi tanulási modellek bemeneteinek leggyakoribb példái a numpy tömbök és a DataFrames. JavaScript UDF használatával tömböt hozhat létre, és a `WITH` záradék kal jsonszerializált DataFrame-et hozhat létre.

### <a name="create-an-input-array"></a>Bemeneti tömb létrehozása

Létrehozhat egy JavaScript UDF-et, amely elfogadja *az N* bemenetek számát, és létrehoz egy tömböt, amely az Azure Machine Learning UDF bemeneteként használható.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Miután hozzáadta a JavaScript UDF-et a feladathoz, meghívhatja az Azure Machine Learning UDF-et a következő lekérdezéssel:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

A következő JSON egy példakérés:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Pandák vagy PySpark DataFrame létrehozása

A `WITH` záradék segítségével hozzon létre egy JSON szerializált DataFrame, amely átadható az Azure Machine Learning UDF az alábbiakszerint.

A következő lekérdezés létrehoz egy DataFrame a szükséges mezők kiválasztásával, és a DataFrame az Azure Machine Learning UDF bemenetként használja.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

A következő JSON egy példakérés az előző lekérdezésből:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Az Azure Machine Learning UDF-ek teljesítményének optimalizálása

Amikor telepíti a modellt az Azure Kubernetes szolgáltatásra, [profilozhatja a modellt az erőforrás-kihasználtság meghatározásához.](../machine-learning/how-to-deploy-and-where.md#profilemodel) Engedélyezheti az [App Insights-ot az üzembe helyezésekhez a](../machine-learning/how-to-enable-app-insights.md) kérelmek és a válaszidők és a hibaarányok megértéséhez.

Ha magas eseményátviteli sebességű forgatókönyvvel rendelkezik, előfordulhat, hogy módosítania kell a következő paramétereket a Stream Analytics-ben az optimális teljesítmény eléréséhez az alsó és teljes késleltetésű késésekkel:

1. Maximális kötegszám.
2. A párhuzamos kérelmek száma partíciónként.

### <a name="determine-the-right-batch-size"></a>A megfelelő kötegméret meghatározása

Miután üzembe helyezte a webszolgáltatást, a különböző kötegelt méretekkel rendelkező mintakérelmet küld50-től kezdődően, és több száz nagyságrendű nagyságrenddel növeli azt. Például 200, 500, 1000, 2000 és így tovább. Észre fogja venni, hogy egy bizonyos kötegméret után a válasz késése nő. A válaszkéses növekedés ének a feladat maximális kötegszámának kell lennie.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>A párhuzamos kérelmek partíciónkénti számának meghatározása

Optimális skálázás esetén a Stream Analytics-feladat képesnek kell lennie arra, hogy több párhuzamos kéréseket küldjön a webszolgáltatásnak, és néhány ezredmásodpercen belül választ kapjon. A webszolgáltatás válaszának késése közvetlenül befolyásolhatja a Stream Analytics-feladat késését és teljesítményét. Ha a feladatból a webszolgáltatáshoz érkező hívás hosszú időt vesz igénybe, valószínűleg a vízjel késleltetésének növekedését fogja látni, és az elmaradt bemeneti események számának növekedését is láthatja.

Az ilyen késés megelőzése érdekében győződjön meg arról, hogy az Azure Kubernetes-szolgáltatás (AKS) fürtje a [megfelelő számú csomóponttal és replikával](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)lett kiépítve. Rendkívül fontos, hogy a webszolgáltatás magas rendelkezésre állású, és sikeres válaszokat ad vissza. Ha a feladat nem érhető el választ (503) kap a webszolgáltatástól, akkor folyamatosan újra próbálkozik exponenciális visszalépéssel. A sikeres (200) és a szolgáltatáson kívüli bármely válasz (503) hibás állapotba kerül.

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Az Azure Stream Analytics felhasználói JavaScript-függvényei](stream-analytics-javascript-user-defined-functions.md)
* [Stream Analytics-feladat méretezése az Azure Machine Learning Studio (klasszikus) funkcióval](stream-analytics-scale-with-machine-learning-functions.md)

