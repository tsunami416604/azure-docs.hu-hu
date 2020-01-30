---
title: Gépi tanulási folyamatok hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: A gépi tanulási folyamatok hibakeresése és hibaelhárítása a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatok fejlesztésével kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 5ba26584f08e705b24749a76d6f607aa84b48fab
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769122"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Gépi tanulási folyamatok hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [gépi tanulási folyamatokban](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -ban és [Azure Machine learning Designerben (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/concept-designer).

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Hibakeresés és hibaelhárítás az Azure Machine Learning SDK-ban
A következő szakaszokban áttekintheti a folyamatok összeállításakor előforduló gyakori buktatókat, valamint a folyamatokban futó kód hibakereséséhez szükséges különböző stratégiákat. Ha nem sikerül a folyamat futtatása a várt módon, kövesse az alábbi tippeket.

### <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

A folyamat egyik leggyakoribb meghibásodása, hogy egy csatolt parancsfájl (adattisztítási parancsfájl, pontozási parancsfájl stb.) nem a kívánt módon fut, vagy futásidejű hibákat tartalmaz a távoli számítási környezetben, amelyek nehezen tudnak hibakeresést végezni az Azure-beli munkaterületen. Learning Studio. 

Maguk a folyamatok nem futtathatók helyileg, de a parancsfájlok a helyi gépen való elkülönítése lehetővé teszi a gyorsabb hibakeresést, mivel nem kell megvárnia a számítási és a környezeti felépítési folyamatra. Ehhez szükség van egy fejlesztési munkára:

* Ha az adatai egy felhőalapú adattárban találhatók, le kell töltenie az adatait, és elérhetővé kell tennie azt a parancsfájl számára. Az adat kis mintájának használatával lerövidítheti a futtatókörnyezetet, és gyors visszajelzést kaphat a parancsfájlok működéséről
* Ha egy közbenső folyamat lépését kísérli meg szimulálni, előfordulhat, hogy manuálisan kell létrehoznia az adott parancsfájlnak az előző lépésből várt típusú objektumokat.
* Emellett meg kell határoznia a saját környezetét, és replikálnia kell a távoli számítási környezetben meghatározott függőségeket.

Ha a parancsfájl beállítása a helyi környezetben való futtatásra van beállítva, sokkal egyszerűbb a hibakeresési feladatok végrehajtása, például:

* Egyéni hibakeresési konfiguráció csatolása
* A végrehajtás felfüggesztése és az objektum állapotának vizsgálata
* Olyan típusú vagy logikai hibák, amelyek nem lesznek elérhetők a futtatókörnyezetig

> [!TIP] 
> Ha ellenőrizni szeretné, hogy a parancsfájl a várt módon fut-e, egy jó következő lépés futtatja a szkriptet egy egylépéses folyamaton, mielőtt egy több lépésből álló folyamaton próbálja futtatni azt.

### <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

A parancsfájlok helyi tesztelése nagyszerű módja annak, hogy a folyamat megkezdése előtt hibakeresést végezzen a fő kódrészletek és az összetett logika között, de egy bizonyos ponton valószínűleg a szkripteket kell hibakeresést végeznie a tényleges folyamat futtatásakor, különösen ha a rendszer a probléma diagnosztizálását végzi a folyamat lépései közötti interakció során. Javasoljuk, hogy az `print()` utasítások liberális használatát adja meg a lépés parancsfájljaiban, hogy a távoli végrehajtás során az objektum állapota és a várt értékek láthatók legyenek, hasonlóan a JavaScript-kód hibakereséséhez.

A naplófájl `70_driver_log.txt` a következőket tartalmazza: 

* A parancsfájl végrehajtása során kinyomtatott utasítások
* A parancsfájl verem-nyomkövetése 

Ha a portálon szeretné megkeresni a többi naplófájlt, először kattintson a folyamat futtatására a munkaterületen.

