---
title: Track kísérletek és képzési mérőszámok segítségével
titleSuffix: Azure Machine Learning service
description: Az Azure Machine Learning szolgáltatással nyomon követhető a kísérletek és figyelheti a mérőszámokat javíthatják a modell létrehozását. További naplózás hozzáadása a tanítási szkriptet, hogyan lehet elküldeni a kísérletet, hogyan ellenőrizheti egy futó feladat állapotát és megtekintése a Futtatás eredményeit.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 356326c7270bb18cec1fdd37dfc6f7f2638b3839
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822043"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Kísérletek és az Azure Machine Learning betanítási metrikák követése

Az Azure Machine Learning szolgáltatásban a kísérletek nyomon, és figyelheti a mérőszámokat javíthatják a modell létrehozását. Ebből a cikkből megtanulhatja, naplózás hozzáadása a tanítási szkriptet különféle módjai hogyan lehet elküldeni a kísérlet **start_logging** és **ScriptRunConfig**, hogyan ellenőrizheti az előrehaladását egy futó feladat, és a egy Futtatás eredményeinek megtekintése. 


## <a name="list-of-training-metrics"></a>Képzési mérőszámok listája 

A következő metrikák hozzáadhat egy Futtatás betanítási kísérlet során. Mi a Futtatás követhető részletes listáját, tekintse meg a [osztály dokumentációja futtatása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Típus| Funkce Pythonu | Megjegyzések|
|----|:----|:----|
|Skaláris értékek |Függvény:<br>`run.log(name, value, description='')`<br><br>Példa:<br>Run.log ("pontossággal", 0,95) |Napló egy numerikus vagy karakterlánc értéket, a Futtatás a megadott névvel. Ennek a mutatónak tárolhatók a kísérletben a futtatási rekord a naplózási a metrika a futtató okoz.  Az azonos metrika futtató, az eredmény egy adott mérőszám vektor fontolóra belül több alkalommal bejelentkezhet.|
|Listák|Függvény:<br>`run.log_list(name, value, description='')`<br><br>Példa:<br>Run.log_list ("pontosságú", [a 0.6-os, 0,7, 0.87]) | Jelentkezzen a Futtatás a megadott nevű értékek listáját.|
|Sor|Függvény:<br>"run.log_row (név, leírás = None, ** kwargs)<br>Példa:<br>Run.log_row ("Keresztül X Y", x = 1, y = 0,4) |} Használatával *log_row* hoz létre egy metrika több oszlopból álló kwargs leírtak szerint. Minden elnevezett paraméter generálja egy oszlop megadott értékkel.  *log_row* egyszer hívható egy hurkot, és a egy teljes táblát hozzon létre egy tetszőleges rekord, vagy több alkalommal bejelentkezni.|
|Tábla|Függvény:<br>`run.log_table(name, value, description='')`<br><br>Példa:<br>Run.log_table ("Keresztül X Y", {"x": [1, 2, 3], "y": [a 0.6-os, 0,7, 0.89]}) | Jelentkezzen a szótárobjektum a Futtatás a megadott névvel. |
|Képek|Függvény:<br>`run.log_image(name, path=None, plot=None)`<br><br>Példa:<br>Run.log_image ("ROC",. lbi) | Jelentkezzen egy képet a futtatási rekord. Be-vagy képfájl a matplotlib log_image használatával a Futtatás rajz.  Ezek a lemezképek lesz látható, és a futtatási rekord összehasonlítható.|
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

1. Töltse be a munkaterület. A munkaterület-konfiguráció beállításával kapcsolatos további információkért kövesse a [rövid](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Experiment, Run, Workspace
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Option 1: Start_logging használata

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
  # Get an experiment object from Azure Machine Learning
  experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
  # Create a run object in the experiment
  run = experiment.start_logging()# Log the algorithm parameter alpha to the run
  run.log('alpha', 0.03)

  # Create, fit, and test the scikit-learn Ridge regression model
  regression_model = Ridge(alpha=0.03)
  regression_model.fit(data['train']['X'], data['train']['y'])
  preds = regression_model.predict(data['test']['X'])

  # Output the Mean Squared Error to the notebook and to the run
  print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
  run.log('mse', mean_squared_error(data['test']['y'], preds))

  # Save the model to the outputs directory for capture
  joblib.dump(value=regression_model, filename='outputs/model.pkl')

  # Take a snapshot of the directory containing this notebook
  run.take_snapshot('./')

  # Complete the run
  run.complete()
  
  ```

A parancsfájl végződik ```run.complete()```, amely jelöli meg a futtatást, befejezettként.  Ezt a funkciót általában interaktív notebook olyan forgatókönyvekben használatos.

## <a name="option-2-use-scriptrunconfig"></a>Option 2: ScriptRunConfig használata

**ScriptRunConfig** beállítása szkript konfigurációi fut egy osztály van. Ezzel a beállítással adhat hozzá figyelés kódja befejezéséről értesítést, vagy egy vizuális widget figyelése lekéréséhez.

Ebben a példában a fent sklearn Ridge alapmodell tartalmazó gyűjteménnyel bővítjük. Egy egyszerű paraméter Ismétlés az ismétlés metrikákat rögzítheti a modell és a kísérlet alatt fut, a betanított modellek az alfa értékeknek keresztül hajtja végre. A példában helyben fut egy felhasználó által felügyelt környezetben. 

1. Hozzon létre egy tanítási szkriptet `train.py`.

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

2. A `train.py` parancsfájl-hivatkozások `mylib.py` lehetővé teszi az alfa értékeknek a ridge modellben használandó listájának beolvasása.

  ```python
  # mylib.py
  
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
  
  experiment = Experiment(workspace=ws, name="train-on-local")
  src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
  run = experiment.submit(src)
  ```

## <a name="cancel-a-run"></a>A Futtatás megszakítása
Futtatás ésőbb elküldésekor, lemondhatja a szolgáltatásokat, akkor is, ha elvesztette az objektumhivatkozás mindaddig, amíg a kísérlet neve ismeri, és futtatási azonosítóját. 

```python
from azureml.core import Experiment
exp = Experiment(ws, "my-experiment-name")

# if you don't know the run id, you can list all runs under an experiment
for r in exp.get_runs():  
    print(r.id, r.get_status())

# if you know the run id, you can "rehydrate" the run
from azureml.core import get_run
r = get_run(experiment=exp, run_id="my_run_id", rehydrate=True)
  
# check the returned run type and status
print(type(r), r.get_status())

# you can cancel a run if it hasn't completed or failed
if r.get_status() not in ['Complete', 'Failed']:
    r.cancel()
```
Vegye figyelembe, hogy jelenleg csak ScriptRun és PipelineRun típusokat támogatja-e a művelet megszakítása.

Ezenkívül lemondhatja a szolgáltatásokat egy Futtatás a következő parancsot a CLI-n keresztül:
```shell
az ml run cancel -r <run_id> -p <project_path>
```


## <a name="view-run-details"></a>Futtatás részletei nézet

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>A figyelő futtatásához a Jupyter notebook widgetek
Ha a **ScriptRunConfig** elküldéséhez metódus fut, a Futtatás, a Jupyter notebook widgettel állapotát megtekintheti. A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.

1. A Jupyter widget tekintse meg a Futtatás befejeződik várakozás közben.

  ```python
  from azureml.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Képernyőkép a Jupyter notebook widget](./media/how-to-track-experiments/widgets.PNG)

2. **[Automatikus machine learning futtatások]**  Az előző menetből a diagramok eléréséhez. Cserélje le `<<experiment_name>>` megfelelő kísérlet nevét:

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

Amikor egy kísérlet befejezését követően, megnyithatja a futtatási rekord rögzített kísérletet. Teheti kétféleképpen az előzmények elérése:

* Első futtatás URL-CÍMÉT közvetlenül ```print(run.get_portal_url())```
* A Futtatás neve elküldésével részleteinek megtekintése (ebben az esetben ```run```). Ezzel a módszerrel a kísérlet neve, azonosítója, típus, állapot, részleteit megjelenítő oldalon, egy hivatkozást az Azure Portalra és dokumentáció mutató hivatkozás mutat.

A hivatkozás a Futtatás méretezhetőséget kínál, közvetlenül a futtatási Részletek lap az Azure Portalon. Itt láthatja, bármely tulajdonságai, a nyomon követett metrikák, lemezképek és bejelentkezett a kísérlet diagram. Ebben az esetben azt naplózza, MSE és az alfa értékeknek.

  ![Futtatás részletei az Azure Portalon](./media/how-to-track-experiments/run-details-page-web.PNG)

Bármely kimenetek vagy a naplók a Futtatás megtekintéséhez, vagy töltse le a kísérlet, így a kísérlet mappa másokkal is megoszthat küldte el a pillanatkép is.

### <a name="viewing-charts-in-run-details"></a>Diagramok részleteinek megtekintése

Különféle módon használhatja a naplózási metrikák rekord különböző típusú API-kat a futtatás során, és megtekintheti őket, mint a diagramok az Azure Portalon. 

|A naplózott érték|Példakód| A portál megtekintése|
|----|----|----|
|Napló numerikus értékek tömbje| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Single-variable vonaldiagram|
|Az azonos nevű metrika ismételten használt egyetlen numerikus érték naplózása (hasonló belül egy "for" ciklus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Single-variable vonaldiagram|
|Jelentkezzen egy sor 2 numerikus oszlopok ismételten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Kétváltozós vonaldiagram|
|A numerikus oszlopok 2 naplótábláját|`run.log_table(name='Sine Wave', value=sines)`|Kétváltozós vonaldiagram|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Understanding automatikus ML-diagramok

Egy jegyzetfüzet egy automatizált gépi Tanulási feladat elküldése után ezek futtatások előzményei a machine learning-szolgáltatás munkaterület található. 

További információk:
+ [Diagramok és görbék képbesorolási modellek esetében](#classification)
+ [Diagramok és grafikonok, regressziós modellek](#regression)
+ [Modell lehetővé teszi azt ismertetik](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>A futtatási diagramok megtekintése

1. Lépjen a munkaterülethez. 

1. Válassza ki **kísérletek** a munkaterület a bal oldali panelen.

  ![Kísérlet menü képernyőképe](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Jelölje ki az Önt érdeklő kísérletet.

  ![Kísérlet listája](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. A tábla válassza ki a Futtatás száma.

   ![Kísérlet futtatása](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1.  A tábla a modellhez, amely a kívánt vizsgálódáshoz iteráció számának kiválasztása

   ![Kísérlet modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>Besorolás

Minden besorolás modell, amely az automatizált gépi tanulási funkciókat az Azure Machine Learning segítségével tekintheti meg a következő diagramok: 
+ [Keveredési mátrix](#confusion-matrix)
+ [Pontosság-visszahívási diagram](#precision-recall-chart)
+ [Fogadó működési jellemzői (vagy ROC)](#ROC)
+ [Átemelés görbévé](#lift-curve)
+ [Nyereség görbévé](#gains-curve)
+ [Hitelesítési diagram](#calibration-plot)

#### <a name="confusion-matrix"></a>Keveredési mátrix

Egy keveredési mátrixot egy osztályozási modell teljesítményét leírására szolgál. Minden egyes sor jeleníti meg az IGAZ osztály példányait, és minden oszlop felel meg a példányok, az előrejelzett osztály. A keveredési mátrix megfelelően osztályozott címkéket és a egy adott modell hibásan besorolt címkéit jeleníti meg.

Az Azure Machine Learning osztályozási problémák, automatikusan biztosít mindegyik modellt, amely egy keveredési mátrixot. Az egyes keveredési mátrixot automatizált gépi Tanulási vörös, zöld és hibásan besorolt címkékként jelennek megfelelően osztályozott címkéket. A kör mérete az adott doboz minták számát jelöli. Emellett a szomszédos sávdiagramok minden egyes előre jelzett címke és az egyes igaz címke gyakorisága száma megtalálható. 

1. példa: Egy gyenge pontosságú osztályozási modell ![gyenge pontosságú osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

2. példa Nagy pontosságú (ideális) osztályozási modell ![nagy pontosságú osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Pontosság-visszahívási diagram

Az ezen a diagramon hasonlítsa össze a pontosság-visszahívási görbék egyes modellekre meghatározni, mely a modellnek van egy elfogadható pontosság és a visszaírási az adott üzleti probléma közötti kapcsolat. A diagram bemutatja a makró átlagos pontosság-visszahívási, a Micro átlagos pontosság-visszahívási és a egy modell minden osztály társított pontosság-visszahívási.

A pontosság jelöli, hogy arra, hogy az összes példány megfelelően címkézése besorolás kifejezés. Visszaírási arra, hogy az összes példányát egy adott címkét az osztályozó által igénybe vett jelöli. A pontosság-visszahívási görbe két ezek a fogalmak közötti kapcsolatot mutatja. Ideális esetben a modell kellene pontosság 100 %-os és 100 %-os pontossággal.

1. példa: Alacsony pontosság és alacsony visszaírási osztályozási modell ![alacsony pontosság és alacsony visszaírási osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

2. példa ~ 100 % -os pontosság és a ~ 100 % -os visszaírási (ideális) osztályozási modell ![egy besorolási modell nagy pontosságú és visszaírási](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

Jellemző (vagy ROC) működő fogadó egy diagram, a megfelelő osztályozott címkék és a egy adott modell hibásan besorolt címkéit. ROC-görbe kevesebb adatot tartalmazó lehet, amikor nagy eltérés, mivel az adatkészletek képzési modellek nem jelenik meg a hamis pozitív címkéket.

1. példa: Alacsony valódi címkéket és a magas false (hamis) címke osztályozási modell ![osztályozási modell a valódi címkéket alacsony és magas false (hamis) címke](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

2. példa Valódi címkéket magas és alacsony false (hamis) címke osztályozási modell ![valódi címkéket magas és alacsony false (hamis) címke osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Átemelés görbévé

Összehasonlíthatja a felvonó, a létrehozott modell automatikusan az Azure Machine Learning az alaptervhez annak érdekében, hogy az érték nyer, hogy az adott modell megtekintéséhez.

Lift diagramok segítségével egy osztályozási modell teljesítményének értékeléséhez. Megmutatja, hogyan sokkal jobban várható, hogy a modell egy modell nélkül képest. 

1. példa: Modell hajt végre egy véletlenszerű kijelölés modell rosszabb ![egy osztályozási modell, amelyek rosszabb, mint egy véletlenszerű kiválasztása](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

2. példa Modell jobban, mint egy véletlenszerű kijelölés modell hajt végre ![osztályozási modell, amely jobb](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Nyereség görbévé

Az adatok minden egyes része egy osztályozási modell teljesítményét értékeli ki a nyereséget diagram. Az egyes PERCENTILIS mennyi jobban várható végrehajtásához egy véletlenszerű kijelölés modell összehasonlítja az adatkészlet jeleníti meg.

A halmozott nyereség diagram segítségével válassza ki a besorolást megszakítási használatával, amely megfelel a modellből a kívánt nyereség százalékban. Ezt az információt biztosít egy másik módszer a megnézzük a kísérő növekedési diagramon az eredményeket.

1. példa: Minimális nyereség osztályozási modell ![minimális nyereség osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

2. példa Jelentős nyereség osztályozási modell ![jelentős nyereség osztályozási modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Hitelesítési diagram

Az összes besorolási kapcsolatos problémák esetén a hitelesítési sor micro – átlag, a makró-átlagos és a egy adott prediktív modellt az egyes osztályok tekintheti meg. 

Hitelesítési rajzot a prediktív modellek magabiztosan megjelenítésére szolgál. Ezt nem: Megjeleníti az előre jelzett valószínűség és a tényleges valószínűsége közötti kapcsolat, ahol "valószínűség" jelenti a valószínűsége, hogy az adott példány néhány címke alatt tartozik. Egy jól hitelesített modell igazítja az y = x sor, ahol a szolgáltatás ésszerűen abban az előrejelzéseket. Egy túlzott confident modell igazítja az y = 0 sort, amelyben az előre jelzett valószínűség megtalálható, de nem tényleges valószínűsége.

1. példa: Egy jól hitelesített modell ![ jól hitelesített modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

2. példa Egy túlzott confident modell ![egy túlzott confident modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regresszió
Minden regressziós modell az automatizált gépi tanulási funkciókat az Azure Machine Learning segítségével létrehozhat, a következő diagram látható: 
+ [Előre jelzett vs. Igaz](#pvt)
+ [Például hisztogramja](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Előre jelzett vs. True (Igaz)

Előre jelzett vs. Igaz előre jelzett érték és a egy regressziós probléma a correlating IGAZ érték közötti kapcsolatot mutatja. Ez a diagram segítségével mérheti a modell teljesítményét, minél közelebb az y = x sor az előre jelzett értékek a következők, annál jobb a prediktív modell pontosságát.

Minden egyes futás után megjelenik egy előre jelzett és minden egyes regressziós modell igaz grafikon. Adatok védelme érdekében értékek együtt vannak binned, és a egy oszlopdiagram a diagramterület alsó részén jelenik meg minden doboz méretét. Hiba történt a margók, ahol a modell lehet ideális megoldás értékkel megjelenítő világosabb terület árnyékolása a össze lehessen hasonlítani a prediktív modellben.

1. példa: Egy regressziós modellt, kis pontosságú az előrejelzések ![alacsony pontosságát az előrejelzések regressziós modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

2. példa Egy regressziós modellt, az előrejelzéseket a nagy pontosságú ![egy regressziós modellt, az előrejelzéseket a nagy pontosságú](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Például hisztogramja

A fennmaradó megfigyelt y – az előre jelzett y jelöli. A hibahatáron az alacsony eltérés megjelenítéséhez, például a hisztogram kell alakúak lehetnek harang görbe 0 Eszközkezelőre. 

1. példa: A hibák az eltérés regressziós modell ![SA regressziós modellt a hibák az eltérés](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

2. példa Még több telepítési hibák regressziós modell ![hibák még több terjesztési regressziós modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>A modellekre teszi azt ismertetik, és a szolgáltatás fontosság

Funkció fontosság biztosít, amely jelzi, hogy milyen értékes alapdokumentációjában volt a modell felépítése a pontszámot. A funkció fontos pontszám modell átfogó, valamint egy prediktív modellt az osztály egy tekintheti meg. Szolgáltatás / láthatja, hogyan viszonyul fontosságát, minden egyes osztály ellen, és teljes.

![A szolgáltatás magyarázat képessége](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Példa notebookok
A következő notebookok a jelen cikk fogalmait bemutatása:
* [How-to-use-azureml/Training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training\train-within-notebook)
* [How-to-use-azureml/Training/train-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/Training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Próbálja ki a következő lépések megtudhatja, hogyan használható az Azure Machine Learning SDK Pythonhoz készült:

* Lásd a példa bemutatja, hogyan regisztrálja a legjobb modellt, és az oktatóanyagban üzembe [betanításához egy kép osztályozási modell az Azure Machine Learning](tutorial-train-models-with-aml.md).

* Ismerje meg, hogyan [PyTorch modellek az Azure Machine Learning](how-to-train-pytorch.md).
