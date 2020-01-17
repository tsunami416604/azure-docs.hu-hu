---
title: ParallelRunStep hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: ParallelRunStep hibakeresése és hibaelhárítása a gépi tanulási folyamatokban a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatokkal kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122963"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) osztályból a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-ból.

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

Tekintse meg a gépi tanulási folyamatok [helyi tesztelése című szakaszt](how-to-debug-pipelines.md#testing-scripts-locally) . A ParallelRunStep a ML-folyamatok lépéseit futtatja, így ugyanazt a választ is alkalmazza.

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

Egy pontozási szkript helyi hibakeresése egy valós folyamaton belül egy pontozási parancsfájl hibakeresése egy nehéz ugrás lehet. A naplók a portálon való megtalálásával kapcsolatos információkért [lásd a Machine learning-folyamatok című szakaszt a parancsfájlok távoli környezetből való hibakereséséhez](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Az ebben a szakaszban található információk egy párhuzamos lépés futtatására is érvényesek.

A naplófájl `70_driver_log.txt` például olyan információt tartalmaz a vezérlőről, amely párhuzamos futtatási lépés kódját indítja el.

A párhuzamos futtatási feladatok elosztott jellegéből adódóan különböző forrásokból származó naplók találhatók. A rendszer azonban két konszolidált fájlt hoz létre, amelyek magas szintű információkat biztosítanak:

- `~/logs/overview.txt`: Ez a fájl magas szintű információt nyújt az eddig feldolgozott mini-batchs (más néven feladatoknak) számáról, valamint az eddig feldolgozott mini-batchek számáról. Ezen a helyen a feladatok eredményét jeleníti meg. Ha a feladatainak végrehajtása sikertelen volt, a hibaüzenet jelenik meg, és a hibaelhárítás elindításának helye.

- `~/logs/sys/master.txt`: Ez a fájl a futó feladathoz tartozó főcsomópontot (más néven Orchestrator) jeleníti meg. Magában foglalja a feladatok létrehozását, a folyamat figyelését, a Futtatás eredményét.

A EntryScript. Logger és Print utasítások használatával generált naplók a következő fájlokban találhatók:

- `~/logs/user/<ip_address>/Process-*.txt`: Ez a fájl entry_scriptokból írt naplókat tartalmaz a EntryScript. Logger használatával. Emellett a Print utasítást (StdOut) is tartalmazza entry_scriptból.

Ha teljes mértékben meg kell ismernie, hogy az egyes csomópontok hogyan hajtották végre a pontszám-parancsfájlt, tekintse meg az egyes csomópontok egyes folyamatainak naplóit. A folyamat naplófájljai a `sys/worker` mappában találhatók, munkavégző csomópontok szerint csoportosítva:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Ez a fájl részletes információkat nyújt az egyes mini-kötegekről, mivel azok a feldolgozók által betöltött vagy elvégezhető. Minden egyes mini-batch esetében ez a fájl a következőket tartalmazza:

    - A munkavégző folyamat IP-címe és PID-je. 
    - Az elemek teljes száma, a sikeresen feldolgozott elemek száma és a sikertelen elemek száma.
    - A kezdési idő, az időtartam, a feldolgozási idő és a futtatási módszer ideje.

Az egyes feldolgozókhoz tartozó folyamatok erőforrás-használatáról is talál információt. Ez az információ CSV formátumú, és `~/logs/sys/perf/<ip_address>/`található. Egyetlen csomópont esetében a feladatok fájljai a `~logs/sys/perf`alatt lesznek elérhetők. Ha például az erőforrás-használatot ellenőrzi, tekintse meg a következő fájlokat:

- `Process-*.csv`: munkavégző folyamatok erőforrás-használata. 
- `sys.csv`: csomóponti napló.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>A felhasználói szkriptből Hogyan a naplót egy távoli környezetből?
A EntryScript az alábbi mintakód alapján is beolvashatja, hogy a naplók megjelenjenek a **naplók/felhasználói** mappában a portálon.

**Egy minta-bejegyzési parancsfájl a Logger használatával:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hogyan adhatok át egy olyan oldali bemenetet, mint például egy keresési táblázatot tartalmazó fájl vagy fájl (ok) az összes feldolgozónak?

Állítson össze egy [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) -objektumot, amely tartalmazza az oldal bemenetét, és regisztrálja magát a munkaterületen. Ezután elérheti azt a következtetési parancsfájlban (például az init () metódusban) a következőképpen:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az SDK-referenciát, amely segítséget nyújt a [azureml-felépítések](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) és a ParallelRunStep osztály [dokumentációjában](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) .

* Kövesse a [speciális oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md) a párhuzamos futtatási lépésekkel rendelkező folyamatok használatáról.
