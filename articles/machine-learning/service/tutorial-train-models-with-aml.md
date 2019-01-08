---
title: 'Kép besorolási oktatóanyag: Modellek betanítása'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Machine Learning szolgáltatás képbesorolási modell betanítására a scikit-learn alkalmazásával egy Python Jupyter-notebookban. Ez az oktatóanyag egy kétrészes sorozat első része.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5008aebfea8fc284b1b85797f0523bc4fb11c7bd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075940"
---
# <a name="tutorial-train-an-image-classification-model-with-azure-machine-learning-service"></a>Oktatóanyag: Betanításához egy kép osztályozási modell Azure Machine Learning szolgáltatással

Az oktatóanyag során egy gépi tanulási modellt fog betanítani helyi és távoli számítási erőforrások használatával. Python Jupyter notebook az Azure Machine Learning szolgáltatás a képzés és az üzembe helyezést megvalósító munkafolyamat használja. Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ebben az oktatóanyagban egy egyszerű logisztikai regressziós betanítja használatával a [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlet és [scikit-további](https://scikit-learn.org) Azure Machine Learning szolgáltatással. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden egyes képe 28 x 28 képpont, egy számot jelölő nine nulláról kézírásos számjegy. A cél, azonosíthatja a számokat a megadott lemezkép multiclass besorolás létrehozása jelöli. 

További információ a következő műveleteket:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * Az adatok vizsgálatát, és hozzáférést.
> * Egy egyszerű logisztikai regressziós betanításához helyileg a népszerű scikit használatával – ismerje meg, gépi tanulási kódtár. 
> * Egy távoli fürtön több modelleket taníthat be.
> * Tekintse át a képzési eredményeket, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan modell kiválasztása és hogyan telepítheti a [második rész a jelen oktatóanyag](tutorial-deploy-models-with-aml.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK-val 1.0.2-es verzióját lett tesztelve.

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb) is elérhető. A `tutorials/img-classification-part1-training.ipynb` notebook az Azure Notebooks szolgáltatásban vagy a saját Jupyter-notebookkiszolgálóján futtatható.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]


## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő az alábbi műveleteket tartalmazza:

* Python-csomagok importálásához.
* Csatlakozhat egy munkaterületet, hogy a helyi számítógép képes kommunikálni a távoli erőforrásokhoz.
* Minden Futtatás nyomon fülre egy kísérlet létrehozásához.
* Hozzon létre egy távoli számítási célnak képzéshez használatára.

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. Az Azure Machine Learning-SDK verziója is tartalmazzák:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Csatlakozás a munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()` beolvassa a fájlt **config.json** és betölti a részletek nevű objektum `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Munkaterület több kísérletet is van: 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-amlcompute"></a>Hozzon létre vagy csatlakoztasson egy már létező AMlCompute

Az Azure Machine Learning Compute (AmlCompute), egy felügyelt szolgáltatás, az adatszakértők betaníthatja machine learning-modellek Azure-beli virtuális gépek fürtjein. Ilyenek például a virtuális gépek a GPU-támogatással. Ebben az oktatóanyagban létrehoz AmlCompute a képzési környezet. Ez a kód létrehozza az Ön számára a számítási fürtök, ha azok még nem léteznek a munkaterületén.

 **A számítási létrehozása nagyjából öt percet vesz igénybe.** Ha a számítási már a munkaterületen, ez a kód használja ezt a szolgáltatást, és kihagyja a létrehozási folyamat:


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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use the 'status' property    
    print(compute_target.status.serialize())
```

Most már rendelkezésre állnak a modell felhőben történő betanításához szükséges csomagok és számítási erőforrások. 

## <a name="explore-data"></a>Adatok megismerése

A modell betanításához előtt kell megérteni az adatokat, amely használja azt. Is ki kell másolnia az adatokat a felhőbe. Ezután a felhőkörnyezet képzési is elérhetők. Ebben a szakaszban megismerheti, hogyan végezze el a következő műveleteket:

* Töltse le a MNIST adatkészlet.
* Néhány példa képek megjelenítéséhez.
* Adatok feltöltése a felhőbe.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

Töltse le az MNIST-adathalmazt, és mentse a fájlokat egy helyi `data` könyvtárba. Képek és címkék az betanításra és tesztelésre le:


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Mintaképek megjelenítése

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ez a lépés szükséges egy `load_data` -függvény, amely tartalmaz egy `util.py` fájlt. Ezt a fájlt a mintamappa tartalmazza. Ellenőrizze, hogy ez a jegyzetfüzet azonos mappába helyezi. A `load_data` függvény egyszerűen elemzi a tömörített fájlok numpy tömbök be:



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the training set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
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

![a képek véletlenszerűen vett minta](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

### <a name="upload-data-to-the-cloud"></a>Adatok feltöltése a felhőbe

Most elérhetővé az adatok távolról az adatokat a helyi gépen feltöltése az Azure-bA. Ezután is hozzáférhet a távoli betanításhoz. Az adattár egy kényelmes szerkezet feltölteni, vagy töltse le az adatokat a munkaterület társítva. Is használhatja azt a távoli számítási célnak a. Egy Azure Blob storage-fiók alapját.

A MNIST fájlok feltöltése egy könyvtárba nevű `mnist` az adattár gyökérkönyvtárában található:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Most már a modell betanításának megkezdéséhez szükséges összes előfeltétellel rendelkezik. 

## <a name="train-a-local-model"></a>Egy helyi modell betanítása

Egy egyszerű logisztikai regressziós modell betanításához scikit használatával – ismerje meg, helyileg.

**Képzési helyileg is igénybe vehet, egy-két perc** a számítógép-konfigurációtól függően:

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

Ezután előrejelzéseket a teszt set használatával, és pontossága kiszámítása: 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

Megjelenik a helyi modell pontossága:

`0.9202`

Mindössze néhány sornyi kóddal rendelkezik 92 százalékos pontosságát.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

Most kiterjesztheti ezt az egyszerű modellt egy más regularizációs arányú modell létrehozásával. Ennek során, a távoli erőforrásokhoz a modell betanítását.  

E gyakorlat céljából küldje el a feladatot a korábban beállított távoli betanítási fürtnek. Olyan feladatot küld el, tegye a következőket:
* Hozzon létre egy könyvtárat.
* Hozzon létre egy tanítási szkriptet.
* Hozzon létre egy estimator objektumot.
* A feladat elküldéséhez.

### <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy könyvtárat a számítógépen a szükséges kódot továbbítására a távoli erőforrás:

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Betanító szkript létrehozása

A feladatnak a fürtre való elküldéséhez először hozzon létre egy betanító szkriptet. Futtassa a következő kódot a tanítási szkriptet nevű létrehozása `train.py` hozott létre a címtárban. Ez a képzés ad hozzá a regularizációs arány az algoritmus betanítása. Ezért küld egy némileg eltérő modellt, mint a helyi verziója:

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

data_folder = os.path.join(args.data_folder, 'mnist')
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
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
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

+ Képzési beolvassa az adatokat tartalmazó könyvtár található argumentumként. Ha később a feladat elküldéséhez, mutasson az adattárral ehhez az argumentumhoz: `parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')`.

+ A tanítási szkriptet menti a modell egy könyvtárba nevű **kimenete**: <br/>
`joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`.<br/>
Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. Az oktatóanyag későbbi részében ezen könyvtár eléréséhez a modellt.
A rendszer a betanítási szkript alapján hivatkozik a `utils.py` fájlra az adathalmaz megfelelő betöltéséhez. Másolja a parancsfájl mappába, ez a szkript, úgy, hogy a tanítási szkriptet a távoli erőforrás együtt is elérhető.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Becslő létrehozása

A futtatás elküldése egy becslőobjektummal történik. Hozzon létre a estimator határozzák meg ezeket a következő kód futtatásával:

* A estimator objektum neve `est`.
* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából. 
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használ.
* A képzési parancsfájl nevét **train.py**.
* A tanítási szkriptet a szükséges paramétereket. 
* Képzési szükséges Python-csomagokat.

Ebben az oktatóanyagban ez a cél AmlCompute. A parancsfájl mappában lévő összes fájlt a rendszer feltölti a fürtcsomópontokon, Futtatás esetében. A **data_folder** az adattár használatára van beállítva `ds.as_mount()`:

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Feladat küldése a fürtnek

Futtassa a kísérletet a estimator objektum elküldésével:

```python
run = exp.submit(config=est)
run
```

Mivel a hívás aszinkron, akkor adja vissza egy **előkészítése** vagy **futó** állapot, amint az a feladat elindult.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Az összes, az első futtatása vesz igénybe **nagyjából 10 percet**. De a későbbi futtatások mindaddig, amíg a parancsfájl-függőségek ne módosítsa, ugyanazt a lemezképet újrahasznosított. Így a tároló indítási ideje sokkal gyorsabb.

Mi történik, várakozás közben:

- **Rendszerkép létrehozása**: Docker-rendszerkép létrehozása, amely megfelel a estimator által megadott Python-környezetet. A rendszerkép feltöltődik a munkaterületre. Rendszerkép létrehozása és feltöltése veszi **nagyjából öt perc alatt**. 

  Ebben a szakaszban egyszer történik a Python-környezeteket, mert a rendszer gyorsítótárazza a tároló későbbi futtatások. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. Ezek a naplók segítségével figyelheti a lemezkép létrehozásának folyamata.

- **Skálázás**: Ha a távoli fürt csomópontjait a Futtatás mint a jelenleg rendelkezésre álló lehetőségek igényel, további csomópontok hozzáadása automatikusan történik. Általában méretezést veszi **nagyjából öt perc alatt.**

- **Futó**: Ebben a szakaszban a szükséges szkriptek és a fájlokat a számítási célnak kell küldeni. Ezután adattárainak csatlakoztatva van, vagy másolja. Majd a **entry_script** futtatása. A feladat futása közben **stdout** és a **. / naplók** könyvtár a rendszer streamként továbbítja a futtatási előzményekben. Ezek a naplók segítségével figyelheti a Futtatás folyamatban van.

- **Utófeldolgozási**: A **. / kimenete** a Futtatás directory át van másolva a munkaterületen, a futtatási előzmények tud elérni ezeket az eredményeket.


Ellenőrizheti, hogy többféle módon egy futó feladat előrehaladását. Ebben az oktatóanyagban egy Jupyter widget és a egy `wait_for_completion` metódust. 

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Kövesse nyomon a futtatás állapotát Jupyter-vezérlővel. A futtatási elküldése, például a widget aszinkron és élő frissíti a feladat befejezéséig 10 – 15 másodpercenként:


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Ez továbbra is a pillanatkép el a betanítási végén látható widget:

![notebook widget](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. Várjon, amíg a modell betanítási befejeződött, további kód futtatása előtt. Használat `wait_for_completion` megjelenítéséhez, miután befejezte a modell betanítása: 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel. Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```
A kimenet mutatja, hogy a távoli modellnek valamivel nagyobb, mint a helyi modell pontossága miatt a regularizációs arány betanítás során is:  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

A következő oktatóanyagban ismerje meg részletesebben ebben a modellben.

## <a name="register-model"></a>Modell regisztrálása

Az utolsó lépés a tanítási szkriptet okkal készítette el a fájlt `outputs/sklearn_mnist_model.pkl` nevű `outputs` a fürt, amelyen a feladat fut a virtuális gépen. `outputs` Ebben a könyvtárban található összes tartalmat automatikusan bekerül a munkaterülethez, az egy speciális könyvtár. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. Ezért a modellfájl már is elérhető a munkaterületén.

Társított futtató fájlt láthatja:

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, így a, vagy más közreműködők is később lekérdezésére, vizsgálja meg, és ez a modell üzembe helyezése:

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Csak az Azure Machine Learning COMPUTE számítási fürt is törölheti. Azonban az automatikus méretezés be van kapcsolva, és a fürt minimális értéke nulla. Így az adott erőforrás nem számítunk fel további számítási díjakat, amikor nincs használatban:


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning szolgáltatás oktatóanyagban használt Python a következő feladatokat:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * Az adatok vizsgálatát, és hozzáférést.
> * Egy egyszerű logisztikai regressziós betanításához helyileg a népszerű scikit használatával – ismerje meg, gépi tanulási kódtár.
> * Egy távoli fürtön több modelleket taníthat be.
> * Képzési adatokat, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezése az oktatóanyag-sorozat következő részére, utasításait használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
