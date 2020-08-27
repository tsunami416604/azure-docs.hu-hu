---
title: Hibakeresés & ML-folyamatok hibaelhárítása
titleSuffix: Azure Machine Learning
description: Azure Machine Learning folyamatok hibakeresése a Pythonban. Ismerje meg a folyamatok fejlesztésével kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket. Ismerje meg, hogyan használható a Visual Studio Code interaktív módon a gépi tanulási folyamatok hibakereséséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: a036cb4212b0237bea1c8509532dc78d469acb17
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950153"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Hibakeresés és hibaelhárítás a gépi tanulási folyamatokban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibakeresést a [gépi tanulási folyamatokban](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -ban és [Azure Machine learning Designerben (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/concept-designer). 

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat a folyamat fejlesztése során felmerülő gyakori problémákat tartalmazza, és lehetséges megoldásokat tartalmaz.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem sikerült átadni az `PipelineData` adatkönyvtárat | Győződjön meg arról, hogy létrehozott egy könyvtárat a parancsfájlban, amely megfelel annak, ahol a folyamat a lépés kimeneti adatait várja. A legtöbb esetben a bemeneti argumentum meghatározza a kimeneti könyvtárat, majd explicit módon létrehozza a könyvtárat. `os.makedirs(args.output_dir, exist_ok=True)`A paranccsal hozhatja létre a kimeneti könyvtárat. Tekintse meg az [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) egy pontozási parancsfájl példája, amely ezt a kialakítási mintát mutatja. |
| Függőségi hibák | Ha olyan függőségi hibákat lát a távoli folyamatokban, amelyek helyi teszteléskor nem történtek meg, ellenőrizze, hogy a távoli környezet függőségei és verziói megfelelnek-e a tesztkörnyezetben. (Lásd: [környezetek kiépítése, gyorsítótárazása és újrafelhasználása](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Nem egyértelmű hibák a számítási célokkal | Próbálkozzon a számítási célok törlésével és újbóli létrehozásával. A számítási célok újbóli létrehozása gyorsan elvégezhető, és bizonyos átmeneti problémák is megoldhatók. |
| A folyamat nem használja újra a lépéseket | Az ismételt használat alapértelmezés szerint engedélyezve van, de gondoskodjon arról, hogy ne tiltsa le egy folyamat lépéseiben. Ha az újbóli használat le van tiltva, a `allow_reuse` lépésben megadott paraméter a következő lesz: `False` . |
| A folyamat feleslegesen fut újra | Annak biztosítása érdekében, hogy a lépések csak akkor fussanak újra, amikor a mögöttes adatokat vagy parancsfájlokat módosítják, az egyes lépésekhez tartozó forráskód-címtárakat le kell választva. Ha ugyanazt a könyvtárat használja több lépéshez, előfordulhat, hogy szükségtelen ismétléseket tapasztal. Használja a `source_directory` paramétert egy folyamat lépés objektumon, hogy az elkülönített könyvtárba mutasson erre a lépésre, és győződjön meg arról, hogy nem ugyanazt az `source_directory` útvonalat használja több lépéshez. |


## <a name="debugging-techniques"></a>Hibakeresési technikák

A folyamatok hibakereséséhez három fő módszer áll rendelkezésre: 

* Az egyes folyamatokkal kapcsolatos lépések hibakeresése a helyi számítógépen
* A naplózás és a Application Insights használatával elkülönítheti és diagnosztizálhatja a probléma forrását
* Távoli hibakereső csatolása az Azure-ban futó folyamatokhoz

### <a name="debug-scripts-locally"></a>Parancsfájlok helyi hibakeresése

A folyamat egyik leggyakoribb meghibásodása, hogy a tartomány parancsfájlja nem a kívánt módon fut, vagy futásidejű hibákat tartalmaz a távoli számítási környezetben, amely nehezen hibakeresést végez.

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

## <a name="configure-write-to-and-review-pipeline-logs"></a>A folyamat naplófájljainak konfigurálása, írása és áttekintése

A parancsfájlok helyi tesztelése nagyszerű módja annak, hogy a folyamat megkezdése előtt hibakeresést végezzen a fő kódrészletek és az összetett logika között, de előfordulhat, hogy a tényleges folyamat futtatásakor valószínűleg a parancsfájlokat kell hibakeresést végeznie, különösen a folyamat lépései közötti interakció során felmerülő viselkedés diagnosztizálásakor. Javasoljuk, hogy az utasítások liberális használatát `print()` a lépés parancsfájljaiban is megtekintheti, így a távoli végrehajtás során az objektum állapota és a várt értékek láthatók, hasonlóan a JavaScript-kód hibakereséséhez.

### <a name="logging-options-and-behavior"></a>Naplózási beállítások és viselkedés

Az alábbi táblázat a folyamatok különböző hibakeresési lehetőségeiről nyújt információt. Nem kimerítő lista, mert az itt látható Azure Machine Learning, Python és OpenCensus mellett más lehetőségek is vannak.

| Kódtár                    | Típus   | Példa                                                          | Cél                                  | További források                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Azure Machine Learning portál felhasználói felülete             | [Kísérletek nyomon követése](how-to-track-experiments.md)<br>[azureml. Core. Run osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python-nyomtatás/-naplózás    | Napló    | `print(val)`<br>`logging.info(message)`                          | Illesztőprogram-naplók, Azure Machine Learning Designer | [Kísérletek nyomon követése](how-to-track-experiments.md)<br><br>[Python-naplózás](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Napló    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – nyomkövetés                | [Folyamatok hibakeresése az Application Insightsban](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportálók](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-naplózási szakácskönyv](https://docs.python.org/3/howto/logging-cookbook.html) |

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

### <a name="finding-and-reading-pipeline-log-files"></a>A folyamat naplófájljainak keresése és olvasása

A naplófájl `70_driver_log.txt` tartalma: 

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

## <a name="logging-in-azure-machine-learning-designer-preview"></a>Bejelentkezés Azure Machine Learning Designerben (előzetes verzió)

A tervezőben létrehozott folyamatok esetében az **70_driver_log** fájlt a szerzői műveletek lapon vagy a folyamat futtatása Részletek lapon találja.

### <a name="enable-logging-for-real-time-endpoints"></a>Valós idejű végpontok naplózásának engedélyezése

A tervezőben a valós idejű végpontok hibaelhárításához és hibakereséséhez engedélyeznie kell az alkalmazás-betekintési naplózást az SDK használatával. A naplózás lehetővé teszi a modell üzembe helyezési és használati problémáinak hibaelhárítását és hibakeresését. További információ: [a telepített modellek naplózása](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Naplók beolvasása a szerzői műveletek lapról

Amikor elküld egy folyamat futását, és az authoring (szerzői műveletek) oldalon marad, megkeresheti az egyes modulokhoz generált naplófájlokat, mivel az egyes modulok futtatása befejeződött.

1. Válassza ki azt a modult, amely a szerzői műveletek vásznon fut.
1. A modul jobb oldali ablaktáblájában lépjen a  **kimenetek és naplók** lapra.
1. Bontsa ki a jobb oldali ablaktáblát, és válassza ki a **70_driver_log.txt** a fájl böngészőben való megtekintéséhez. A naplókat helyileg is letöltheti.

    ![Kibontott kimeneti ablaktábla a tervezőben](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Naplók beolvasása a folyamat-futtatásokból

A naplófájlokat meghatározott futtatásokhoz is megtalálhatja a folyamat futtatása Részletek lapon, amely a Studióban található **folyamatok** vagy **kísérletek** szakaszban található.

1. Válassza ki a tervezőben létrehozott folyamat-futtatást.

    ![Folyamat futtatása lap](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Válasszon ki egy modult a betekintő ablaktáblán.
1. A modul jobb oldali ablaktáblájában lépjen a  **kimenetek és naplók** lapra.
1. A jobb oldali ablaktábla kibontásával megtekintheti a **70_driver_log.txt** fájlt a böngészőben, vagy kiválaszthatja a fájlt a naplók helyi letöltéséhez.

> [!IMPORTANT]
> Ha frissíteni szeretne egy folyamatot a folyamat futásának részletei lapon, a **clone** folyamatot egy új folyamat-piszkozatra kell futtatnia. A folyamat futtatása a folyamat pillanatképe. A naplófájlhoz hasonló, és nem módosítható. 

## <a name="application-insights"></a>Application Insights
A OpenCensus Python-függvénytár ily módon történő használatával kapcsolatos további információkért tekintse meg a következő útmutatót: a [gépi tanulási folyamatok hibakeresése és hibaelhárítása Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktív hibakeresés a Visual Studio Code-ban

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a ML-folyamaton használt Python-kóddal. A Visual Studio Code (VS Code) és a debugpy használatával a kódot a betanítási környezetben futtatva is csatlakoztathatja. További információkért tekintse meg az [interaktív hibakeresést a vs Code útmutatóban](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az SDK-referenciát a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag súgójában.

* Tekintse meg a [Designer-kivételek és-hibakódok](algorithm-module-reference/designer-error-codes.md)listáját.
