---
title: Gépi tanulási folyamatok hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: A gépi tanulási folyamatok hibakeresése és hibaelhárítása a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatok fejlesztésével kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket. Ismerje meg, hogyan használható a Visual Studio Code interaktív módon a gépi tanulási folyamatok hibakereséséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: c81d4db5798c15327e06471f1cb0da4841bd61b2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283445"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Gépi tanulási folyamatok hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [gépi tanulási folyamatokban](concept-ml-pipelines.md) a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -ban és [Azure Machine learning Designerben (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/concept-designer). A következő témakörben található információk:

* Hibakeresés a Azure Machine Learning SDK használatával
* Hibakeresés a Azure Machine Learning Designer használatával
* Hibakeresés Application Insights használatával
* Interaktív hibakeresés a Visual Studio Code (VS Code) és a Python Tools for Visual Studio (PTVSD) használatával

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

| Erőforrástár                    | Típus   | Példa                                                          | Cél                                  | További források                                                                                                                                                                                                                                                                                                                    |
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

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Hibakeresés és hibaelhárítás a Visual Studio Code-ban

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a ML-folyamaton használt Python-kóddal. A Visual Studio Code (VS Code) és a Python Tools for Visual Studio (PTVSD) használatával a kódot a betanítási környezetben futtatva is csatlakoztathatja.

### <a name="prerequisites"></a>Előfeltételek

* Egy __Azure Machine learning munkaterület__ , amely __Azure-Virtual Network__használatára van konfigurálva.
* Egy __Azure Machine learning folyamat__ , amely a folyamat lépéseinek részeként Python-parancsfájlokat használ. Például egy PythonScriptStep.
* Egy Azure Machine Learning számítási fürt, amely __a virtuális hálózaton__ található, és amelyet __a folyamat a képzéshez használ__.
* A __virtuális hálózatban__található __fejlesztési környezet__ . A fejlesztési környezet a következők egyike lehet:

    * Egy Azure-beli virtuális gép a virtuális hálózaton
    * Jegyzetfüzet virtuális gép számítási példánya a virtuális hálózaton
    * Egy virtuális magánhálózat (VPN) által a virtuális hálózathoz csatlakoztatott ügyfélszámítógép.

