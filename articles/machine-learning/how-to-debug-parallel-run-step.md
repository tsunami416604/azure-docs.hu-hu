---
title: ParallelRunStep hibakeresése és hibaelhárítása
titleSuffix: Azure Machine Learning
description: ParallelRunStep hibakeresése és hibaelhárítása a gépi tanulási folyamatokban a Pythonhoz készült Azure Machine Learning SDK-ban. Ismerje meg a folyamatokkal kapcsolatos gyakori buktatókat, valamint a távoli végrehajtás előtt és közben a parancsfájlok hibakeresését segítő tippeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: a6a3e9a7a914711f6b7c923ac2249ebf3285c877
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031014"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep hibakeresése és hibaelhárítása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan végezhet hibakeresést és hibaelhárítást a [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) osztályból a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-ból.

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

Tekintse meg a gépi tanulási folyamatok [helyi tesztelése című szakaszt](how-to-debug-pipelines.md#testing-scripts-locally) . A ParallelRunStep a ML-folyamatok lépéseit futtatja, így ugyanazt a választ is alkalmazza.

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

Egy pontozási szkript helyi hibakeresése egy valós folyamaton belül egy pontozási parancsfájl hibakeresése egy nehéz ugrás lehet. A naplók a portálon való megtalálásával kapcsolatos információkért [lásd a Machine learning-folyamatok című szakaszt a parancsfájlok távoli környezetből való hibakereséséhez](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Az ebben a szakaszban található információk egy ParallelRunStep is érvényesek.

A naplófájl például a `70_driver_log.txt` vezérlőből származó adatokat tartalmaz, amely elindítja a ParallelRunStep kódot.

A ParallelRunStep-feladatok elosztott jellegéből adódóan számos különböző forrásból származó naplók találhatók. A rendszer azonban két konszolidált fájlt hoz létre, amelyek magas szintű információkat biztosítanak:

- `~/logs/overview.txt`: Ez a fájl magas szintű információt nyújt a mini-batchs (más néven feladatoknak) számáról, amely az eddig feldolgozott mini-batchek számát tartalmazza. Ezen a helyen a feladatok eredményét jeleníti meg. Ha a feladatainak végrehajtása sikertelen volt, a hibaüzenet jelenik meg, és a hibaelhárítás elindításának helye.

- `~/logs/sys/master.txt`: Ez a fájl a futó feladathoz tartozó fő csomópontot (más néven Orchestrator) jeleníti meg. Magában foglalja a feladatok létrehozását, a folyamat figyelését, a Futtatás eredményét.

A EntryScript Helper és Print utasítások használatával generált naplók a következő fájlokban találhatók:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Ezek a fájlok entry_script a EntryScript Helper használatával írt naplók. A entry_script a Print utasítást (StdOut) is tartalmazza.

A parancsfájlban található hibák tömör megismeréséhez a következőt kell tennie:

- `~/logs/user/error.txt`: Ez a fájl megpróbálja összefoglalni a parancsfájlban szereplő hibákat.

A parancsfájl hibáit a következő témakörben találhatja meg:

- `~/logs/user/error/`: Tartalmazza az összes dobott hibát, valamint a csomópont által rendezett teljes verem nyomkövetését.

Ha teljes mértékben meg kell ismernie, hogy az egyes csomópontok hogyan hajtották végre a pontszám-parancsfájlt, tekintse meg az egyes csomópontok egyes folyamatainak naplóit. A folyamat naplófájljai a `sys/node` mappában találhatók, munkavégző csomópontok szerint csoportosítva:

- `~/logs/sys/node/<node_name>.txt`: Ez a fájl részletes információkat nyújt az egyes mini-kötegekről, mivel azokat a feldolgozók felvettek vagy elvégezték. Minden egyes mini-batch esetében ez a fájl a következőket tartalmazza:

    - A munkavégző folyamat IP-címe és PID-je. 
    - Az elemek teljes száma, a sikeresen feldolgozott elemek száma és a sikertelen elemek száma.
    - A kezdési idő, az időtartam, a feldolgozási idő és a futtatási módszer ideje.

Az egyes feldolgozókhoz tartozó folyamatok erőforrás-használatáról is talál információt. Ez az információ CSV formátumú, és a következő helyen található: `~/logs/sys/perf/overview.csv` . Az egyes folyamatokra vonatkozó információk a alatt találhatók `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>A felhasználói szkriptből Hogyan a naplót egy távoli környezetből?
A ParallelRunStep process_count_per_node alapján több folyamatot is futtathat egy csomóponton. Az egyes folyamatok naplófájljainak a csomóponton való rendszerezéséhez és a Print és a log utasítás összevonásához javasoljuk, hogy az alább látható módon használja a ParallelRunStep naplózó használatát. Egy adatgyűjtő-EntryScript kap, és a naplók megjelennek a **naplók/felhasználói** mappában a portálon.

**Egy minta-bejegyzési parancsfájl a Logger használatával:**
```python
from azureml_user.parallel_run import EntryScript

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

A felhasználó a ParalleRunStep side_inputs paraméterével adhat át hivatkozási adattípust a parancsfájlnak. A side_inputsként megadott összes adatkészlet minden munkavégző csomóponthoz lesz csatlakoztatva. A felhasználó lekérheti a csatlakoztatás helyét a pass argumentum használatával.

Készítse el a hivatkozási adatokat tartalmazó [adatkészletet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) , és regisztrálja azt a munkaterületen. Adja át a `side_inputs` paraméterének `ParallelRunStep` . Emellett az elérési útját is hozzáadhatja a `arguments` szakaszhoz, hogy könnyen hozzáférhessen a csatlakoztatott elérési úthoz:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Ezután elérheti azt a következtetési parancsfájlban (például az init () metódusban) a következőképpen:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>További lépések

* A [azureml-pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) csomaggal kapcsolatos segítségért tekintse meg az SDK-referenciát. A ParallelRunStep osztály [dokumentációjának](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) megtekintése.

* Kövesse a [speciális oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md) a folyamatok ParallelRunStep használatával történő használatáról. Az oktatóanyag bemutatja, hogyan adhat át egy másik fájlt oldalsó bemenetként. 
