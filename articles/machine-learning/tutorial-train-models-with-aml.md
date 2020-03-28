---
title: 'Képbesorolási oktatóanyag: Vonatmodellek'
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning használatával betaníthat egy lemezkép-besorolási modellt egy Python Jupyter-jegyzetfüzetben. Ez az oktatóanyag a második rész.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 8cf46db06a4a2f8fa86f97dab5a8477cf427c999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159075"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Oktatóanyag: Vonat képbesorolási modellek MNIST adatokkal és scikit-learn 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban egy gépi tanulási modell betanítása távoli számítási erőforrásokon. Az Azure Machine Learning betanítási és üzembe helyezési munkafolyamatát egy Python Jupyter-jegyzetfüzetben fogja használni.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regressziót képez az [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlet és a [scikit-learn](https://scikit-learn.org) használatával az Azure Machine Learning használatával. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden kép egy 28 x 28 képpontméretű, kézzel írt számjegy, amely nullától kilencig közelítő számot jelent. A cél egy többosztályos besoroló létrehozása, amely képes azonosítani az egyes képeken látható számjegyeket.

További információ a következő műveletek elmisztika a műveletről:

> [!div class="checklist"]
> * Állítsa be a fejlesztői környezetet.
> * Hozzáférés és vizsgálja meg az adatokat.
> * Egyszerű logisztikai regressziós modell betanítása egy távoli fürtön.
> * Tekintse át a betanítási eredményeket, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan válasszon ki egy modellt, és telepítse azt [a második részben ez a bemutató](tutorial-deploy-models-with-aml.md).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

>[!NOTE]
> Ebben a cikkben az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 1.0.65-ös verziójával teszteltük a kódot.

## <a name="prerequisites"></a>Előfeltételek

* Az [oktatóanyag befejezése: Első Azure ML-kísérlet létrehozásának első lépései:](tutorial-1st-experiment-sdk-setup.md)
    * Munkaterület létrehozása
    * Klónozza az oktatóanyagok jegyzetfüzetét a munkaterületi mappába.
    * Hozzon létre egy felhőalapú számítási példányt.

* A klónozott *oktatóanyagok/image-classification-mnist-data* mappában nyissa meg az *img-classification-part1-training.ipynb* notebookot. 


