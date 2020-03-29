---
title: A ParallelRunStep hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: Hibakeresés és a ParallelRunStep hibaelhárítása az Azure Machine Learning SDK python-hoz gépi tanulási folyamataiban. Ismerje meg a folyamatok fejlesztésének gyakori buktatóit, és tippeket, amelyek segítenek a parancsfájlok hibakeresésében a távoli végrehajtás előtt és alatt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122963"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>A ParallelRunStep hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hibakeresés és hibaelhárítás a [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) osztály az [Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

Tekintse meg a [tesztelési parancsfájlok helyileg szakasz](how-to-debug-pipelines.md#testing-scripts-locally) gépi tanulási folyamatok. A ParallelRunStep a rendszerhiba-folyamatok lépésként fut, így ugyanaz a válasz mindkét.

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresési funkciója távoli környezetből

A pontozási parancsfájl helyi hibakereséséről a pontozási parancsfájl tényleges folyamatában történő hibakeresésére való áttérés nehéz ugrás lehet. A naplók a portálon való megkeresésével kapcsolatos információkért a [gépi tanulási folyamatok szakasza a parancsfájlok távoli környezetből történő hibakereséséről.](how-to-debug-pipelines.md#debugging-scripts-from-remote-context) Az ebben a szakaszban szereplő információk egy párhuzamos lépésfuttatásra is vonatkoznak.

A naplófájl `70_driver_log.txt` például a párhuzamos futtatási lépéskódot elindító vezérlő adatait tartalmazza.

A párhuzamos futtatási feladatok elosztott jellege miatt több különböző forrásból származó naplók találhatók. Azonban két összevont fájl jön létre, amelyek magas szintű információkat nyújtanak:

- `~/logs/overview.txt`: Ez a fájl magas szintű információt nyújt az eddig létrehozott minikötegek (más néven feladatok) számáról és az eddig feldolgozott minikötegek számáról. Ezen a végén, azt mutatja, az eredmény a feladat. Ha a feladat nem sikerült, akkor megjelenik a hibaüzenet, és hol kell elindítani a hibaelhárítást.

- `~/logs/sys/master.txt`: Ez a fájl biztosítja a futó feladat fő csomópontját (más néven az orchestratornézetet). Tartalmazza a feladat létrehozását, a folyamatfigyelést, a futtatás eredményét.

Az EntryScript.logger és a print statements használatával a bejegyzési parancsfájlból létrehozott naplók a következő fájlokban találhatók:

- `~/logs/user/<ip_address>/Process-*.txt`: Ez a fájl az Entry_script-ből írt naplókat tartalmazza az EntryScript.logger használatával. Ez is tartalmaz print statement (stdout) entry_script.

Ha teljes körűen meg kell ismernie, hogy az egyes csomópontok hogyan hajtották végre a pontszámparancsfájlt, tekintse meg az egyes csomópontok egyes folyamatnaplóit. A folyamatnaplók a `sys/worker` feldolgozócsomópontok szerint csoportosított mappában találhatók:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Ez a fájl részletes információkat tartalmaz minden egyes mini-tételről, amikor a dolgozó felveszi vagy kitölti. Minden mini-tétel, ez a fájl tartalmazza:

    - A munkavégző folyamat IP-címe és PID-je. 
    - Az elemek teljes száma, a sikeresen feldolgozott cikkek száma és a sikertelen cikkek száma.
    - A kezdési idő, az időtartam, a feldolgozási idő és a futtatási módszer ideje.

Az egyes dolgozók folyamatainak erőforrás-felhasználásáról is találhat információkat. Ez az információ CSV formátumú, és a ban `~/logs/sys/perf/<ip_address>/`található. Egyetlen csomópont esetén a feladatfájlok a `~logs/sys/perf`területen érhetők el. Ha például az erőforrások kihasználtságát ellenőrzi, tekintse meg a következő fájlokat:

- `Process-*.csv`: Dolgozónkénti erőforrás-használat. 
- `sys.csv`: Csomópontonkénti napló.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hogyan tudok távoli környezetből naplózni a felhasználói parancsfájlból?
Az EntryScript-ből beszerezhet egy naplózót, ahogy az az alábbi mintakódban látható, hogy a **naplók/felhasználói** mappák megjelenjenek a portálon.

**Mintabejegyzés-parancsfájl a naplózó használatával:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hogyan adhatok át egy oldalbevitelt, például egy, egy keress táblát tartalmazó fájlt vagy fájlt vagy fájlt az összes dolgozómnak?

Hozzon [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) létre egy adatkészlet-objektumot, amely tartalmazza az oldalsó bemenetet, és regisztráljon a munkaterülettel. Ezt követően a következtetési parancsfájlban (például az init() metódusban) az alábbiak szerint érheti el:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>További lépések

* Az [Azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) csomaggal és a ParallelRunStep osztály [dokumentációjával](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) kapcsolatban az SDK-referencia című témakörben talál segítséget.

* Kövesse a párhuzamos futtatási lépésekkel rendelkező folyamatok használatának [speciális oktatóanyagát.](tutorial-pipeline-batch-scoring-classification.md)
