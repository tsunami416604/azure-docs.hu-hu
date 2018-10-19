---
title: 'Oktatóanyag: Képbesorolási modell betanítása az Azure Machine Learning szolgáltatással'
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure Machine Learning szolgáltatás képbesorolási modell betanítására a scikit-learn alkalmazásával egy Python Jupyter-notebookban. Ez az oktatóanyag egy kétrészes sorozat első része.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 7110d8df5ee837829f97fec8ab26d929689e3436
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785138"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning-service"></a>1. oktatóanyag: Képbesorolási modell betanítása az Azure Machine Learning szolgáltatással

Az oktatóanyag során egy gépi tanulási modellt fog betanítani helyi és távoli számítási erőforrások használatával. Az Azure Machine Learning szolgáltatás (előzetes verzió) betanítási és üzembehelyezési munkafolyamatát fogja használni egy Python Jupyter-notebookban.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regresszió betanítását foglalja magában, a [MNIST](http://yann.lecun.com/exdb/mnist/)-adathalmaz, a [scikit-learn](http://scikit-learn.org) és az Azure Machine Learning szolgáltatás használatával.  Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Mindegyik képe egy 28x28 képpontos, 0 és 9 közötti, kézzel írt számjegy. A cél egy többosztályos besoroló létrehozása, amely képes azonosítani az egyes képeken látható számjegyeket. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * Az adatok elérése és vizsgálata
> * Egy egyszerű logisztikai regresszió helyi betanítása a népszerű scikit-learn Machine Learning-kódtárral 
> * Több modell betanítása távoli fürtön
> * A betanítás eredményeinek áttekintése és a legjobb modell regisztrálása

A modellek kiválasztásával és üzembe helyezésével később, az [oktatóanyag második részében](tutorial-deploy-models-with-aml.md) fog megismerkedni. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="get-the-notebook"></a>A notebook beszerzése

Az Ön kényelme érdekében ez az oktatóanyag [Jupyter-notebookként](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb) is elérhető. A `01.train-models.ipynb` notebook az Azure Notebooks szolgáltatásban vagy a saját Jupyter-notebookkiszolgálóján futtatható.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható.  A beállítás a következőket tartalmazza:

* Python-csomagok importálása
* Munkaterülethez való csatlakozás a helyi számítógép és a távoli erőforrások közötti kommunikáció engedélyezéséhez
* Kísérlet létrehozása az összes futtatás nyomon követéséhez
* Távoli, betanításhoz használt számítási cél létrehozása

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. Jelenítse meg az Azure Machine Learning SDK verzióját is.

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

### <a name="connect-to-workspace"></a>Csatlakozás a munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. A `Workspace.from_config()` beolvassa a **config.json** fájlt, és betölti a részleteket a `ws` nevű objektumba.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterületen több kísérlet is létrehozható. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Távoli számítógépcél létrehozása

Az Azure Batch AI egy felügyelt szolgáltatás, amely lehetővé teszi az adatelemzők számára, hogy gépi tanulási modelleket tanítsanak be az Azure-beli virtuális gépek fürtjein, beleértve a GPU-támogatással rendelkező virtuális gépeket.  Ebben az oktatóanyagban egy Azure Batch AI-fürtöt fog létrehozni betanítási környezetként. Ez a kód létrehoz Önnek egy fürtöt, ha még nem létezik a munkaterületén. 

 **A fürt létrehozása körülbelül 5 percet vesz igénybe.** Ha a fürt már szerepel a munkaterületben, akkor a létrehozási folyamat kimarad, és a kód a meglévő fürtöt használja.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if type(compute_target) is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Most már rendelkezésre állnak a modell felhőben történő betanításához szükséges csomagok és számítási erőforrások. 

## <a name="explore-data"></a>Adatok megismerése

A modell betanítása előtt értelmeznie kell a betanításhoz használni kívánt adatokat.  Ezenkívül a felhőbe is be kell másolnia az adatokat, hogy hozzáférhetők legyenek a felhőalapú betanítási környezet számára.  Ebben a szakaszban megtanulhatja a következőket:

* Az MNIST-adathalmaz letöltése
* Mintaképek megjelenítése
* Adatok feltöltése a felhőbe

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

Töltse le az MNIST-adathalmazt, és mentse a fájlokat egy helyi `data` könyvtárba.  A rendszer a betanításhoz és a teszteléshez is használható képeket és címkéket tölt le.  


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

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Vegye figyelembe, hogy ehhez a lépéshez egy `load_data` függvény szükséges, amely az `util.py` fájlban található. Ezt a fájlt a mintamappa tartalmazza. Ügyeljen rá, hogy ezzel a notebookkal egy mappába helyezze. A `load_data` függvény numpy-tömbökbe elemzi a tömörített fájlokat.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

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

![véletlenszerű képminta](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

### <a name="upload-data-to-the-cloud"></a>Adatok feltöltése a felhőbe

Most tegye távolról elérhetővé az adatokat azáltal, hogy a helyi számítógépéről feltölti őket az Azure-ba, hogy elérhetők legyenek a távoli betanításhoz. Az adattár a munkaterületéhez társított, hasznos szerkezet, amellyel adatokat tölthet fel vagy le, illetve a távoli számítási céljairól kezelheti őket. Az Azure Blob Storage-fiókra épül.

Az MNIST-fájlokat az adattár gyökérkönyvtárában található, `mnist` nevű könyvtárba tölti fel a rendszer.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Most már a modell betanításának megkezdéséhez szükséges összes előfeltétellel rendelkezik. 

## <a name="train-a-model-locally"></a>Modell helyi betanítása

Ebben a szakaszban egy, a scikit-learnből származó egyszerű logisztikai regressziós modellt taníthat be helyileg.

**A helyi betanítás egy-két percig tarthat** a számítógép konfigurációjától függően.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

Ezután végezzen előrejelzéseket a tesztkészlettel, és számítsa ki a pontosságot. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

Megjelenik a helyi modell pontossága:

`0.9202`

Mindössze néhány sor kóddal 92%-os pontosságot ért el.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

Most kiterjesztheti ezt az egyszerű modellt egy más regularizációs arányú modell létrehozásával. Ez alkalommal egy távoli erőforráson fogja betanítani a modellt.  

E gyakorlat céljából küldje el a feladatot a korábban beállított távoli betanítási fürtnek.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Becslő létrehozása
* Feladat küldése 

### <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy kódtárat, amellyel eljuttathatja a szükséges kódot a számítógépéről a távoli erőforrásra.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Betanító szkript létrehozása

A feladatnak a fürtre való elküldéséhez először hozzon létre egy betanító szkriptet. A `train.py` nevű betanító szkript imént létrehozott könyvtárban történő létrehozásához futtassa a következő kódot. Ez a betanítás regularizációs arányt ad hozzá a betanító algoritmushoz, így a helyi verziótól kismértékben eltérő modellt hoz létre.

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
run = Run.get_submitted_run()

print('Train a logistic regression model with regularizaion rate of', args.reg)
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

+ A betanító szkript beolvas egy argumentumot az adatokat tartalmazó könyvtár megtalálásához.  Amikor később elküldi a feladatot, az adattárban a következő argumentumra kell mutatnia: `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`

    
+ A betanítási szkript egy outputs (kimenetek) nevű könyvtárba menti a modellt. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. Az oktatóanyag későbbi részében ebből a könyvtárból férhet majd hozzá a modelljéhez.

A rendszer a betanítási szkript alapján hivatkozik a `utils.py` fájlra az adathalmaz megfelelő betöltéséhez.  Másolja ezt a szkriptet a szkriptmappába, hogy a betanítási szkripttel együtt elérhető legyen a távoli erőforráson.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Becslő létrehozása

A futtatás elküldése egy becslőobjektummal történik.  A becslő létrehozásához definiálja a következőket az alábbi kód futtatásával:

* A becslőobjektum neve (`est`)
* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából. 
* A számítási cél.  Ebben az esetben a létrehozott Batch AI-fürtöt fogja használni.
* A betanítási szkript neve (train.py)
* A betanítási szkript szükséges paraméterei 
* A betanításhoz szükséges Python-csomagok

Az oktatóanyagban ez a cél a Batch AI-fürt. A projektkönyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából. A data_folder érték az adattárra van állítva (`ds.as_mount()`).

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

Futtassa a kísérletet a becslőobjektum elküldésével.

```python
run = exp.submit(config=est)
run
```

Mivel a hívás aszinkron, **Preparing** (előkészítés) vagy **Running** (futtatás) állapotot ad vissza, amint a feladat elindult.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Az első futtatás összesen **nagyjából 10 percig tart**. A későbbi futtatások esetében azonban a rendszer ugyanazt a képet használja újból, amíg a szkript függőségei nem változnak, ezért a tároló sokkal gyorsabban indul el.

Ez történik, amíg várakozik:

- **Rendszerkép létrehozása**: A Docker-rendszerkép létrehozása a becslő által meghatározott Python-környezetnek megfelelően történik. A rendszerkép feltöltődik a munkaterületre. A rendszerkép létrehozása és feltöltése **körülbelül 5 percet** vesz igénybe. 

  Ez a lépés egyszer meg végbe minden Python-környezet esetében, mert a tároló megmarad a gyorsítótárban a későbbi futtatásoknál való felhasználáshoz.  A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. A kép létrehozásának folyamata ezekkel a naplókkal monitorozható.

- **Skálázás**: Ha a távoli fürtnek a jelenleg elérhetőnél több csomópontra van szüksége a futtatás végrehajtásához, a rendszer automatikusan hozzáad új csomópontokat. A skálázás **körülbelül 5 percet** vesz igénybe.

- **Futtatás**: Ebben a szakaszban a rendszer elküldi a szükséges szkriptek és a fájlokat a számítási célnak, majd csatlakoztatja/másolja az adattárakat, végül pedig futtatja az entry_script szkriptet. Amíg a feladat fut, a rendszer a futási előzményekbe streameli az stdoutot és a ./logs könyvtárat. A futtatás folyamata ezekkel a naplókkal monitorozható.

- **Utófeldolgozás**: A rendszer a futtatás ./outputs könyvtárát a munkaterület futási előzményeibe másolja be, hogy Ön hozzáférhessen ezekhez az eredményekhez.


A futó feladatok állapota többféleképpen ellenőrizhető. Ez az oktatóanyag egy Jupyter-vezérlőt, valamint egy `wait_for_completion` metódust használ erre. 

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Kövesse nyomon a futtatás állapotát Jupyter-vezérlővel.  A futtatás elküldéséhez hasonlóan a vezérlő aszinkron módon működik, és 10-15 másodpercenként élő állapotfrissítést biztosít a feladat befejeződéséig.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Itt látható egy pillanatfelvétel a vezérlőről, amely a betanítás végén jelenik meg:

![notebook vezérlő](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. A további parancsok futtatása előtt várja meg, amíg a modell betanítása befejeződik. A `wait_for_completion` használatával jelenítheti meg, ha a modell betanítása befejeződött. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel.  Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```
A kimenet azt mutatja, hogy a távoli modell pontossága valamivel nagyobb, mint a helyi modellé, a betanítás során hozzáadott regularizációs aránynak köszönhetően.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Az üzembe helyezési oktatóanyagban részletesebben megismerheti ezt a modellt.

## <a name="register-model"></a>Modell regisztrálása

A betanítási szkript utolsó lépése az `outputs/sklearn_mnist_model.pkl` fájlt egy `outputs` nevű könyvtárba írta annak a fürtnek a virtuális gépén, ahol a feladat végre lett hajtva. Az `outputs` egy különleges könyvtár, mert a teljes tartalma automatikusan feltöltődik a munkaterületére.  Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. A modellfájl így már a munkaterületén is elérhető,

és láthatja a futtatáshoz tartozó fájlokat.

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, hogy később Ön (vagy más közreműködők) lekérdezhessék, megvizsgálhassák és üzembe helyezhessék ezt a modellt.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Törölheti csak az Azure Managed Compute-fürtöt. Mivel azonban az automatikus méretezés be van kapcsolva, és a fürt minimális értéke 0, ezután a konkrét erőforrás után nem kell további számítási díjakat fizetnie, amikor nincs használatban.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>További lépések

Ebben az Azure Machine Learning szolgáltatáshoz kapcsolódó oktatóanyagban a következőkhöz használta a Pythont:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása
> * Az adatok elérése és vizsgálata
> * Egy egyszerű logisztikai regresszió helyi betanítása a népszerű scikit-learn Machine Learning-kódtárral
> * Több modell betanítása távoli fürtön
> * A betanítás részleteinek áttekintése és a legjobb modell regisztrálása

Készen áll arra, hogy az oktatóanyag-sorozat következő részében található utasítások alapján üzembe helyezze a regisztrált modellt:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
