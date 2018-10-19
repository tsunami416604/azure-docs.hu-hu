---
title: Nyomon követheti kísérletek és képzési mérőszámokat – az Azure Machine Learning |} A Microsoft Docs
description: Az Azure Machine Learning szolgáltatással nyomon követhető a kísérletek és figyelheti a mérőszámokat javíthatják a modell létrehozását. További naplózás hozzáadása a tanítási szkriptet, hogyan lehet elküldeni a kísérletet, hogyan ellenőrizheti egy futó feladat állapotát és megtekintése a Futtatás eredményeit.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 4d8b913c9e77254eb4d232e6bf5add136204b842
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427756"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Kísérletek és az Azure Machine Learning betanítási metrikák követése

Az Azure Machine Learning szolgáltatásban a kísérletek nyomon, és figyelheti a mérőszámokat javíthatják a modell létrehozását. Ebből a cikkből megtanulhatja, naplózás hozzáadása a tanítási szkriptet különféle módjai hogyan lehet elküldeni a kísérlet **start_logging** és **ScriptRunConfig**, hogyan ellenőrizheti az előrehaladását egy futó feladat, és a egy Futtatás eredményeinek megtekintése. 

>[!NOTE]
> Ebben a cikkben kód teszteltük az Azure Machine Learning SDK verziója 0.168 

## <a name="list-of-training-metrics"></a>Képzési mérőszámok listája 

