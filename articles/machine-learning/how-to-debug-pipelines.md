---
title: Hibakeresés és hibaelhárítás a gépi tanulási folyamatokban
titleSuffix: Azure Machine Learning
description: Hibakeresés és gépi tanulási folyamatok hibaelhárítása az Azure Machine Learning SDK pythonhoz. Ismerje meg a folyamatok fejlesztésének gyakori buktatóit, és tippeket, amelyek segítenek a parancsfájlok hibakeresésében a távoli végrehajtás előtt és alatt. Ismerje meg, hogyan használhatja a Visual Studio-kódot a gépi tanulási folyamatok interaktív hibakereséséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064077"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Hibakeresés és hibaelhárítás a gépi tanulási folyamatokban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hibakeresés és hibaelhárítás [gépi tanulási folyamatok](concept-ml-pipelines.md) az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) és az [Azure Machine Learning designer (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Tájékoztatás akövetkezőkről:

* Hibakeresés az Azure Machine Learning SDK használatával
* Hibakeresés az Azure Machine Learning Designer használatával
* Hibakeresés az Application Insights használatával
* A Visual Studio Code (VS Code) és a Python Tools for Visual Studio (PTVSD) segítségével interaktív hibakeresés

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Hibakeresés és hibaelhárítás az Azure Machine Learning SDK-ban
A következő szakaszok áttekintést nyújtanak a folyamatok létrehozásakor gyakori buktatókat, és a folyamaton futó kód hibakeresésére szolgáló különböző stratégiákat. Az alábbi tippek használata, ha problémái vannak a várt módon futó folyamat beszerzésével.

### <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

A folyamat egyik leggyakoribb hibája, hogy egy csatolt parancsfájl (adattisztító parancsfájl, pontozási parancsfájl stb.) nem a megfelelően fut, vagy olyan futásidejű hibákat tartalmaz a távoli számítási környezetben, amelyeket nehéz hibahibahibakeresésa a munkaterületen az Azure Machine-ben Tanuló stúdió. 

Maguk a folyamatok nem futtathatók helyileg, de a parancsfájlok futtatása a helyi gépen lehetővé teszi a gyorsabb hibakeresést, mert nem kell megvárnia a számítási és környezeti létrehozási folyamatot. Ehhez némi fejlesztési munkára van szükség:

* Ha az adatok egy felhőbeli adattárban vannak, le kell töltenie az adatokat, és elérhetővé kell tennie azadatokat a parancsfájl számára. Az adatok egy kis mintájának használata jó módszer a futásidejű leépítésre és a parancsfájlok viselkedésére vonatkozó visszajelzés gyors lehívására
* Ha köztes folyamatlépést próbál szimulálni, előfordulhat, hogy manuálisan kell megépítenie azokat az objektumtípusokat, amelyeket az adott parancsfájl az előző lépéstől vár.
* Saját környezetet is meg kell határoznia, és replikálnia kell a távoli számítási környezetben definiált függőségeket.

Ha már futtatható a parancsfájl beállítása a helyi környezetben, sokkal egyszerűbb a hibakeresési feladatok, mint például:

* Egyéni hibakeresési konfiguráció csatolása
* A végrehajtás felfüggesztése és az objektumállapot vizsgálata
* Befogási típus vagy logikai hibák, amelyek futásidejűg nem lesznek elérhetők

> [!TIP] 
> Miután meggyőződhet arról, hogy a parancsfájl a várt módon fut, egy jó következő lépés a parancsfájl futtatása egy egylépéses folyamat, mielőtt több lépésben próbálna futtatni egy folyamatban.

### <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresési funkciója távoli környezetből

A parancsfájlok helyi tesztelése nagyszerű módja a főbb kódtöredékek és az összetett logika hibakeresésének a folyamat létrehozása előtt, de egy bizonyos ponton valószínűleg hibakeresést kell futtatnia a parancsfájlok között a tényleges folyamat futtatása során, különösen akkor, ha a viselkedés diagnosztizálása a csővezeték lépései közötti interakció során. Javasoljuk, `print()` hogy a lépésparancsfájlokban liberális annektált annektált módon használhassa a parancsfájlokat, hogy a távoli végrehajtás során láthassa az objektum állapotát és a várt értékeket, hasonlóan ahhoz, ahogyan a JavaScript-kódot debugolja.

A naplófájl a következőket `70_driver_log.txt` tartalmazza: 

* A parancsfájl végrehajtása során minden nyomtatott utasítás
* A parancsfájl veremnyomkövetése 

Ha meg szeretné találni ezt és más naplófájlokat a portálon, először kattintson a folyamat fut a munkaterületen.

![Folyamatfuttatási lista lapja](./media/how-to-debug-pipelines/pipelinerun-01.png)