A bemutató és a mellékelt **utils.py** fájl is elérhető a [GitHubon,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) ha saját [helyi környezetben](how-to-configure-environment.md#local)szeretné használni. Futtassa `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` az oktatóanyag függőségei nek telepítéséhez.

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint a jegyzetfüzetben.  
>
> Váltson most a Jupyter-jegyzetfüzetre, ha a kód futtatásakor szeretne olvasni. 
> Ha egy jegyzetfüzetben egyetlen kódcellát szeretne futtatni, kattintson a kódcellára, és nyomja meg a **Shift+Enter billentyűkombinációt.** Vagy futtassa a teljes jegyzetfüzetet a Felső eszköztár **Összes futtatása** parancsával.

## <a name="set-up-your-development-environment"></a><a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Python-csomagok importálása.
* Csatlakozzon egy munkaterülethez, hogy a helyi számítógép kommunikálhasson a távoli erőforrásokkal.
* Hozzon létre egy kísérletet, hogy nyomon kövesse az összes fut.
* Hozzon létre egy távoli számítási célt a betanításhoz.

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. Jelenítse meg az Azure Machine Learning SDK verzióját is:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Csatlakozás munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()`beolvassa a **config.json** fájlt, és `ws`betölti a részleteket egy objektumnak nevezett objektumba:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterület több kísérletet is használhat:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Meglévő számítási cél létrehozása vagy csatolása

Az Azure Machine Learning Compute felügyelt szolgáltatás használatával az adatszakértők gépi tanulási modelleket taníthatnak be az Azure virtuális gépek fürtjein. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban az Azure Machine Learning Compute-t hozza létre betanítási környezetként. Az oktatóanyag későbbi részében elküldi a Python-kódot a virtuális gép futtatásához. 

Az alábbi kód létrehozza a számítási fürtöket, ha azok még nem léteznek a munkaterületen.

 **A számítási cél létrehozása körülbelül öt percet vesz igénybe.** Ha a számítási erőforrás már a munkaterületen van, a kód használja, és kihagyja a létrehozási folyamatot.

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

A modell betanítása előtt meg kell értenie a betanításához használt adatokat. Ebben a szakaszban megtanulhatja a következőket:

* Töltse le az MNIST adatkészletet.
* Néhány mintakép megjelenítése.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

Azure Open datasets segítségével a nyers MNIST adatfájlok beolvasása. [Az Azure Open Datasets](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) olyan nyilvános adatkészletek, amelyek segítségével forgatókönyv-specifikus funkciókat adhat a gépi tanulási megoldásokhoz a pontosabb modellek érdekében. Minden adatkészlet rendelkezik egy `MNIST` megfelelő osztály, ebben az esetben az adatok beolvasása különböző módon.

Ez a kód `FileDataset` objektumként olvassa be az adatokat, amely a alosztálya. `Dataset` A `FileDataset` hivatkozások egyetlen vagy több fájlt bármilyen formátumban az adattárak vagy nyilvános URL-eket. Az osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számításhoz az adatforrás helyére való hivatkozás létrehozásával. Emellett regisztrálja az adatkészletet a munkaterületre a betanítás során történő egyszerű lekérés érdekében.

Kövesse az [útmutatót, ha](how-to-create-register-datasets.md) többet szeretne megtudni az adatkészletekről és azok SDK-ban való használatáról.

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

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ehhez a `load_data` lépéshez egy `util.py` fájlban található függvény szükséges. Ezt a fájlt a mintamappa tartalmazza. Győződjön meg arról, hogy ugyanabba a mappába van helyezve, mint ez a jegyzetfüzet. A `load_data` függvény egyszerűen numpy tömbökbe elemzi a tömörített fájlokat.

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

![Véletlenszerű képminta](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

Ehhez a feladathoz elküldi a feladatot a korábban beállított távoli betanítási fürtön való futtatáshoz.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Becslő objektum létrehozása
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

+ A betanítási parancsfájl argumentumot olvas fel az adatokat tartalmazó könyvtár megkereséséhez. Amikor később elküldi a feladatot, az adattárban a következő argumentumra kell mutatnia: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ A betanítási parancsfájl a modellt egy **kimenetnevű**könyvtárba menti. Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. A modell elérése ebből a könyvtárból később az oktatóanyagban. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ A betanítási `utils.py` parancsfájl megköveteli, hogy a fájl megfelelően töltse be az adatkészletet. A következő kód `utils.py` `script_folder` másolja be, hogy a fájl elérhető legyen a betanítási parancsfájllal együtt a távoli erőforráson.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Becslő létrehozása

Egy [SKLearn estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objektum ot használ a futtatás elküldéséhez. A becslő létrehozásához határozza meg a következő elemeket az alábbi kód futtatásával:

* A becslőobjektum neve (`est`).
* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használja.
* A betanítási parancsfájl neve **train.py**.
* A betanítási szkript szükséges paraméterei.

Ebben az oktatóanyagban ez a cél az AmlCompute. A parancsfájlmappában lévő összes fájl feltöltésre kerül a fürtcsomópontokba futtatásra. A **data_folder** az adatkészlet használatára van beállítva. Először hozzon létre egy környezeti objektumot, amely meghatározza a képzéshez szükséges függőségeket. 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

Ezután hozza létre a becslőt a következő kóddal.

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

Futtassa a kísérletet a becslő objektum elküldésével:

```python
run = exp.submit(config=est)
run
```

Mivel a hívás aszinkron, a feladat **indításakor egy felkészülési** vagy **futási** állapotot ad vissza.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Összességében az első futás **körülbelül 10 percet**vesz igénybe. De a későbbi futtatások, mindaddig, amíg a parancsfájl-függőségek nem változnak, ugyanazt a lemezképet újra felhasználja. Tehát a konténer indítási ideje sokkal gyorsabb.

Mi történik várakozás közben:

- **Lemezkép létrehozása:** Létrejön egy Docker-rendszerkép, amely megfelel a becslés által megadott Python-környezetnek. A rendszerkép feltöltődik a munkaterületre. A képek létrehozása és feltöltése **körülbelül öt percet**vesz igénybe.

  Ez a szakasz minden Python-környezetben egyszer történik, mert a tároló gyorsítótárazva van a későbbi futtatások hoz. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. Ezek a naplók segítségével figyelheti a lemezkép létrehozásának folyamatát.

- **Méretezés**: Ha a távoli fürt a jelenleg rendelkezésre állónál több csomópontot igényel a futtatáshoz, a rendszer automatikusan további csomópontokat ad hozzá. Méretezés általában **körülbelül öt percet** vesz igénybe.

- **Futás:** Ebben a szakaszban a szükséges parancsfájlok és fájlok küldése a számítási cél. Ezután az adattárak csatlakoztatva vagy másolva lesznek. És akkor a **entry_script** fut. Afeladat futása közben a **stdout** és a **./logs** könyvtár a futtatási előzményekbe kerül. Ezek a naplók segítségével figyelheti a futtatás folyamatát.

- **Utófeldolgozás:** A futtatás **./outputkönyvtárát** a rendszer átmásolja a munkaterület futtatási előzményeibe, így elérheti ezeket az eredményeket.

A futó feladat előrehaladását többféleképpen ellenőrizheti. Ez az oktatóanyag Jupyter `wait_for_completion` widgetet és egy módszert használ.

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Nézze meg a haladást a távon egy [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). A futtatási beküldéshez hasonlóan a widget is aszinkron, és 10-15 másodpercenként élő frissítéseket biztosít, amíg a feladat befejeződik:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

A widget a következőhöz fog hasonlítani a képzés végén:

![Jegyzetfüzet widget](./media/tutorial-train-models-with-aml/widget.png)

Ha meg kell szakítania a futást, kövesse [az alábbi utasításokat.](https://aka.ms/aml-docs-cancel-run)

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. Várjon, amíg a modell befejezi a betanítást, mielőtt további kódot futtatna. A `wait_for_completion` modellbetanítás befejezésének megjelenítése:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel. Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```

A kimenet azt mutatja, hogy a távoli modell pontossága 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

A következő oktatóanyagban részletesebben is megismerheti ezt a modellt.

## <a name="register-model"></a>Modell regisztrálása

A betanítási parancsfájl utolsó `outputs/sklearn_mnist_model.pkl` lépése írta `outputs` a fájlt egy könyvtárban a virtuális gép a fürt, ahol a feladat fut. `outputs`egy speciális könyvtár, hogy a könyvtár összes tartalma automatikusan feltöltődik a munkaterületre. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. Így a modellfájl most már a munkaterületen is elérhető.

A futtatáshoz kapcsolódó fájlok a következők:

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, hogy Ön vagy más közreműködők később lekérdezhessék, megvizsgálhassák és telepíthessék ezt a modellt:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Csak az Azure Machine Learning Compute-fürtis törölhető. Az automatikus skálázás azonban be van kapcsolva, és a fürt minimális értéke nulla. Így ez az erőforrás nem jár további számítási díjakkal, ha nincs használatban:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>További lépések

Ebben az Azure Machine Learning-oktatóanyagban a Pythont használta a következő feladatokhoz:

> [!div class="checklist"]
> * Állítsa be a fejlesztői környezetet.
> * Hozzáférés és vizsgálja meg az adatokat.
> * Több modell betanítása egy távoli fürtön a népszerű scikit-learn gépi tanulási könyvtár használatával
> * Tekintse át a betanításrészleteit, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezésére az oktatóanyag-sorozat következő részében található utasítások használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
