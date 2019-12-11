---
title: 'Rendszerképek besorolása oktatóanyag: a betanítási modellek'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan taníthat egy képbesorolási modellt a scikit-Learn egy Python Jupyter notebookon Azure Machine Learning használatával. Ez az oktatóanyag egy kétrészes sorozat első része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ca0f64fe67865e18c47009779cf8bd307a21c961
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978730"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Oktatóanyag: képosztályozási modellek betanítása MNIST-adatokkal és scikit – további tudnivalók a Azure Machine Learning használatával
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani a távoli számítási erőforrásokra. A Azure Machine Learning képzési és üzembe helyezési munkafolyamatát egy Python Jupyter notebookon fogja használni.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regressziót mutat be a [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlettel és a [scikit-Learn](https://scikit-learn.org) Azure Machine learning használatával. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden rendszerkép egy 28 x 28 képpontos, a nulla és kilenc közötti számot jelölő, kézzel írt számjegy. A cél egy többosztályos besoroló létrehozása, amely képes azonosítani az egyes képeken látható számjegyeket.

Ismerje meg, hogyan végezheti el a következő műveleteket:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Egy egyszerű logisztikai regressziós modell betanítása egy távoli fürtön.
> * Tekintse át a képzés eredményeit, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan választhatja ki a modelleket, és hogyan telepítheti azt az [oktatóanyag második részében](tutorial-deploy-models-with-aml.md).

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

>[!NOTE]
> A cikkben ismertetett kód [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 1.0.65-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az [oktatóanyagot: első lépésként hozza létre első Azure ml-kísérletét](tutorial-1st-experiment-sdk-setup.md) :
    * Munkaterületek létrehozása
    * Az oktatóanyagok notebookjának klónozása a munkaterületen lévő mappába.
    * Felhőalapú notebook-alapú virtuális gép létrehozása.

* A klónozott **oktatóanyagok** mappában Nyissa meg az **IMG-besorolás-part1-Training. ipynb** notebookot. 


Az oktatóanyag és a kapcsolódó **utils.py** -fájl a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha a saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni. A `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` futtatása az oktatóanyag függőségeinek telepítéséhez.

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint amit a jegyzetfüzetben lát.  
>
> Váltson a Jupyter jegyzetfüzetre, ha a kód futtatása közben szeretné olvasni. 
> Ha egyetlen kód cellát szeretne futtatni egy jegyzetfüzetben, kattintson a kód cellára, és nyomja le a **SHIFT + ENTER billentyűkombinációt**. Vagy futtassa a teljes jegyzetfüzetet úgy, hogy az **összes futtatása** lehetőséget választja a felső eszköztáron.

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

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()` beolvassa a **config. JSON** fájlt, és betölti a részleteket egy `ws`nevű objektumba:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterület több kísérlettel is rendelkezhet:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Meglévő számítási cél létrehozása vagy csatolása

A felügyelt Azure Machine Learning számítási szolgáltatással az adatszakértők a gépi tanulási modelleket az Azure-beli virtuális gépek fürtjén is betanítják. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban Azure Machine Learning számítást hoz létre képzési környezetként. Az alábbi kód létrehozza a számítási fürtöket, ha azok még nem léteznek a munkaterületen.

 **A számítási cél létrehozása körülbelül öt percet vesz igénybe.** Ha a számítási erőforrás már szerepel a munkaterületen, a kód ezt használja, és kihagyja a létrehozási folyamatot.

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

A modellek betanítása előtt meg kell ismernie a betanításához használt adattípust. Ebben a szakaszban megtanulhatja a következőket:

* Töltse le a MNIST adatkészletet.
* Néhány mintakép megjelenítése.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

A nyers MNIST-adatfájlok beszerzéséhez használja az Azure Open adatkészleteket. Az [Azure Open-adatkészletek](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. Az egyes adatkészletek a megfelelő osztállyal rendelkeznek, `MNIST` ebben az esetben az adat különböző módokon történő lekéréséhez.

Ez a kód `FileDataset` objektumként kérdezi le az adatmennyiséget, amely a `Dataset`alosztálya. A `FileDataset` az adattárolókban vagy a nyilvános URL-címekben lévő bármilyen formátumú fájlt egy vagy több fájlra hivatkozik. Az osztály lehetővé teszi a fájlok a számításhoz való letöltését vagy csatlakoztatását az adatforrás helyére mutató hivatkozás létrehozásával. Emellett regisztrálja az adatkészletet a munkaterületen a betanítás során történő egyszerű lekéréshez.

Az adatkészletekről és azok használatáról az SDK-ban kövesse az [útmutató](how-to-create-register-datasets.md) című témakört.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Mintaképek megjelenítése

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ehhez a lépéshez egy `util.py`-fájlban szereplő `load_data` függvényre van szükség. Ezt a fájlt a mintamappa tartalmazza. Győződjön meg arról, hogy az a jegyzetfüzettel megegyező mappába van helyezve. A `load_data` függvény egyszerűen elemzi a tömörített fájlokat a NumPy tömbökbe.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, "train-images-idx3-ubyte.gz"), False) / 255.0
X_test = load_data(os.path.join(data_folder, "t10k-images-idx3-ubyte.gz"), False) / 255.0
y_train = load_data(os.path.join(data_folder, "train-labels-idx1-ubyte.gz"), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, "t10k-labels-idx1-ubyte.gz"), True).reshape(-1)

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

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

E gyakorlat céljából küldje el a feladatot a korábban beállított távoli betanítási fürtnek.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Kalkulátor-objektum létrehozása
* Feladat küldése

### <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy kódtárat, amellyel eljuttathatja a szükséges kódot a számítógépéről a távoli erőforrásra.

```python
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
import glob

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
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

+ A betanítási parancsfájlhoz az adathalmaz megfelelő betöltéséhez `utils.py` fájl szükséges. A következő kód `utils.py`t másolja a `script_folder`ba, hogy a fájl elérhető legyen a távoli erőforráson a betanítási szkripttel együtt.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Becslő létrehozása

A Futtatás elküldéséhez egy [SKLearn kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objektumot kell használni. A becslő létrehozásához határozza meg a következő elemeket az alábbi kód futtatásával:

* A becslőobjektum neve (`est`).
* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használja.
* A betanítási szkript neve, **train.py**.
* A betanítási szkript szükséges paraméterei.

Ebben az oktatóanyagban ez a cél a AmlCompute. A parancsfájl mappájában lévő összes fájl a fürt csomópontjaiba lesz feltöltve futtatásra. A **data_folder** az adatkészlet használatára van beállítva. Először hozzon létre egy környezeti objektumot, amely meghatározza a betanításhoz szükséges függőségeket. 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

Ezután hozza létre a kalkulátort a következő kóddal.

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env, 
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Feladat küldése a fürtnek

Futtassa a kísérletet a kalkulátor-objektum elküldésével:

```python
run = exp.submit(config=est)
run
```

Mivel a hívás aszinkron, egy **előkészítési** vagy **futtatási** állapotot ad vissza, amint a feladatot elindítják.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Összességében az első futtatás **körülbelül 10 percet**vesz igénybe. A későbbi futtatások esetében azonban, ha a parancsfájl függőségei nem változnak, ugyanazt a rendszerképet használja a rendszer. Így a tároló indítási ideje sokkal gyorsabb.

Mi történik a várakozás közben:

- **Rendszerkép létrehozása**: a rendszer létrehoz egy Docker-rendszerképet, amely megfelel a kalkulátor által meghatározott Python-környezetnek. A rendszerkép feltöltődik a munkaterületre. A képek létrehozása és feltöltése **körülbelül öt percet**vesz igénybe.

  Ez a szakasz egyszer fordul elő minden Python-környezetben, mert a tároló gyorsítótárazva lesz a későbbi futtatásokhoz. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. A képlétrehozási folyamat nyomon követhető a naplók használatával.

- **Skálázás**: Ha a távoli fürtnek több csomópontra van szüksége a jelenleg elérhető futtatáshoz, a további csomópontok automatikusan hozzáadódnak. A skálázás általában **körülbelül öt percet** vesz igénybe.

- **Futtatás**: ebben a szakaszban a szükséges parancsfájlokat és fájlokat a rendszer elküldi a számítási célra. Az adattárolók csatlakoztatása vagy másolása megtörtént. Ezután a **entry_script** fut. Amíg a feladatok futnak, az **StdOut** és a **./logs** könyvtár a futtatási előzményekre van továbbítva. A Futtatás előrehaladását a naplók használatával figyelheti.

- **Feldolgozás után**a rendszer átmásolja a futtatáshoz tartozó **./outputs** könyvtárat a munkaterület futtatási előzményeibe, így elérheti ezeket az eredményeket.

A futó feladatok előrehaladását többféleképpen is megtekintheti. Ez az oktatóanyag egy Jupyter widgetet és egy `wait_for_completion` metódust használ.

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

A modell betanítása és monitorozása a háttérben zajlik. Várjon, amíg a modell befejezte a képzést, mielőtt további kódokat futtasson. `wait_for_completion` használata a modell betanításának befejeződése után:

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

A betanítási parancsfájl utolsó lépése a fájlt `outputs/sklearn_mnist_model.pkl` egy `outputs` nevű könyvtárban a fürt virtuális gépén, ahol a feladatot futtatják. a `outputs` egy speciális könyvtár, amely a címtárban található összes tartalmat automatikusan feltölti a munkaterületre. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. Így a modell fájlja már elérhető a munkaterületen is.

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
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Következő lépések

Ebben a Azure Machine Learning oktatóanyagban a Pythont használtuk a következő feladatokhoz:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Több modell betanítása egy távoli fürtön a népszerű scikit-Learn Machine learning-kódtár használatával
> * Tekintse át a képzés részleteit, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezésére az oktatóanyag-sorozat következő részében található utasítások használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