Keresse meg a folyamat futtatási részleteit tartalmazó lapot.

![Folyamatfuttatási részletes lap](./media/how-to-debug-pipelines/pipelinerun-02.png)

Kattintson az adott lépéshez tartozó modulra. Nyissa meg a **Naplók** lapot. Más naplók közé tartozik a környezeti rendszerkép-összeállítási folyamat és a lépéselőkészítési parancsfájlok információkat.

![Folyamatfolyamat futtatásának részleteilap naplója](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> A *közzétett folyamatok futtatása* a munkaterület **Végpontok** lapján található. A *nem közzétett folyamatok futtatása* a **Kísérletek** vagy **a Folyamatok területen**található.

### <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat a csővezeték-fejlesztés során felmerülő gyakori problémákat tartalmazza a lehetséges megoldásokkal.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem lehet adatokat átadni a könyvtárnak `PipelineData` | Győződjön meg arról, hogy létrehozott egy könyvtárat a parancsfájlban, amely megfelel annak, ahol a folyamat a lépéskimeneti adatokat várja. A legtöbb esetben egy bemeneti argumentum határozza meg a kimeneti könyvtárat, majd explicit módon hozza létre a könyvtárat. A `os.makedirs(args.output_dir, exist_ok=True)` kimeneti könyvtár létrehozásához használható. Tekintse meg az [oktatóanyag](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) egy pontozási parancsfájl példa, amely bemutatja ezt a tervezési mintát. |
| Függőségi hibák | Ha helyileg fejlesztett ki és tesztelt parancsfájlokat, de függőségi problémákat talált, amikor egy távoli számítási folyamaton fut, győződjön meg arról, hogy a számítási környezet függőségei és verziói megfelelnek a tesztkörnyezetnek. [(Lásd: Környezetépítés, gyorsítótárazás és újrafelhasználás](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Kétértelmű hibák számítási célokkal | A számítási célok törlése és újbóli létrehozása megoldhatja a számítási célokkal kapcsolatos bizonyos problémákat. |
| A folyamat nem használja fel újra a lépéseket | A lépésújrahasználat alapértelmezés szerint engedélyezve van, de győződjön meg róla, hogy nem tiltotta le egy folyamatlépésben. Ha az újrafelhasználás `allow_reuse` le van tiltva, `False`a lépésben lévő paraméter a . |
| A folyamat feleslegesen fut újra | Annak érdekében, hogy a lépések csak akkor fussanak újra, amikor az alapul szolgáló adatok vagy parancsfájlok megváltoznak, válassza le a könyvtárakat az egyes lépésekhez. Ha ugyanazt a forráskönyvtárat több lépéshez használja, szükségtelen ismétléseket tapasztalhat. Használja `source_directory` a paramétert egy folyamat lépésobjektum ban, hogy pont az elkülönített könyvtárat, hogy a lépés, és győződjön meg arról, hogy nem használja ugyanazt `source_directory` az elérési utat több lépésben. |

### <a name="logging-options-and-behavior"></a>Naplózási beállítások és viselkedés

Az alábbi táblázat a folyamatok különböző hibakeresési lehetőségeiről tartalmaz tájékoztatást. Ez nem egy teljes lista, mivel más lehetőségek is léteznek, csak az Azure Machine Learning, Python és OpenCensus is itt látható.

| Erőforrástár                    | Típus   | Példa                                                          | Cél                                  | Források                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Az Azure Machine Learning Portal felhasználói felülete             | [A kísérletek nyomon követése](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python nyomtatás/naplózás    | Napló    | `print(val)`<br>`logging.info(message)`                          | Illesztőprogram-naplók, Azure Machine Learning Designer | [A kísérletek nyomon követése](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python naplózás](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Napló    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - nyomkövetések                | [Folyamatok hibakeresése az Application Insightsban](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportálók](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python naplózási szakácskönyv](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Példa naplózási beállításokra

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Hibakeresés és hibaelhárítás az Azure Machine Learning tervezőjében (előzetes verzió)

Ez a szakasz áttekintést nyújt a tervezőben lévő folyamatok hibaelhárításáról.
A tervezőben létrehozott folyamatok esetében a **naplófájlokat** a szerzői oldalon vagy a folyamat futtatási részleteit tartalmazó lapon találja.

### <a name="access-logs-from-the-authoring-page"></a>Naplók elérése a szerzői oldalról

Amikor elküldi a folyamat futtatását, és a szerzői oldalon marad, megtalálhatja az egyes modulokhoz létrehozott naplófájlokat.

1. Jelöljön ki bármelyik modult a szerzői vásznon.
1. A modul jobb oldali ablaktáblájában lépjen a **Kimenet+ogs** lapra.
1. A naplófájl kijelölése`70_driver_log.txt`

    ![Naplózási lap modulnaplói](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>A folyamatfuttatásokból származó naplók elérése

Az adott futtatások naplófájljait a folyamatok futtatási részletei lapon, a **Folyamatok** vagy a **Kísérletek** szakaszban is megtalálhatja.

1. Válassza ki a tervezőben létrehozott folyamatfuttatást.
    ![A folyamat futtatása lap](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Jelöljön ki bármelyik modult a betekintő ablaktáblán.
1. A modul jobb oldali ablaktáblájában lépjen a **Kimenet+ogs** lapra.
1. A naplófájl kijelölése`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Hibakeresés és hibaelhárítás az Application Insightsban
Az OpenCensus Python-kódtár ily módon történő használatáról az útmutatóban talál további információt: [A gépi tanulási folyamatok hibakeresése és hibaelhárítása az Application Insightsban](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Hibakeresés és hibaelhárítás a Visual Studio-kódban

Bizonyos esetekben előfordulhat, hogy interaktívan hibakeresést kell használnia az ML-folyamatban használt Python-kóddal. A Visual Studio Code (VS Code) és a Python Tools for Visual Studio (PTVSD) használatával csatolhatja a kódot, miközben fut a betanítási környezetben.

### <a name="prerequisites"></a>Előfeltételek

* Az __Azure Machine Learning-munkaterület,__ amely __egy Azure virtuális hálózat__használatára van konfigurálva.
* Egy __Azure Machine Learning-folyamat,__ amely a folyamat lépések részeként python-parancsfájlokat használ. Például egy PythonScriptStep.
* Egy Azure Machine Learning Compute-fürt, amely __a virtuális hálózatban__ van, és __a folyamat a betanításhoz használja.__
* A __virtuális hálózatban__található __fejlesztői környezet.__ A fejlesztői környezet a következők egyike lehet:

    * Egy Azure virtuális gép a virtuális hálózatban
    * A virtuális hálózat ban lévő notebook virtuális gép számítási példánya
    * Virtuális magánhálózat (VPN) által a virtuális hálózathoz csatlakoztatott ügyfélgép.

Az Azure virtuális hálózat Azure Machine Learning del való használatáról további információt a [Biztonságos Azure ML-kísérletezés és az Azure virtuális hálózaton belüli feladatok lekötése című](how-to-enable-virtual-network.md)témakörben talál.

### <a name="how-it-works"></a>Működés

Az ML-folyamat lépései Python-parancsfájlokat futtatnak. Ezek a parancsfájlok a következő műveletek végrehajtására módosulnak:
    
1. Naplózza annak az állomásnak az IP-címét, amelyen futnak. Az IP-cím segítségével csatlakoztathatja a hibakeresőt a parancsfájlhoz.

2. Indítsa el a PTVSD hibakeresési összetevőt, és várja meg, amíg a hibakereső csatlakozik.

3. A fejlesztői környezetből figyelheti a betanítási folyamat által létrehozott naplókat, hogy megtalálja azt az IP-címet, ahol a parancsfájl fut.

4. A VS Code-nak azt az IP-címet kell `launch.json` megadnia, amelyhez a hibakeresőt egy fájl segítségével csatlakoztathatja.

5. Csatolja a hibakeresőt, és interaktívan lépked át a parancsfájlon.

### <a name="configure-python-scripts"></a>Python-parancsfájlok konfigurálása

A hibakeresés engedélyezéséhez hajtsa végre a következő módosításokat a következő python-parancsfájlokon, amelyeket a hibakód-folyamat lépései használnak:

1. Adja hozzá a következő importálási nyilatkozatokat:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Adja hozzá az alábbi argumentumokat. Ezek az argumentumok lehetővé teszik a hibakereső szükség szerint történő engedélyezését, és beállíthatja a hibakereső csatolásának időidejét:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Adja hozzá a következő állításokat. Ezek az utasítások betöltik az aktuális futtatási környezetet, így naplózhatja annak a csomópontnak az IP-címét, amelyen a kód fut:

    ```python
    global run
    run = Run.get_context()
    ```

1. Adjon `if` hozzá egy utasítást, amely elindítja a PTVSD-t, és megvárja, amíg a hibakereső csatolja. Ha nincs hibakereső csatolja az időbeli meghosszabbítás előtt, a parancsfájl a szokásos módon folytatódik.

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

A következő Python-példa `train.py` egy alapfájlt mutat be, amely lehetővé teszi a hibakeresést:

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

### <a name="configure-ml-pipeline"></a>Ml-folyamat konfigurálása

A PTVSD indításához és a futtatási környezet lebiztosításához szükséges `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`Python-csomagok biztosításához hozzon létre egy környezetet, és állítsa be a . Módosítsa az SDK-verziót úgy, hogy az megfeleljen a használt verziónak. A következő kódrészlet bemutatja, hogyan hozhat létre környezetet:

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

A [Python-parancsfájlok konfigurálása](#configure-python-scripts) szakaszban két új argumentumot adtak hozzá a forgatókönyv-folyamat lépései által használt parancsfájlok. A következő kódrészlet bemutatja, hogyan használhatja ezeket az argumentumokat az összetevő hibakeresésének engedélyezéséhez, és hogyan állíthat be időkimenőt. Azt is bemutatja, hogyan kell használni `runconfig=run_config`a korábban létrehozott környezetet a következők beállításával:

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

Amikor a folyamat fut, minden lépés létrehoz egy gyermek futtatása. Ha a hibakeresés engedélyezve van, a módosított parancsfájl a `70_driver_log.txt` következő szöveghez hasonló információkat naplóz a gyermek futtatásához:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Mentse `ip_address` az értéket. Ezt használják a következő szakaszban.

> [!TIP]
> Az IP-címet is megtalálhatja a folyamatlépéshez futtatott gyermek futtatási naplóiból. Az információk megtekintéséről további információt az [Azure ML-kísérletek futtatásának és metrikáinak figyelése című témakörben talál.](how-to-track-experiments.md)

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A Python Tools for Visual Studio (PTVSD) telepítéséhez a VS Code fejlesztői környezetben a következő paranccsal:

    ```
    python -m pip install --upgrade ptvsd
    ```

    A PTVSD VS-kóddal való használatáról a [Távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)című témakörben talál további információt.

1. Ha a VS Code-ot a hibakeresőt futtató Azure Machine Learning-számítással való kommunikációra szeretné konfigurálni, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS-kód lapon válassza a __Debug menüt,__ majd a __Konfigurációk megnyitása parancsot.__ Megnyílik __egy launch.json__ nevű fájl.

    1. A __launch.json__ fájlban keresse meg `"configurations": [`a tartalmazó sort, és utána szúrja be a következő szöveget. Módosítsa `"host": "10.3.0.5"` a bejegyzést az előző szakasznaplóiban visszaadott IP-címre. Módosítsa `"localRoot": "${workspaceFolder}/code/step"` a bejegyzést egy helyi könyvtárra, amely a hibakeresés alatt álló parancsfájl másolatát tartalmazza:

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
        > Ha a konfigurációk szakaszban már vannak más bejegyzések, a beszúrt kód után adjon hozzá vesszőt (,) .

        > [!TIP]
        > Az ajánlott eljárás az, hogy a parancsfájlok erőforrásait külön `localRoot` könyvtárakban `/code/step1`tartja, ezért a példa érték-referenciaértékei .
        >
        > Ha több parancsfájlt keres, különböző könyvtárakban hozzon létre külön konfigurációs szakaszt minden parancsfájlhoz.

    1. Mentse a __launch.json__ fájlt.

### <a name="connect-the-debugger"></a>A hibakereső csatlakoztatása

1. Nyissa meg a VS-kódot, és nyissa meg a parancsfájl helyi példányát.
2. Állítsa be a töréspontokat ott, ahol le szeretné állítani a parancsfájlt, miután csatolta.
3. Amíg a gyermekfolyamat futtatja `Timeout for debug connection` a parancsfájlt, és az megjelenik a naplókban, használja az F5 gombot, vagy válassza a __Debug__lehetőséget. Amikor a rendszer kéri, válassza ki az __Azure Machine Learning Compute: távoli hibakeresési__ konfiguráció. Az oldalsávon is kiválaszthatja a hibakeresési ikont, az __Azure Machine Learning: távoli hibakeresési__ bejegyzést a Hibakeresési legördülő menüből, majd a zöld nyíllal csatolhatja a hibakeresőt.

    Ezen a ponton a VS-kód csatlakozik a PTVSD-hez a számítási csomóponton, és megáll a korábban beállított töréspontnál. Most már lépésben a kódot, mert fut, változók megtekintése, stb.

    > [!NOTE]
    > Ha a naplóban `Debugger attached = False`megjelenik egy bejegyzés, amely tartalmazza a , majd az időtúllépése lejárt, és a parancsfájl a hibakereső nélkül folytatódott. Küldje el újra a folyamatot, `Timeout for debug connection` és csatlakoztassa a hibakeresőt az üzenet után, még az időadat lejárta előtt.

## <a name="next-steps"></a>További lépések

* Tekintse meg az SDK-referencia segítséget az [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) csomag és az [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) csomag.

* Tekintse meg a [tervezői kivételek és hibakódok](algorithm-module-reference/designer-error-codes.md)listáját.
