---
title: 'Kép besorolási oktatóanyag: Modellek betanítása'
titleSuffix: Azure Machine Learning service
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Machine Learning szolgáltatás képbesorolási modell betanítására a scikit-learn alkalmazásával egy Python Jupyter-notebookban. Ez az oktatóanyag egy kétrészes sorozat első része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 01/28/2019
ms.custom: seodec18
ms.openlocfilehash: e7617aec2739daa4f84bcecab060ae0f8e28fabe
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361591"
---
# <a name="tutorial-train-an-image-classification-model-with-azure-machine-learning-service"></a>Oktatóanyag: Betanításához egy kép osztályozási modell Azure Machine Learning szolgáltatással

Ebben az oktatóanyagban egy gépi tanulási modellt a távoli számítási erőforrások betanításához. Az Azure Machine Learning szolgáltatás (előzetes verzió) betanítási és üzembehelyezési munkafolyamatát fogja használni egy Python Jupyter-notebookban.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ebben az oktatóanyagban egy egyszerű logisztikai regressziós betanítja használatával a [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlet és [scikit-további](https://scikit-learn.org) Azure Machine Learning szolgáltatással. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden egyes képe 28 x 28 képpont, egy számot jelölő nine nulláról kézírásos számjegy. A cél, azonosíthatja a számokat a megadott lemezkép multiclass besorolás létrehozása jelöli. 

További információ a következő műveleteket:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * Az adatok vizsgálatát, és hozzáférést.
> * Egy távoli fürtön egy egyszerű logisztikai regressziós modell betanításához.
> * Tekintse át a képzési eredményeket, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan modell kiválasztása és hogyan telepítheti a [második rész a jelen oktatóanyag](tutorial-deploy-models-with-aml.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

>[!NOTE]
> Ebben a cikkben kód az Azure Machine Learning SDK verziója 1.0.8 lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

Ugrás a [a fejlesztési környezet beállítása](#start) olvassa végig a notebook lépéseket, vagy használja az alábbi utasításokat a notebook beszerzése és az Azure notebookok vagy a saját notebook server futtatásához.  A jegyzetfüzet futtatásához szüksége lesz:

* Egy Python 3.6-os notebook kiszolgálót a következőkkel:
    * Az Azure Machine Learning SDK a Pythonhoz
    * `matplotlib` és `scikit-learn`
* Az oktatóanyag notebook és a fájl utils.py
* A machine learning-munkaterület 
* A konfigurációs fájl ugyanabban a könyvtárban, a notebookot a munkaterület számára 

Ezekről az előfeltételekről le az alábbi szakaszok egyikét.
 
* Használat [Azure notebookok](#azure) 
* Használat [saját notebook server](#server)

### <a name="azure"></a>Az Azure notebookok használata: Ingyenes Jupyter notebookok a felhőben

Nem kell mást Azure notebookok használatának megkezdéséhez. A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) már telepítve és konfigurálva van az Ön számára [Azure notebookok](https://notebooks.azure.com/). A telepítés és a jövőbeli frissítések automatikusan felügyelt Azure-szolgáltatások használatával.

Miután elvégezte az alábbi lépéseket, futtassa a **oktatóanyagok/img – osztályozás – 1-training.ipynb** a notebook a **első lépések** projekt.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


### <a name="server"></a>A saját Jupyter notebook server használata

Ezek a lépések használatával hozzon létre egy helyi Jupyter Notebook kiszolgálót a számítógépen. 

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Miután végrehajtotta a lépéseket, futtassa a **oktatóanyagok/img – osztályozás – 1-training.ipynb** notebookot.

## <a name="start"></a>A fejlesztési környezet beállítása

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
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

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

### <a name="create-or-attach-an-existing-compute-resource"></a>Hozzon létre vagy egy meglévő számítási erőforrás csatolása

Az Azure Machine Learning COMPUTE számítási, egy felügyelt szolgáltatás, az adatszakértők betaníthatja machine learning-modellek Azure-beli virtuális gépek fürtjein. Ilyenek például a virtuális gépek a GPU-támogatással. Ebben az oktatóanyagban hoz létre az Azure Machine Learning Compute a képzési környezet. Az alábbi kódot az Ön hozza létre a számítási fürtök, ha azok még nem léteznek a munkaterületén.

 **A számítási létrehozása nagyjából öt percet vesz igénybe.** Ha a számítási már a munkaterületen, a kódot használja ezt a szolgáltatást, és kihagyja a létrehozási folyamat.


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
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
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
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```
Ehhez hasonló kimenetet fog látni: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Mintaképek megjelenítése

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ez a lépés szükséges egy `load_data` -függvény, amely tartalmaz egy `util.py` fájlt. Ezt a fájlt a mintamappa tartalmazza. Ellenőrizze, hogy ez a jegyzetfüzet azonos mappába helyezi. A `load_data` függvény egyszerűen elemzi a tömörített fájlok numpy tömbök be:



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
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

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
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
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
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

Ebben az oktatóanyagban ez a cél AmlCompute. A parancsfájl mappában lévő összes fájlt a rendszer feltölti a fürtcsomópontokon, Futtatás esetében. A **data_folder** az adattár használatára van beállítva `ds.path('mnist').as_mount()`:

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
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

Ha a Futtatás megszakítása van szüksége, kövesse [ezek az utasítások](https://aka.ms/aml-docs-cancel-run).

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
A kimenet mutatja, hogy a távoli modellnek 0.9204 pontosságát:

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
> * Egy távoli fürtön a népszerű scikit használatával több modelleket taníthat be – ismerje meg, gépi tanulási kódtár
> * Képzési adatokat, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezése az oktatóanyag-sorozat következő részére, utasításait használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