A következő metrikák hozzáadhat egy Futtatás betanítási kísérlet során. Mi a Futtatás követhető részletes listáját, tekintse meg a [SDK-forrásdokumentáció](https://docs.microsoft.com/python/api/overview/azure/azure-ml-sdk-overview?view=azure-ml-py).

|Típus| Funkce Pythonu | Példa | Megjegyzések|
|----|:----|:----|:----|
|Skaláris értékek | `run.log(name, value, description='')`| `run.log("accuracy", 0.95) ` |Napló egy numerikus vagy karakterlánc értéket, a Futtatás a megadott névvel. Ennek a mutatónak tárolhatók a kísérletben a futtatási rekord a naplózási a metrika a futtató okoz.  Az azonos metrika futtató, az eredmény egy adott mérőszám vektor fontolóra belül több alkalommal bejelentkezhet.|
|Listák| `run.log_list(name, value, description='')`| `run.log_list("accuracies", [0.6, 0.7, 0.87])` | Jelentkezzen a Futtatás a megadott nevű értékek listáját.|
|Sor| `run.log_row(name, description=None, **kwargs)`| `run.log_row("Y over X", x=1, y=0.4)` | Használatával *log_row* hoz létre egy metrika több oszlopból álló kwargs leírtak szerint. Minden elnevezett paraméter generálja egy oszlop megadott értékkel.  *log_row* egyszer hívható egy hurkot, és a egy teljes táblát hozzon létre egy tetszőleges rekord, vagy több alkalommal bejelentkezni.|
|Tábla| `run.log_table(name, value, description='')`| `run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]})` | Jelentkezzen a szótárobjektum a Futtatás a megadott névvel. |
|Képek| `run.log_image(name, path=None, plot=None)`| `run.log_image("ROC", plt)` | Jelentkezzen egy képet a futtatási rekord. Be-vagy képfájl a matplotlib log_image használatával a Futtatás rajz.  Ezek a lemezképek lesz látható, és a futtatási rekord összehasonlítható.|
|Futtatás címkézése| `run.tag(key, value=None)`| `run.tag("selected", "yes")` | A Futtatás egy karakterláncot és egy opcionális karakterláncértéket a címkékkel.|
|Töltse fel a fájl vagy könyvtár|`run.upload_file(name, path_or_stream)`| Run.upload_file ("best_model.pkl", ". / model.pkl") | Töltse fel egy fájlt a futtatási rekord. Futtatások automatikusan rögzítheti a megadott kimeneti könyvtár, amely alapértelmezés szerint a fájl ". / kimenete" legtöbb Futtatás típusok esetében.  Upload_file csak akkor használja további fájlokat szeretne feltölteni, vagy nincs megadva egy kimeneti könyvtárat. Javasoljuk, hogy hozzáadása `outputs` a nevéhez, így az informatikai részleg által feltöltött lekérdezi a kimeneti könyvtár. Listázhatja az összes kapcsolódó fájlt a futtatási rekord által nevű `run.get_file_names()`|

> [!NOTE]
> Metrikák fejlécekké, a listákat, a sorok és a táblák rendelkezhet típusa: lebegőpontos, egész szám vagy karakterlánc.

## <a name="log-metrics-for-experiments"></a>Kísérletek log metrikái

Ha szeretné nyomon követni vagy figyelése is futtathatja a kísérletet, hozzá kell adnia a naplózása, amikor a Futtatás küld kódot. A futtatási küldésének aktiválásához módjai a következők:
* __Run.start_logging__ – a naplózási szolgáltatások hozzáadása a tanítási szkriptet, és a egy interaktív naplózási munkamenet elindításához a megadott kísérletben. **start_logging** notebookok – egyéb felhasználási helyzetek egy interaktív Futtatás használatra hoz létre. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.
* __ScriptRunConfig__ – a naplózási szolgáltatások hozzáadása a tanítási szkriptet, és a teljes parancsfájl mappát a Futtatás tölthető be.  **ScriptRunConfig** beállítása szkript konfigurációi fut egy osztály van. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

## <a name="set-up-the-workspace-and-experiment"></a>A munkaterület és a kísérlet
Mielőtt hozzáadná a naplózás és a egy kísérlet elküldése, be kell állítania a munkaterületet, és a kísérlet.

1. Töltse be a munkaterület. A munkaterület-konfiguráció beállításával kapcsolatos további információkért kövesse a [rövid](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. A kísérlet létrehozása.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  exp = Experiment(workspace_object = ws, name = experiment_name)
  ```
  
## <a name="option-1-use-startlogging"></a>1. lehetőség: Start_logging használata

**start_logging** notebookok – egyéb felhasználási helyzetek egy interaktív Futtatás használatra hoz létre. A munkamenet során naplózott összes metrikák kerülnek a futtatási rekordját a kísérletben.

Az alábbi példában egy egyszerű sklearn Ridge modell helyileg a helyi Jupyter notebook betanítja. A különböző környezetekben kísérletek elküldése kapcsolatos további információkért lásd: [állítsa be a modell betanítása és az Azure Machine Learning szolgáltatás a számítási célokhoz](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Helyi Jupyter notebook létrehozása egy tanítási szkriptet. 

  ``` python
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
  run = Run.start_logging(experiment = exp)
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

A parancsfájl végződik ```run.complete()```, amely jelöli meg a futtatást, befejezettként.  Ez jellemzően interaktív notebook forgatókönyvekben.

## <a name="option-2-use-scriptrunconfig"></a>2. lehetőség: ScriptRunConfig használata

**ScriptRunConfig** beállítása szkript konfigurációi fut egy osztály van. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

Ebben a példában a fent sklearn Ridge alapmodell tartalmazó gyűjteménnyel bővítjük. Egy egyszerű paraméter Ismétlés az ismétlés metrikákat rögzítheti a modell és a kísérlet alatt fut, a betanított modellek az alfa értékeknek keresztül hajtja végre. A példában helyben fut egy felhasználó által felügyelt környezetben. 

1. Hozzon létre egy tanítási szkriptet. Ez az használ ```%%writefile%%``` a betanítási kódot írja ki a parancsfájl mappába ```train.py```.

  ```python
  %%writefile $project_folder/train.py

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

2. A ```train.py``` parancsfájl-hivatkozások ```mylib.py```. Ez a fájl lehetővé teszi az alfa értékeknek a ridge modellben használandó listájának beolvasása.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Állítsa be a felhasználó által felügyelt helyi környezetben.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Küldje el a ```train.py``` parancsfájl a felhasználó által felügyelt környezetben való futtatásához. Ez a mappa teljes szkript elküldésekor a betanításhoz, beleértve a ```mylib.py``` fájlt.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_user_managed)
  run = exp.submit(src)
  ```
  
## <a name="view-run-details"></a>Futtatás részletei nézet

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>A figyelő futtatásához a Jupyter notebook widgetek
Ha a **ScriptRunConfig** elküldéséhez metódus fut, a Futtatás, a Jupyter notebook widgettel állapotát megtekintheti. A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

1. A Jupyter widget tekintse meg a Futtatás befejeződik várakozás közben.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Képernyőkép a Jupyter notebook widget](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

Modell betanítása és figyelési fordulnak elő a háttérben, hogy a várakozás közben egyéb feladatokat is futtathat. Azt is, amíg a modell több kód futtatása előtt képzési befejeződéséig. Ha használ **ScriptRunConfig**, használható ```run.wait_for_completion(show_output = True)``` megjelenítése, ha a modell betanítása befejeződött. A ```show_output``` jelző biztosít részletes kimenet. 
  
### <a name="query-run-metrics"></a>A lekérdezés futtatása a metrikák

Megtekintheti a metrikákat egy betanított modell használatával ```run.get_metrics()```. Most már beszerezheti az összes naplózott határozza meg a legjobb modellt a fenti példában a metrikákat.

<a name='view-the-experiment-in-the-web-portal'/>
## <a name="view-the-experiment-in-the-azure-portal"></a>A kísérlet megtekintéséhez az Azure Portalon

Amikor egy kísérlet befejezését követően, megnyithatja a futtatási rekord rögzített kísérletet. Ezt kétféleképpen teheti meg:

* Első futtatás URL-CÍMÉT közvetlenül ```print(run.get_portal_url())```
* A Futtatás neve elküldésével részleteinek megtekintése (ebben az esetben ```run```). Ez mutat, a kísérlet neve, azonosítója, típusa, állapot, részleteit megjelenítő oldalon, az Azure Portalon mutató hivatkozást és a dokumentációra mutató hivatkozással.

A hivatkozás a Futtatás méretezhetőséget kínál, közvetlenül a futtatási Részletek lap az Azure Portalon. Itt láthatja, bármely tulajdonságai, a nyomon követett metrikák, lemezképek és bejelentkezett a kísérlet diagram. Ebben az esetben azt naplózza, MSE és az alfa értékeknek.

  ![Képernyőkép a futtatási részletek az Azure Portalon](./media/how-to-track-experiments/run-details-page-web.PNG)

Bármely kimenetek vagy a naplók a Futtatás megtekintéséhez, vagy töltse le a kísérlet, így a kísérlet mappa másokkal is megoszthat küldte el a pillanatkép is.

## <a name="example-notebooks"></a>Példa notebookok
A következő notebookok a jelen cikk fogalmait bemutatása:
* [01.Getting-Started/01.train-within-notebook/01.train-within-notebook.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/01.train-within-notebook)
* [01.Getting-Started/02.train-on-Local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)

Ezeket a notebookokat lekérése: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki a következő lépések megtudhatja, hogyan használható az Azure Machine Learning SDK Pythonhoz készült:

* Lásd a példa bemutatja, hogyan regisztrálja a legjobb modellt, és az oktatóanyagban üzembe [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md).

* Ismerje meg, hogyan [PyTorch modellek az Azure Machine Learning](how-to-train-pytorch.md).
