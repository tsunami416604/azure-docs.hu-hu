---
title: 'A képbesorolás oktatóanyaga: Modellek betanítása'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan taníthat egy képbesorolási modellt a scikit-Learn egy Python Jupyter notebookon Azure Machine Learning szolgáltatással. Ez az oktatóanyag egy kétrészes sorozat első része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 97e3fcb732e85f8c190a0d6607d85a6ffc8d36a7
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370742"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Oktatóanyag: Képosztályozási modellek betanítása MNIST-adatokkal és scikit – további tudnivalók a Azure Machine Learning használatával

Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani a távoli számítási erőforrásokra. Az Azure Machine Learning szolgáltatás (előzetes verzió) betanítási és üzembehelyezési munkafolyamatát fogja használni egy Python Jupyter-notebookban.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regressziót mutat be a [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlettel és a [scikit](https://scikit-learn.org) Azure Machine learning szolgáltatással való megismerésével. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden rendszerkép egy 28 x 28 képpontos, a nulla és kilenc közötti számot jelölő, kézzel írt számjegy. A cél egy többosztályos osztályozó létrehozása az adott rendszerkép által reprezentált számjegy azonosításához.

Ismerje meg, hogyan végezheti el a következő műveleteket:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Egy egyszerű logisztikai regressziós modell betanítása egy távoli fürtön.
> * Tekintse át a képzés eredményeit, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan választhatja ki a modelleket, és hogyan telepítheti azt az [oktatóanyag második részében](tutorial-deploy-models-with-aml.md).

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

>[!NOTE]
> A cikkben ismertetett kód Azure Machine Learning SDK 1.0.41-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

Ugorjon a telepítési [környezet beállítása](#start) a jegyzetfüzet lépéseinek beolvasásához, vagy az alábbi utasításokat követve szerezze be a jegyzetfüzetet, és futtassa azt Azure Notebooks vagy a saját notebook-kiszolgálóján.  A jegyzetfüzet futtatásához a következőkre lesz szüksége:

* Python 3,6 notebook-kiszolgáló a következő telepítéssel:
    * A Pythonhoz készült Azure Machine Learning SDK
    * `matplotlib` és `scikit-learn`
* Az oktatóanyag jegyzetfüzet és a fájl **utils.py**
* Machine learning-munkaterület
* A munkaterülethez tartozó konfigurációs fájl ugyanabban a könyvtárban, mint a jegyzetfüzet

Az alábbi részekben ismertetett előfeltételek bármelyikét lekérheti.

* [Felhőalapú notebook-kiszolgáló használata a](#azure) munkaterületen
* [Saját notebook-kiszolgáló](#server) használata

### <a name="azure"></a>Felhőalapú notebook-kiszolgáló használata a munkaterületen

Könnyedén megkezdheti saját felhőalapú notebook-kiszolgálójának megkezdését. A [Pythonhoz készült Azure Machine learning SDK](https://aka.ms/aml-sdk) már telepítve van és konfigurálva van a felhőalapú erőforrás létrehozása után.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* A jegyzetfüzet weboldalának elindítása után nyissa meg az **oktatóanyagok/IMG-Classification-part1-Training. ipynb** jegyzetfüzetet.

### <a name="server"></a>Saját Jupyter notebook-kiszolgáló használata

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 A lépések elvégzése után futtassa az **oktatóanyagok/IMG-Classification-part1-Training. ipynb** notebookot a klónozott címtárból.

## <a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Python-csomagok importálása.
* Kapcsolódjon egy munkaterülethez, hogy a helyi számítógép képes legyen kommunikálni a távoli erőforrásokkal.
* Hozzon létre egy kísérletet az összes Futtatás nyomon követéséhez.
* Hozzon létre egy távoli számítási célt, amelyet a képzéshez használhat.

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. Megjeleníti a Azure Machine Learning SDK verzióját is:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Kapcsolódás munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()`beolvassa a **config. JSON** fájlt, és betölti a részleteket `ws`egy nevű objektumba:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterület több kísérlettel is rendelkezhet:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Meglévő számítási erőforrás létrehozása vagy csatolása

A felügyelt Azure Machine Learning számítási szolgáltatással az adatszakértők a gépi tanulási modelleket az Azure-beli virtuális gépek fürtjén is betanítják. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban Azure Machine Learning számítást hoz létre képzési környezetként. Az alábbi kód létrehozza a számítási fürtöket, ha azok még nem léteznek a munkaterületen.

 **A számítás létrehozása körülbelül öt percet vesz igénybe.** Ha a számítás már szerepel a munkaterületen, a kód ezt használja, és kihagyja a létrehozási folyamatot.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Most már rendelkezésre állnak a modell felhőben történő betanításához szükséges csomagok és számítási erőforrások.

## <a name="explore-data"></a>Adatok megismerése

A modellek betanítása előtt meg kell ismernie a betanításához használt adattípust. A felhőbe is át kell másolnia az adatfájlokat. Ezt követően a felhőalapú képzési környezete is elérhető. Ebből a szakaszból megtudhatja, hogyan végezheti el a következő műveleteket:

* Töltse le a MNIST adatkészletet.
* Néhány mintakép megjelenítése.
* Adatok feltöltése a felhőbe.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

Töltse le az MNIST-adathalmazt, és mentse a fájlokat egy helyi `data` könyvtárba. A betanítási és tesztelési célú lemezképek és címkék letöltése:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

A következőhöz hasonló kimenet jelenik meg:```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Mintaképek megjelenítése

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ehhez a lépéshez egy `load_data` `util.py` fájlhoz tartozó függvényre van szükség. Ezt a fájlt a mintamappa tartalmazza. Győződjön meg arról, hogy az a jegyzetfüzettel megegyező mappába van helyezve. A `load_data` függvény egyszerűen elemzi a tömörített fájlokat a NumPy tömbökben:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

A véletlenszerű képminta a következőket jeleníti meg:

![Véletlenszerűen kiválasztott képek](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

### <a name="upload-data-to-the-cloud"></a>Adatok feltöltése a felhőbe

Most tegye elérhetővé az adatok távoli elérését a helyi gépről származó adatok Azure-ba való feltöltésével. Ezt követően a távoli betanításhoz is elérhető. Az adattár a munkaterülethez tartozó, az adatok feltöltésére és letöltésére szolgáló kényelmes szerkezet. A távoli számítási célokból is használhatja azt. Ezt egy Azure Blob Storage-fiók támogatja.

A rendszer feltölti a MNIST-fájlokat az `mnist` adattár gyökerében elnevezett könyvtárba:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist',
          overwrite=True, show_progress=True)
```

Most már a modell betanításának megkezdéséhez szükséges összes előfeltétellel rendelkezik.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

E gyakorlat céljából küldje el a feladatot a korábban beállított távoli betanítási fürtnek.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Hozzon létre egy estimator objektumot
* Feladat küldése

### <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy kódtárat, amellyel eljuttathatja a szükséges kódot a számítógépéről a távoli erőforrásra.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Betanító szkript létrehozása

A feladatnak a fürtre való elküldéséhez először hozzon létre egy betanító szkriptet. A `train.py` nevű betanító szkript imént létrehozott könyvtárban történő létrehozásához futtassa a következő kódot.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Figyelje meg, hogyan kéri le a szkript az adatokat, és menti a modelleket:

+ A betanítási parancsfájl beolvas egy argumentumot, amely az adatokat tartalmazó könyvtárat keresi. Amikor később elküldi a feladatot, az adattárban a következő argumentumra kell mutatnia: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ A betanítási szkript egy **kimenet**nevű könyvtárba menti a modellt. Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. Ehhez a címtárhoz az oktatóanyagban később férhet hozzá a modellhez. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ A betanítási parancsfájl használatához `utils.py` a fájlnak megfelelően be kell töltenie az adatkészletet. A következő kód bemásolja `utils.py` `script_folder` a fájlt, hogy a fájl elérhető legyen a távoli erőforráson lévő betanítási parancsfájllal együtt.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Becslő létrehozása

A Futtatás elküldéséhez egy [SKLearn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objektumot kell használni. Hozza létre a kalkulátort a következő kód futtatásával az elemek definiálásához:

* A kalkulátor objektum neve, `est`.
* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használja.
* A betanítási szkript neve, **Train.py**.
* A betanítási parancsfájlhoz szükséges paraméterek.

Ebben az oktatóanyagban ez a cél AmlCompute. A parancsfájl mappájában lévő összes fájl a fürt csomópontjaiba lesz feltöltve futtatásra. A **data_folder** az adattár `ds.path('mnist').as_mount()`használatára van beállítva:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Feladat küldése a fürtnek

Futtassa a kísérletet a kalkulátor-objektum elküldésével:

```python
run = exp.submit(config=est)
run
```

Mivel a hívás aszinkron, egy előkészítési vagy  futtatási  állapotot ad vissza, amint a feladatot elindítják.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Összességében az első futtatás **körülbelül 10 percet**vesz igénybe. A későbbi futtatások esetében azonban, ha a parancsfájl függőségei nem változnak, ugyanazt a rendszerképet használja a rendszer. Így a tároló indítási ideje sokkal gyorsabb.

Mi történik a várakozás közben:

- **Rendszerkép létrehozása**: A rendszer létrehoz egy Docker-rendszerképet, amely megfelel a kalkulátor által megadott Python-környezetnek. A rendszerkép feltöltődik a munkaterületre. A képek létrehozása és feltöltése **körülbelül öt percet**vesz igénybe.

  Ez a szakasz egyszer fordul elő minden Python-környezetben, mert a tároló gyorsítótárazva lesz a későbbi futtatásokhoz. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. A képlétrehozási folyamat nyomon követhető a naplók használatával.

- **Méretezés**: Ha a távoli fürtnek több csomópontra van szüksége a jelenleg elérhető futtatáshoz, a további csomópontok automatikusan hozzáadódnak. A skálázás általában **körülbelül öt percet** vesz igénybe.

- **Futtatás**: Ebben a szakaszban a rendszer elküldi a szükséges parancsfájlokat és fájlokat a számítási célra. Az adattárolók csatlakoztatása vagy másolása megtörtént. Majd futtassa a **entry_script** . Amíg a feladatok futnak, az **StdOut** és a **./logs** könyvtár a futtatási előzményekre van továbbítva. A Futtatás előrehaladását a naplók használatával figyelheti.

- **Feldolgozás utáni**: A rendszer átmásolja a futtatáshoz tartozó **./outputs** könyvtárat a munkaterület futtatási előzményeire, így elérheti ezeket az eredményeket.

A futó feladatok előrehaladását többféleképpen is megtekintheti. Ez az oktatóanyag egy Jupyter widgetet és `wait_for_completion` egy metódust használ.

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Tekintse meg a Futtatás folyamatát egy [Jupyter widgettel](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). A futtatási beküldéshez hasonlóan a widget aszinkron módon működik, és 10 – 15 másodpercig élő frissítéseket biztosít a feladatok befejezéséig:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

A widget a következőhöz hasonlóan fog kinézni a képzés végén:

![Jegyzetfüzet widget](./media/tutorial-train-models-with-aml/widget.png)

Ha le kell mondania egy futtatást, kövesse [az alábbi utasításokat](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. Várjon, amíg a modell befejezte a képzést, mielőtt további kódokat futtasson. A `wait_for_completion` következő paranccsal jelenítheti meg, hogy mikor fejeződik be a modell betanítása:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel. Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```

A kimenetben látható, hogy a távoli modell pontossága 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

A következő oktatóanyagban részletesebben megismerheti ezt a modellt.

## <a name="register-model"></a>Modell regisztrálása

A betanítási parancsfájl utolsó lépése a fájlt `outputs/sklearn_mnist_model.pkl` egy, a fürt virtuális gépe nevű `outputs` könyvtárban írta, amelyben a feladatot futtatja. `outputs`egy speciális könyvtár, amelyben a címtárban található összes tartalom automatikusan fel lesz töltve a munkaterületre. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. Így a modell fájlja már elérhető a munkaterületen is.

A futtatáshoz tartozó fájlok láthatók:

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, hogy Ön vagy más közreműködők később is lekérdezzenek, megvizsgálják és üzembe helyezhetik a modellt:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

A Azure Machine Learning számítási fürtöt is törölheti. Az autoskálázás azonban be van kapcsolva, és a fürt minimális értéke nulla. Így az adott erőforrás nem jár további számítási költségekkel, ha nincs használatban:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>További lépések

Ebben a Azure Machine Learning szolgáltatási oktatóanyagban a Pythont használtuk a következő feladatokhoz:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Több modell betanítása egy távoli fürtön a népszerű scikit-Learn Machine learning-kódtár használatával
> * Tekintse át a képzés részleteit, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezésére az oktatóanyag-sorozat következő részében található utasítások használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