![Folyamat futtatási listájának lapja](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navigáljon a folyamat futásának részletei lapra.

![Folyamat futásának részletes lapja](./media/how-to-debug-pipelines/pipelinerun-02.png)

Kattintson a modulra az adott lépéshez. Navigáljon a **naplók** lapra. Egyéb naplók a környezeti rendszerkép létrehozási folyamatával és a lépés-előkészítési parancsfájlokkal kapcsolatos információkat tartalmaznak.

![Folyamat futásának részletes lapja napló lapja](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> A *közzétett folyamatokra* vonatkozó futtatások a munkaterület **végpontok** lapján találhatók. A *nem közzétett folyamatokra* vonatkozó futtatások a **kísérletekben** vagy **folyamatokban**találhatók.

### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat a folyamat fejlesztése során felmerülő gyakori problémákat tartalmazza, és lehetséges megoldásokat tartalmaz.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem lehet átadni az adat`PipelineData` könyvtárba | Győződjön meg arról, hogy létrehozott egy könyvtárat a parancsfájlban, amely megfelel annak, ahol a folyamat a lépés kimeneti adatait várja. A legtöbb esetben a bemeneti argumentum meghatározza a kimeneti könyvtárat, majd explicit módon létrehozza a könyvtárat. A kimeneti könyvtár létrehozásához használja a `os.makedirs(args.output_dir, exist_ok=True)`. Tekintse meg az [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) egy pontozási parancsfájl példája, amely ezt a kialakítási mintát mutatja. |
| Függőségi hibák | Ha helyileg fejlesztett ki és tesztelt parancsfájlokat, de függőségi problémákat tapasztal, amikor távoli számítási folyamaton fut, ügyeljen arra, hogy a számítási környezet függőségei és verziói megfeleljenek a tesztkörnyezet feltételeinek. |
| Nem egyértelmű hibák a számítási célokkal | A számítási célok törlése és újbóli létrehozása a számítási célokkal kapcsolatos bizonyos problémák megoldására szolgál. |
| A folyamat nem használja újra a lépéseket | Az ismételt használat alapértelmezés szerint engedélyezve van, de gondoskodjon arról, hogy ne tiltsa le egy folyamat lépéseiben. Ha az újbóli használat le van tiltva, a lépésben szereplő `allow_reuse` paraméter értéke `False`. |
| A folyamat feleslegesen fut újra | Annak biztosítása érdekében, hogy a lépések csak akkor fussanak újra, amikor a mögöttes adatokat vagy parancsfájlokat módosítják, az egyes lépésekhez adja meg a címtárakat Ha ugyanazt a könyvtárat használja több lépéshez, előfordulhat, hogy szükségtelen ismétléseket tapasztal. A folyamat lépéseit tartalmazó objektum `source_directory` paraméterének használatával mutasson erre a lépésre az elkülönített könyvtárra, és győződjön meg arról, hogy nem ugyanazt a `source_directory` elérési utat használja több lépéshez. |

### <a name="logging-options-and-behavior"></a>Naplózási beállítások és viselkedés

Az alábbi táblázat a folyamatok különböző hibakeresési lehetőségeiről nyújt információt. Nem kimerítő lista, mert az itt látható Azure Machine Learning, Python és OpenCensus mellett más lehetőségek is vannak.

| Részletes ismertetés                    | Type (Típus)   | Példa                                                          | Cél                                  | Segédanyagok és eszközök                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Azure Machine Learning portál felhasználói felülete             | [Kísérletek nyomon követése](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml. Core. Run osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python-nyomtatás/-naplózás    | Napló    | `print(val)`<br>`logging.info(message)`                          | Illesztőprogram-naplók, Azure Machine Learning Designer | [Kísérletek nyomon követése](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python-naplózás](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Napló    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – nyomkövetés                | [Folyamatok hibakeresése Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportálók](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-naplózási szakácskönyv](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Naplózási beállítások – példa

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Hibakeresés és hibaelhárítás a Azure Machine Learning Designerben (előzetes verzió)

Ez a szakasz áttekintést nyújt a folyamatoknak a tervezőben való hibakereséséről.
A tervezőben létrehozott folyamatok esetében a **naplófájlokat** a szerzői műveletek lapon vagy a folyamat futtatása Részletek lapon találja.

### <a name="access-logs-from-the-authoring-page"></a>Naplók elérése a szerzői műveletek lapról

Amikor elküld egy folyamat futását, és az authoring (szerzői műveletek) oldalon marad, megkeresheti az egyes modulokhoz generált naplófájlokat.

1. Válasszon ki egy modult a szerzői műveletek vásznon.
1. A Tulajdonságok ablaktáblán lépjen a **naplók** lapra.
1. Válassza ki a naplófájlt `70_driver_log.txt`

    ![Szerzői műveletek lapjainak naplói](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Naplók elérése a folyamat-futtatásokból

Az adott Futtatások naplófájljait a folyamatok vagy **kísérletek** szakaszban **található folyamat futtatása** részletek lapján is megtalálhatja.

1. Válassza ki a tervezőben létrehozott folyamat-futtatást.
    ![folyamat futtatási lapja](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Válassza ki bármelyik modult az előnézet ablaktáblán.
1. A Tulajdonságok ablaktáblán lépjen a **naplók** lapra.
1. Válassza ki a naplófájlt `70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Hibakeresés és hibaelhárítás a Application Insights
A OpenCensus Python-függvénytár ily módon történő használatával kapcsolatos további információkért tekintse meg a következő útmutatót: a [gépi tanulási folyamatok hibakeresése és hibaelhárítása Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az SDK-referenciát a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag súgójában.

* Tekintse meg a [Designer-kivételek és-hibakódok](algorithm-module-reference/designer-error-codes.md)listáját.
