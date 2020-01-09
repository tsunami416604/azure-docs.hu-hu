---
title: ParallelRunStep hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: A gépi tanulási folyamatok hibakeresése és hibaelhárítása a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatokkal kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540606"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Hibakeresés és hibaelhárítás a ParallelRun használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) osztályból a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-ból.

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

Tekintse meg a gépi tanulási folyamatok [helyi tesztelése című szakaszt](how-to-debug-pipelines.md#testing-scripts-locally) . A ParallelRunStep a ML-folyamatok lépéseit futtatja, így ugyanazt a választ is alkalmazza.

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

Egy pontozási szkript helyi hibakeresése egy valós folyamaton belül egy pontozási parancsfájl hibakeresése egy nehéz ugrás lehet. A naplók a portálon való megtalálásával kapcsolatos információkért [lásd a Machine learning-folyamatok című szakaszt a parancsfájlok távoli környezetből való hibakereséséhez](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Az ebben a szakaszban található információk a Batch-következtetések futtatására is érvényesek.

Például a naplófájl `70_driver_log.txt` a következőket is tartalmazza: 

* A parancsfájl végrehajtása során kinyomtatott utasítások.
* A parancsfájl verem-nyomkövetése.

A Batch-következtetési feladatok elosztott jellegéből adódóan számos különböző forrásból származó naplók találhatók. A rendszer azonban két konszolidált fájlt hoz létre, amelyek magas szintű információkat biztosítanak:

- `~/logs/overview.txt`: Ez a fájl magas szintű információt nyújt az eddig feldolgozott mini-batchs (más néven feladatoknak) számáról, valamint az eddig feldolgozott mini-batchek számáról. Ezen a helyen a feladatok eredményét jeleníti meg. Ha a feladatainak végrehajtása sikertelen volt, a hibaüzenet jelenik meg, és a hibaelhárítás elindításának helye.

- `~/logs/master.txt`: Ez a fájl a futó feladathoz tartozó főcsomópontot (más néven Orchestrator) jeleníti meg. Magában foglalja a feladatok létrehozását, a folyamat figyelését, a Futtatás eredményét.

Ha teljes mértékben meg kell ismernie, hogy az egyes csomópontok hogyan hajtották végre a pontszám-parancsfájlt, tekintse meg az egyes csomópontok egyes folyamatainak naplóit. A folyamat naplófájljai a `worker` mappában találhatók, munkavégző csomópontok szerint csoportosítva:

- `~/logs/worker/<ip_address>/Process-*.txt`: Ez a fájl részletes információkat nyújt az egyes mini-kötegekről, mivel azok a feldolgozók által betöltött vagy elvégezhető. Minden egyes mini-batch esetében ez a fájl a következőket tartalmazza:

    - A munkavégző folyamat IP-címe és PID-je. 
    - Az elemek teljes száma és a sikeresen feldolgozott elemek száma. 
    - A kezdési és befejezési idő (`start1` és `end1`). 
    - A processzoridő (`start2` és `end2`) kezdési és befejezési időpontja. 

Az egyes feldolgozókhoz tartozó folyamatok erőforrás-használatáról is talál információt. Ez az információ CSV formátumú, és `~/logs/performance/<ip_address>/`található. Ha például az erőforrás-használatot ellenőrzi, tekintse meg a következő fájlokat:

- `process_resource_monitor_<ip>_<pid>.csv`: munkavégző folyamatok erőforrás-használata. 
- `sys_resource_monitor_<ip>.csv`: csomóponti napló.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>A felhasználói szkriptből Hogyan a naplót egy távoli környezetből?
Az alábbi lépésekkel beállíthatja, hogy a naplók megjelenjenek a **naplók/felhasználók** mappában a portálon:
1. Mentse az alábbi első kódrészletet a következő fájlba: entry_script_helper., és helyezze a fájlt a belépési parancsfájllal megegyező mappába. Ez az osztály beolvassa az elérési utat a AmlCompute belül. Helyi teszt esetén `get_working_dir()` módosíthatja a helyi mappák visszaküldését.
2. Egy naplózó konfigurációt a `init()` metódusban, majd használja. Az alábbi második kódrészlet egy példa. 

**entry_script_helper.:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Egy minta-bejegyzési parancsfájl a Logger használatával:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az SDK-referenciát, amely segítséget nyújt a [azureml-felépítések](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) és a ParallelRunStep osztály [dokumentációjában](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) .

* Kövesse a [speciális oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md) a Batch-pontozási folyamatok használatáról.
