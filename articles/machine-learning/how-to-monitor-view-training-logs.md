---
title: ML futtatási naplók figyelése és megtekintése & metrikák
titleSuffix: Azure Machine Learning
description: Figyelje az Azure ML-kísérleteit, és tekintse meg a futtatási metrikákat a modell létrehozási folyamatának növelése érdekében. A widgetek és a Studio-portál használatával vizsgálja meg a futtatási állapotot és a futtatási rekordok megtekintését.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: aa4b55cb0700a47d9235a1d526ef1b1678d6db8b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333817"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>ML futtatási naplók és metrikák figyelése és megtekintése



Ebből a cikkből megtudhatja, hogyan figyelheti Azure Machine Learning futtatásait, és hogyan tekintheti meg a naplókat. Mielőtt megtekinti a naplókat, először engedélyeznie kell azokat. További információ: [a naplózás engedélyezése az Azure ml-képzésekben](how-to-track-experiments.md).

A naplók segítségével diagnosztizálhatja a hibákat és a figyelmeztetéseket, vagy nyomon követheti a teljesítmény mérőszámait, például a paramétereket és a modell pontosságát. Ebből a cikkből megtudhatja, hogyan tekintheti meg a naplókat a következő módszerek használatával:

> [!div class="checklist"]
> * A monitor futtatása a Studióban
> * A monitor futtatása a Jupyter Notebook Widget használatával
> * Gépi tanulás automatizált futtatásának figyelése
> * Kimeneti naplók megtekintése a befejezés után
> * Kimeneti naplók megtekintése a Studióban

A kísérletek kezelésével kapcsolatos általános információkért lásd: a [betanítási futtatások elindítása, figyelése és megszakítása](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>A monitor futtatása a Studióban

Ha figyelni szeretné a futtatásokat egy adott számítási célra a böngészőben, kövesse az alábbi lépéseket:

1. A [Azure Machine learning Studióban](https://ml.azure.com/)válassza ki a munkaterületet, majd a lap bal oldalán kattintson a __számítás__ elemre.

1. Válassza a __betanítási fürtök__ lehetőséget a betanításhoz használt számítási célok listájának megjelenítéséhez. Ezután válassza ki a fürtöt.

    ![Válassza ki a betanítási fürtöt](./media/how-to-track-experiments/select-training-compute.png)

1. Válassza a __futtatások__lehetőséget. Megjelenik a fürtöt használó futtatások listája. Egy adott Futtatás részleteinek megtekintéséhez használja a __Run (Futtatás__ ) oszlopban található hivatkozást. A kísérlet részleteinek megtekintéséhez használja a __kísérlet__ oszlopban található hivatkozást.

    ![A betanítási fürt futtatásának kiválasztása](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Mivel a kiszámított számítási célok egy megosztott erőforrás, több futtatási várólistára vagy aktívra lehet szükség egy adott időpontban.
    > 
    > A futtatások tartalmazhatnak alárendelt futtatásokat, így egy betanítási feladatok több bejegyzést is eredményezhetnek.

A Futtatás befejezése után már nem jelenik meg ezen a lapon. A befejezett futtatásokkal kapcsolatos információk megtekintéséhez látogasson el a Studio __kísérletek__ szakaszára, és válassza ki a kísérletet, majd futtassa a parancsot. További információért lásd a [befejezett futtatások metrikáinak megtekintése](#view-the-experiment-in-the-web-portal)című szakaszt.

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>A monitor futtatása a Jupyter notebook Widget használatával

Ha a **ScriptRunConfig** metódust használja a futtatások elküldéséhez, tekintse meg a Futtatás előrehaladását a [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)használatával. A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

Tekintse meg a Jupyter widgetet a Futtatás befejeződésére való várakozás közben.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Képernyőkép a Jupyter notebook widgetről](./media/how-to-track-experiments/run-details-widget.png)

A munkaterületen megjelenő megjelenítésre mutató hivatkozást is kaphat.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Gépi tanulás automatizált futtatásának figyelése

Az automatizált gépi tanulás futtatásához az előző futtatásból származó diagramok eléréséhez cserélje le a parancsot `<<experiment_name>>` a megfelelő kísérlet nevére:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Automatikus Machine Learning Jupyter notebook widget](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Kimenet megjelenítése befejezés után

A **ScriptRunConfig**használatakor a ```run.wait_for_completion(show_output = True)``` segítségével megjelenítheti a modell betanításának befejeződését. A ```show_output``` jelző részletes kimenetet biztosít. További információt a [naplózás engedélyezésének](how-to-track-experiments.md#scriptrun-logs)ScriptRunConfig című szakaszában talál.

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Lekérdezés-futtatási metrikák

A betanított modell metrikáit a használatával tekintheti meg ```run.get_metrics()``` . Például a fenti példával meghatározhatja a legjobb modellt úgy, hogy a legalacsonyabb Mean Square error (MSE) értékkel keresi a modellt.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Futtatási rekordok megtekintése a Studióban

A [Azure Machine learning Studióban](https://ml.azure.com)böngészhet a befejezett futtatási rekordok, beleértve a naplózott metrikákat is.

Navigáljon a **kísérletek** lapra, és válassza ki a kísérletet. A kísérlet futtatása irányítópulton láthatja az egyes futtatások nyomon követett mérőszámait és naplóit. 

Egy adott Futtatás részletezésével megtekintheti a kimeneteit vagy naplóit, vagy letöltheti a kísérlet pillanatképét, így megoszthatja a kísérlet mappáját másokkal.

A futtatási lista tábla szerkesztésével több futtatást is kijelölhet, és megjelenítheti a futtatások utolsó, minimális vagy maximális naplózott értékét. A diagramok testreszabásával összehasonlíthatja a naplózott mérőszámok értékeit és összesítéseit több Futtatás között.

![Részletek futtatása a Azure Machine Learning Studióban](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Diagramok formázása a Studióban

A naplózási API-k a következő módszerekkel befolyásolhatják a mérőszámok megjelenítését.

|Naplózott érték|Mintakód| Formátum a portálon|
|----|----|----|
|Numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Egyváltozós vonalas diagram|
|Egy numerikus értéket többször is felhasználhat ugyanazzal a metrikai névvel (például a cikluson belül)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Egyváltozós vonalas diagram|
|Sor naplózása 2 numerikus oszloppal ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonalas diagram|
|Táblázat két numerikus oszloppal|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonalas diagram|


## <a name="next-steps"></a>Következő lépések

Próbálja ki a következő lépéseket a Azure Machine Learning használatának megismeréséhez:

* Ismerje meg, hogyan [követheti nyomon a kísérleteket, és hogyan engedélyezheti a naplókat a Azure Machine learning Designerben](how-to-track-designer-experiments.md).

* [A képosztályozási modellek Azure Machine Learninggel való betanításával](tutorial-train-models-with-aml.md) kapcsolatos témakörben talál példát arra, hogyan regisztrálhatja a legjobb modellt, és hogyan helyezheti üzembe.

