---
title: Naplózási mérőszámok a betanítási futtatások során
titleSuffix: Azure Machine Learning service
description: Nyomon követheti a kísérleteket, és figyelheti a mérőszámokat a modell létrehozási folyamatának növelése érdekében. Megtudhatja, hogyan adhat hozzá naplózást a betanítási parancsfájlhoz, hogyan küldheti el a kísérletet, hogyan tekintheti át a futó feladatok állapotát, és hogyan tekintheti meg a Futtatás naplózott eredményeit.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: edc0da77fc1c2813c2485fca18d50952e3060db8
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370473"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>A betanítási metrikák a Azure Machine Learningban való futtatásakor

A kísérletek és a monitorozási mérőszámok nyomon követésével növelheti a modell létrehozásának folyamatát. Ebből a cikkből megtudhatja, hogyan veheti fel a naplózást a betanítási parancsfájlba, hogyan küldhet el egy kísérletet, hogyan figyelheti a futtatást, és hogyan tekintheti meg a futtatások eredményeit Azure Machine Learning szolgáltatásban.

## <a name="list-of-training-metrics"></a>Képzési mérőszámok listája 

A következő metrikák hozzáadhat egy Futtatás betanítási kísérlet során. Mi a Futtatás követhető részletes listáját, tekintse meg a [osztály dokumentációja futtatása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Típus| Funkce Pythonu | Megjegyzések|
|----|:----|:----|
|Skaláris értékek |Függvény:<br>`run.log(name, value, description='')`<br><br>Példa:<br>Run.log ("pontossággal", 0,95) |Napló egy numerikus vagy karakterlánc értéket, a Futtatás a megadott névvel. Ennek a mutatónak tárolhatók a kísérletben a futtatási rekord a naplózási a metrika a futtató okoz.  Az azonos metrika futtató, az eredmény egy adott mérőszám vektor fontolóra belül több alkalommal bejelentkezhet.|
|Listák|Függvény:<br>`run.log_list(name, value, description='')`<br><br>Példa:<br>Run.log_list ("pontosságú", [a 0.6-os, 0,7, 0.87]) | Jelentkezzen a Futtatás a megadott nevű értékek listáját.|
|Sor|Függvény:<br>`run.log_row(name, description=None, **kwargs)`<br>Példa:<br>Run.log_row ("Keresztül X Y", x = 1, y = 0,4) | Használatával *log_row* hoz létre egy metrika több oszlopból álló kwargs leírtak szerint. Minden elnevezett paraméter generálja egy oszlop megadott értékkel.  *log_row* egyszer hívható egy hurkot, és a egy teljes táblát hozzon létre egy tetszőleges rekord, vagy több alkalommal bejelentkezni.|
|Tábla|Függvény:<br>`run.log_table(name, value, description='')`<br><br>Példa:<br>Run.log_table ("Keresztül X Y", {"x": [1, 2, 3], "y": [a 0.6-os, 0,7, 0.89]}) | Jelentkezzen a szótárobjektum a Futtatás a megadott névvel. |
|Képek|Függvény:<br>`run.log_image(name, path=None, plot=None)`<br><br>Példa:<br>`run.log_image("ROC", plt)` | Jelentkezzen egy képet a futtatási rekord. Be-vagy képfájl a matplotlib log_image használatával a Futtatás rajz.  Ezek a lemezképek lesz látható, és a futtatási rekord összehasonlítható.|
|Futtatás címkézése|Függvény:<br>`run.tag(key, value=None)`<br><br>Példa:<br>Run.tag ("kijelölt", "yes") | A Futtatás egy karakterláncot és egy opcionális karakterláncértéket a címkékkel.|
|Töltse fel a fájl vagy könyvtár|Függvény:<br>`run.upload_file(name, path_or_stream)`<br> <br> Példa:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Töltse fel egy fájlt a futtatási rekord. Futtatások automatikusan rögzítheti a megadott kimeneti könyvtár, amely alapértelmezés szerint a fájl ". / kimenete" legtöbb Futtatás típusok esetében.  Upload_file csak akkor használja további fájlokat szeretne feltölteni, vagy nincs megadva egy kimeneti könyvtárat. Javasoljuk, hogy hozzáadása `outputs` a nevéhez, így az informatikai részleg által feltöltött lekérdezi a kimeneti könyvtár. Listázhatja az összes kapcsolódó fájlt a futtatási rekord által nevű `run.get_file_names()`|

> [!NOTE]
> Metrikák fejlécekké, a listákat, a sorok és a táblák rendelkezhet típusa: lebegőpontos, egész szám vagy karakterlánc.

## <a name="start-logging-metrics"></a>Indítsa el a naplózás metrikák

Ha szeretné nyomon követni vagy figyelése is futtathatja a kísérletet, hozzá kell adnia a naplózása, amikor a Futtatás küld kódot. A futtatási küldésének aktiválásához módjai a következők:
* __Run.start_logging__ – a naplózási szolgáltatások hozzáadása a tanítási szkriptet, és a egy interaktív naplózási munkamenet elindításához a megadott kísérletben. **start_logging** notebookok – egyéb felhasználási helyzetek egy interaktív Futtatás használatra hoz létre. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.
* __ScriptRunConfig__ – a naplózási szolgáltatások hozzáadása a tanítási szkriptet, és a teljes parancsfájl mappát a Futtatás tölthető be.  **ScriptRunConfig** beállítása szkript konfigurációi fut egy osztály van. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

## <a name="set-up-the-workspace"></a>A munkaterület beállítása
Mielőtt hozzáadná a naplózás és a egy kísérlet elküldése, be kell állítania a munkaterületen.

1. Töltse be a munkaterület. Ha többet szeretne megtudni a munkaterület-konfiguráció beállításáról, kövesse az [Azure Machine learning szolgáltatás munkaterületének létrehozása](setup-create-workspace.md#sdk)című témakör lépéseit.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-startlogging"></a>1\. lehetőség: Start_logging használata

**start_logging** notebookok – egyéb felhasználási helyzetek egy interaktív Futtatás használatra hoz létre. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.

Az alábbi példában egy egyszerű sklearn Ridge modell helyileg a helyi Jupyter notebook betanítja. A különböző környezetekben kísérletek elküldése kapcsolatos további információkért lásd: [állítsa be a modell betanítása és az Azure Machine Learning szolgáltatás a számítási célokhoz](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Helyi Jupyter notebook létrehozása egy tanítási szkriptet. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Adjon hozzá kísérlet nyomon követése az Azure Machine Learning szolgáltatás SDK használatával, és a egy megőrzött modell feltöltése rekord futtassa a kísérletet. Az alábbi kód felveszi a címkéket, naplók, a, és feltölti egy modellfájl futtassa a kísérletet.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    A parancsfájl végződik ```run.complete()```, amely jelöli meg a futtatást, befejezettként.  Ezt a funkciót általában interaktív notebook olyan forgatókönyvekben használatos.

## <a name="option-2-use-scriptrunconfig"></a>2\. lehetőség: ScriptRunConfig használata

A [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) a parancsfájlok futtatásához szükséges konfigurációk beállításának osztálya. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

Ebben a példában a fent sklearn Ridge alapmodell tartalmazó gyűjteménnyel bővítjük. Egy egyszerű paraméter Ismétlés az ismétlés metrikákat rögzítheti a modell és a kísérlet alatt fut, a betanított modellek az alfa értékeknek keresztül hajtja végre. A példában helyben fut egy felhasználó által felügyelt környezetben. 

1. Hozzon létre egy `train.py`képzési parancsfájlt.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. A `train.py` szkriptek `mylib.py` hivatkozásai lehetővé teszik, hogy lekérje a Ridge-modellben használandó alfa-értékek listáját.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Állítsa be a felhasználó által felügyelt helyi környezetben.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Küldje el a ```train.py``` parancsfájl a felhasználó által felügyelt környezetben való futtatásához. Ez a mappa teljes szkript elküldésekor a betanításhoz, beleértve a ```mylib.py``` fájlt.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Futtatás kezelése

A [tanítási futtatások indítása, figyelése és megszakítása című](how-to-manage-runs.md) cikk a kísérletek kezelésével kapcsolatos konkrét Azure Machine learning munkafolyamatokat emeli ki.

## <a name="view-run-details"></a>Futtatás részletei nézet

### <a name="monitor-run-with-jupyter-notebook-widget"></a>A monitor futtatása a Jupyter notebook widgettel
Ha a **ScriptRunConfig** metódust használja a futtatások elküldéséhez, tekintse meg a Futtatás folyamatát egy [Jupyter widgettel](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

1. A Jupyter widget tekintse meg a Futtatás befejeződik várakozás közben.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Képernyőkép a Jupyter notebook widget](./media/how-to-track-experiments/run-details-widget.png)

A munkaterületen megjelenő megjelenítésre mutató hivatkozást is kaphat.

```python
print(run.get_portal_url())
```

2. **[Automatikus machine learning futtatások]**  Az előző menetből a diagramok eléréséhez. Cserélje `<<experiment_name>>` le a nevet a megfelelő kísérlet nevére:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![A Machine Learning automatikus Jupyter notebook widget](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


További részleteit a folyamat kattintson a folyamat a megtekintéséhez, amelyet szeretne Fedezze fel a táblázatban, és a diagramok az Azure-portálról egy előugró ablak jelenik meg.

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

Modell betanítása és figyelési fordulnak elő a háttérben, hogy a várakozás közben egyéb feladatokat is futtathat. Azt is, amíg a modell több kód futtatása előtt képzési befejeződéséig. Ha használ **ScriptRunConfig**, használható ```run.wait_for_completion(show_output = True)``` megjelenítése, ha a modell betanítása befejeződött. A ```show_output``` jelző biztosít részletes kimenet. 


### <a name="query-run-metrics"></a>A lekérdezés futtatása a metrikák

Megtekintheti a metrikákat egy betanított modell használatával ```run.get_metrics()```. Most már beszerezheti az összes naplózott határozza meg a legjobb modellt a fenti példában a metrikákat.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>A kísérlet megtekintéséhez az Azure Portalon

Amikor egy kísérlet befejezését követően, megnyithatja a futtatási rekord rögzített kísérletet. Az előzményeket kétféleképpen érheti el:

* Első futtatás URL-CÍMÉT közvetlenül ```print(run.get_portal_url())```
* A Futtatás neve elküldésével részleteinek megtekintése (ebben az esetben ```run```). Ezzel a módszerrel a kísérlet neve, azonosítója, típus, állapot, részleteit megjelenítő oldalon, egy hivatkozást az Azure Portalra és dokumentáció mutató hivatkozás mutat.

A hivatkozás a Futtatás méretezhetőséget kínál, közvetlenül a futtatási Részletek lap az Azure Portalon. Itt láthatja, bármely tulajdonságai, a nyomon követett metrikák, lemezképek és bejelentkezett a kísérlet diagram. Ebben az esetben azt naplózza, MSE és az alfa értékeknek.

  ![Futtatás részletei az Azure Portalon](./media/how-to-track-experiments/run-details-page.png)

Bármely kimenetek vagy a naplók a Futtatás megtekintéséhez, vagy töltse le a kísérlet, így a kísérlet mappa másokkal is megoszthat küldte el a pillanatkép is.

### <a name="viewing-charts-in-run-details"></a>Diagramok részleteinek megtekintése

Különféle módon használhatja a naplózási metrikák rekord különböző típusú API-kat a futtatás során, és megtekintheti őket, mint a diagramok az Azure Portalon. 

|A naplózott érték|Példakód| A portál megtekintése|
|----|----|----|
|Napló numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable vonaldiagram|
|Az azonos nevű metrika ismételten használt egyetlen numerikus érték naplózása (hasonló belül egy "for" ciklus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable vonaldiagram|
|Jelentkezzen egy sor 2 numerikus oszlopok ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonaldiagram|
|A numerikus oszlopok 2 naplótábláját|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonaldiagram|


## <a name="example-notebooks"></a>Példa notebookok
A következő notebookok a jelen cikk fogalmait bemutatása:
* [How-to-use-azureml/Training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/Training/train-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki a következő lépések megtudhatja, hogyan használható az Azure Machine Learning SDK Pythonhoz készült:

* Lásd a példa bemutatja, hogyan regisztrálja a legjobb modellt, és az oktatóanyagban üzembe [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md).

* Ismerje meg, hogyan [PyTorch modellek az Azure Machine Learning](how-to-train-pytorch.md).