Az Azure Virtual Network és az Azure Machine Learning használatával kapcsolatos további információkért lásd: [Az Azure-beli Virtual Network-kísérletezés és a feladatok következtetése](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Működés

A ML-folyamat lépései Python-szkripteket futtatnak. Ezek a parancsfájlok a következő műveletek végrehajtásához vannak módosítva:
    
1. Naplózza annak a gazdagépnek az IP-címét, amelyen a futnak. Az IP-cím segítségével kapcsolja össze a hibakeresőt a parancsfájlhoz.

2. Indítsa el a PTVSD hibakeresési összetevőjét, és várjon, amíg a hibakereső csatlakozni próbál.

3. A fejlesztési környezetből megfigyelheti a betanítási folyamat által létrehozott naplókat, hogy megkeresse azt az IP-címet, amelyen a parancsfájl fut.

4. Ha azt szeretné, hogy az IP-cím a hibakeresőt egy `launch.json` fájl használatával kapcsolja össze, a VS code.

5. Csatlakoztatja a hibakeresőt, és interaktív módon átugorja a parancsfájlt.

### <a name="configure-python-scripts"></a>Python-parancsfájlok konfigurálása

A hibakeresés engedélyezéséhez végezze el a következő módosításokat az ML-folyamat lépései által használt Python-szkript (ek) ben:

1. Adja hozzá a következő importálási utasításokat:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Adja hozzá a következő argumentumokat. Ezekkel az argumentumokkal engedélyezheti a hibakeresőt igény szerint, és beállíthatja a hibakereső csatolásának időtúllépését:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Adja hozzá a következő utasításokat. Ezek az utasítások betöltik az aktuális futtatási környezetet, így naplózni lehet annak a csomópontnak az IP-címét, amelyen a kód fut:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adjon hozzá egy `if` utasítást, amely elindítja a PTVSD, és megvárja, amíg a hibakereső csatolva van. Ha az időkorlát előtt nincs hibakereső társítva, a parancsfájl a szokásos módon folytatódik.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

A következő Python-példa egy alapszintű `train.py` fájlt mutat be, amely lehetővé teszi a hibakeresést:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML folyamat konfigurálása

Ha meg szeretné adni a PTVSD indításához és a futtatási környezet beszerzéséhez szükséges Python-csomagokat, hozzon létre egy környezetet, és állítsa be `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`. Módosítsa az SDK verzióját úgy, hogy az megfeleljen a használtnak. A következő kódrészlet bemutatja, hogyan hozhat létre környezetet:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

A [Python-parancsfájlok konfigurálása](#configure-python-scripts) szakaszban két új argumentum lett hozzáadva az ml-folyamat lépései által használt parancsfájlokhoz. A következő kódrészlet azt mutatja be, hogyan használhatók ezek az argumentumok az összetevő hibakeresésének engedélyezéséhez, illetve időtúllépési érték megadásához. Azt is bemutatja, hogyan használható a korábban létrehozott környezet a `runconfig=run_config`beállításával:

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

A folyamat futásakor minden lépés létrehoz egy alárendelt futtatást. Ha a hibakeresés engedélyezve van, a módosított parancsfájl a gyermek futtatásához `70_driver_log.txt` következő szövegéhez hasonló adatokat naplóz:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Mentse a `ip_address` értéket. A következő szakaszban használatos.

> [!TIP]
> Az IP-címet az ehhez a folyamathoz tartozó alárendelt futtatási naplókból is megtalálhatja. Az információk megtekintésével kapcsolatos további információkért lásd: az [Azure ml-kísérletek futtatásának és metrikáinak monitorozása](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. Ha a VS Code fejlesztői környezetre szeretné telepíteni a Python Tools for Visual Studio (PTVSD), használja a következő parancsot:

    ```
    python -m pip install --upgrade ptvsd
    ```

    További információ a PTVSD és a VS Code használatával kapcsolatban: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Ha úgy szeretné konfigurálni a VS Code-t, hogy kommunikáljon a hibakeresőt futtató Azure Machine Learning számítási feladatokkal, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __hibakeresés__ menüt, majd válassza a __konfigurációk megnyitása__lehetőséget. Megnyílik egy __Launch. JSON__ nevű fájl.

    1. A __Launch. JSON__ fájlban keresse meg a `"configurations": [`tartalmazó sort, majd szúrja be a következő szöveget. Módosítsa az `"host": "10.3.0.5"` bejegyzést a naplókban az előző szakaszban visszaadott IP-címhez. Módosítsa a `"localRoot": "${workspaceFolder}/code/step"` bejegyzést egy helyi könyvtárba, amely a hibakereső parancsfájl másolatát tartalmazza:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Ha már vannak más bejegyzések a konfigurációk szakaszban, adjon hozzá egy vesszőt (,) a beszúrt kód után.

        > [!TIP]
        > Az ajánlott eljárás az, hogy a szkriptek erőforrásai külön címtárakban maradjanak, ezért a `localRoot` példa értéke `/code/step1`ra hivatkozik.
        >
        > Ha több parancsfájlt is hibakeresést végez, különböző címtárakban hozzon létre külön konfigurációs szakaszt minden parancsfájlhoz.

    1. Mentse a __Launch. JSON__ fájlt.

### <a name="connect-the-debugger"></a>A hibakereső összekötése

1. Nyissa meg a VS Code-ot, és nyissa meg a parancsfájl helyi példányát.
2. Állítsa be azokat a töréspontokat, amelyeken a parancsfájlt le szeretné állítani a csatolása után.
3. Amíg az alárendelt folyamat futtatja a szkriptet, és a `Timeout for debug connection` megjelenik a naplókban, használja az F5 billentyűt, vagy válassza a __hibakeresés__lehetőséget. Ha a rendszer kéri, válassza a __Azure Machine learning számítás: Távoli hibakeresési__ konfiguráció elemet. Azt is megteheti, hogy kijelöli a hibakeresés ikont az oldalsó sávon, a __Azure Machine learning: Távoli hibakeresési__ bejegyzést a hibakeresés legördülő menüből, majd a zöld nyíl használatával csatlakoztatja a hibakeresőt.

    Ezen a ponton a VS Code csatlakozik a PTVSD a számítási csomóponton, és a korábban beállított törésponton leáll. Most már megkezdheti a kód futtatását, megtekintheti a változókat stb.

    > [!NOTE]
    > Ha a napló egy `Debugger attached = False`t tartalmazó bejegyzést jelenít meg, akkor az időtúllépés lejárt, és a parancsfájl a hibakereső nélkül folytatódott. Küldje el újra a folyamatot, és kapcsolódjon a hibakeresőhöz a `Timeout for debug connection` üzenet után, és az időkorlát lejárta előtt.

## <a name="next-steps"></a>További lépések

* Tekintse meg az SDK-referenciát a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és a [azureml-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag súgójában.

* Tekintse meg a [Designer-kivételek és-hibakódok](algorithm-module-reference/designer-error-codes.md)listáját.
