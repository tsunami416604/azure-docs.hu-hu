---
title: Gépi tanulási folyamatok ismétlése és fejlesztése
titleSuffix: Azure Machine Learning
description: Minták, gyakorlatok és tippek a gyors fejlesztéshez
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858196"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Gépi tanulási folyamatok ismétlése és fejlesztése

Azure Machine Learning folyamatok hatékony módszert biztosítanak a kódok modularize, az eredmények újbóli felhasználására és a számítási erőforrások optimalizálására. Íme néhány gyakorlati tipp és eljárás a folyamatok használatáról.

## <a name="how-do-you-get-started-with-pipelines"></a>Hogyan kezdheti meg a folyamatokat?

Az első lépésekhez több lehetőség is rendelkezésre áll, ha az új folyamatok:

* Ha a legjobbat az építési folyamat elolvasásával és újbóli létrehozásával tanulja meg, a cikk a [Machine learning-folyamatok létrehozása és futtatása a Azure Machine learning SDK-val](how-to-create-your-first-pipeline.md) jó illeszkedést biztosít 
* Ha interaktív módon szeretné megtanulni egy Jupyter-jegyzetfüzetben, akkor a [Azure Machine learning folyamatokban fejlesztett folyamat: első lépések](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) notebook a legmegfelelőbb
* Ha inkább a kód-első helyzetet részesíti előnyben, akkor a [Azure Machine learning-folyamatok bemutató](https://github.com/microsoft/aml-pipelines-demo) -tárházának klónozása jó kiindulási pontot biztosít

## <a name="how-do-you-modularize-pipeline-code"></a>Hogyan modularize a folyamat kódja? 

A modulok és az `ModuleStep` osztály nagyszerű lehetőséget biztosít a ml-kódok modularize. Azt azonban szem előtt kell tartani, hogy a folyamat lépései közötti váltás mérhetetlenül drágább, mint a függvények hívása. A kérdés, amit meg kell kérnie, nem annyira "ezek a függvények és adatok koncepcionálisan eltérőek, mint a többi szakaszban?" de szeretném, hogy a függvények és az adatok külön fejlődnek? vagy a számítás költséges, és felhasználható a kimenet? További információ: thisn'tebook [modul létrehozása, ModuleVersion és azok használata egy folyamatba a ModuleStep használatával](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Ahogy korábban már említettük, az adatok előkészítésének a képzésből való elválasztása gyakran egy ilyen lehetőség. Előfordulhat, hogy az adatelőkészítés bonyolult és időigényes, ezért a folyamatot külön folyamat lépéseire lehet bontani. Egyéb lehetőségek közé tartozik a képzés utáni tesztelés és elemzés. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Hogyan gyorsítható meg a folyamat-iteráció? 

A folyamatok gyors iterációjának gyakori módszerei a következők: 

- A folyamat klónozása (másolat készítése) és a folyamat újrafuttatása gyorsan
- A számítási példány futásának fenntartása, hogy elkerülje az indítási időt
- Az adatmennyiség és a lépések konfigurálása az ismételt használat engedélyezéséhez lehetővé teszi, hogy a folyamat kihagyja az adatmódosítások újraszámítását

Ha gyorsan meg szeretné ismételni a folyamatot, a folyamat klónozásával elvégezheti a folyamatot, majd újra futtathatja a folyamatot. Egy másik hasznos módszer az, ha a számítási meleget tartja, nem számítunk fel díjat az új számítási feladatokhoz. Ha úgy állítja be a lépést, hogy lehetővé tegye egy Futtatás eredményének újbóli használatát, akkor az ismételt végrehajtás a lehetséges módon újrahasznosítja az eredményeket (ha a lépések nem változnak).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Hogyan működhet együtt a ML-folyamatok? 

A különböző folyamatok olyan természetes vonalak, amelyeken a tevékenységek szét vannak osztva. Több fejlesztő vagy akár több csapat is dolgozhat különböző lépéseken, ha a lépések között áthaladó adatmennyiségek és argumentumok megegyeznek. 

Az aktív fejlesztés során lekérheti `PipelineRun` és `StepRun` futtathatja az eredményeket a munkaterületről, ezekkel az objektumokkal letöltheti a végleges és a köztes kimenetet, és használhatja ezeket az összetevőket a saját moduláris feladataihoz.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Folyamatok elkülönítése a technikák teszteléséhez

A valós szintű ML-megoldások általában az összes lépés jelentős testreszabásával járnak. A nyers adatfeldolgozást gyakran elő kell készíteni valamilyen módon: szűrve, átalakítva és kibővítve. A betanítási folyamatokhoz több lehetséges architektúra is tartozhat, és a Deep learning esetében a rétegek és az aktiválási függvények számos lehetséges variációja lehet. A hiperparaméter-keresés egységes architektúrával is jelentős WINS-t eredményezhet.

Az eszközök, például a [AutoML](concept-automated-ml.md) és az [automatizált hiperparaméter-keresések](how-to-tune-hyperparameters.md)mellett a folyamatok az a/B tesztelési megoldások fontos eszközei lehetnek. Ha a folyamat lépéseinek több változata is van, egyszerűen létrehozhat külön futtatásokat a következő variációk kipróbálásához: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

