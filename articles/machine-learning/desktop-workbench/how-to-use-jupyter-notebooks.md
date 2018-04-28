---
title: Jupyter notebookok használata az Azure Machine Learning-munkaterület |} Microsoft Docs
description: Kézikönyv a Jupyter Notebook funkciójával Azure Machine Learning-munkaterület
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 11d295734fd6d9180468709f0a25cca48b13df4d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Jupyter notebookok használata az Azure Machine Learning-munkaterület

Az Azure Machine Learning-munkaterület adatok interaktív tudományos kísérletezhet a Jupyter notebookok való integrálásán keresztül támogatja. A cikkből megtudhatja, hogyan teheti növeléséhez a sebesség és az adatok interaktív tudományos kísérletezhet minőségének hatékony használatára.

## <a name="prerequisites"></a>Előfeltételek
- [Azure Machine Learning fiókok létrehozása és telepítése az Azure Machine Learning-munkaterület](../service/quickstart-installation.md).
- Ismernie kell a [Jupyter Notebook](http://jupyter.org/). Ez a cikk nincs Jupyter használatának megtanulása kapcsolatban.

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook architektúrája
Magas szinten a Jupyter Notebook architektúra három összetevőket tartalmazza. Minden egyes különböző számítási környezetben futtathatja:

- **Ügyfél**: megkapja a felhasználói bevitel és a kimeneti megjelenítve jeleníti meg.
- **Kiszolgáló**: a notebook fájlok (.ipynb) üzemeltető webkiszolgáló.
- **Kernel**: futtatókörnyezet, mely a notebook végrehajtása a cellák történik.

További információkért lásd: a hivatalos [Jupyter dokumentáció](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). A következő ábra szemlélteti, hogyan az ügyfél, a kiszolgáló és a kernel architektúra van leképezve az Azure Machine Learning összetevői:

![Jupyter Notebook architektúrája](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Az Azure Machine Learning-munkaterület notebookok kernelek
Az Azure Machine Learning-munkaterület különböző kernelek elérheti futtatási konfiguráció definiálásával és számítási célt a `aml_config` a projekt mappájára. Új számítási cél hozzáadása kiállításával a `az ml computetarget attach` parancs megegyezik a hozzáadása egy új rendszermag.

>[!NOTE]
>Felülvizsgálati [konfigurálása Azure Machine Learning kísérletezhet szolgáltatás](experimentation-service-configuration.md) vonatkozó részletes információért futtassa a konfiguráció és célok számítási.

### <a name="kernel-naming-convention"></a>Kernel elnevezési egyezmény
Az Azure Machine Learning-munkaterület egyéni Jupyter kernelek állít elő. Ezek kernelek megnevezett  *\<projekt neve > \<futtassa a konfiguráció neve >*. Például, ha nevű futtatási konfigurációt _docker-python_ nevű projekt _myIris_, az Azure Machine Learning teszi elérhetővé a kernel nevű *myIris docker-python.* A futó kernel beállítása a Jupyter Notebook **Kernel** menüben, a a **módosítás kernel** almenü. A futó kernel neve jelenik meg a jobb szélén az egérrel a menüsoron.
 
Azure Machine Learning-munkaterület jelenleg a következő típusú kernelek.

### <a name="local-python-kernel"></a>Helyi Python kernel
A Python kernel végrehajtási támogatja a helyi gépen. Integrálva van az Azure Machine Learning futtatása előzmények támogatása. A kernel neve általában nem *my_project_name helyi.*

>[!NOTE]
>Ne használja a Python 3 kernel. Egy önálló kernel alapértelmezés szerint a Jupyter által biztosított, és nincs integrálva az Azure Machine Learning képességeit. Például a `%azureml` Jupyter magic függvények visszatérési "nem található" hibák. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python kernel a Docker (helyi vagy távoli)
A Python kernel futtat egy Docker-tároló a helyi számítógépen vagy távoli Linux virtuális gép (VM). A kernel neve általában nem *my_project docker.* A társított `docker.runconfig` fájl a `Framework` mező értéke `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark kernel a Docker (helyi vagy távoli)
A PySpark kernel végrehajtja a parancsfájlokat egy Docker-tároló, a helyi számítógépen vagy egy távoli Linux virtuális gépen belül jelenleg futó Spark környezetben. A kernel értéke általában *my_project docker.* A társított `docker.runconfig` fájl a `Framework` mező értéke `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Az Azure HDInsight-fürtök PySpark kernel
A kernel futtatja a távoli Azure HDInsight-fürthöz, a mellékelt számítási célként a projekthez. A kernel értéke általában *my_project my_hdi.* 

>[!IMPORTANT]
>Az a `.compute` a HDI fájlt a cél számítási, módosítania kell a `yarnDeployMode` mezőről `client` (az alapértelmezett érték `cluster`) használata a kernel. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Az Azure Machine Learning-munkaterület Jupyter kiszolgáló indítása
Az Azure Machine Learning-munkaterület, jegyzetfüzeteket keresztül hozzáférhet a **notebookok** fülre. A _zárolásának Iris_ mintaprojektet tartalmaz egy `iris.ipynb` minta notebookot.

![notebookok lap](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

A notebook Azure Machine Learning munkaterület megnyitásakor az megjelenik a saját dokumentum lapján **előkép mód**. Ez egy csak olvasható nézetű, egy futó Jupyter kiszolgáló és a kernel nem igényel.

![notebook előzetes verzió](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Válassza a **Start Notebook Server** gomb elindítja a Jupyter kiszolgálót, és a notebook vált **szerkesztési módot**. Az ismerős Jupyter Notebook felhasználói felületet beágyazott munkaterület jelenik meg. Most már beállíthat egy kernel a **Kernel** menü és a notebook interaktív munkamenet elindításához. 

>[!NOTE]
>A nem helyi kernelek is igénybe vehet egy perc múlva elindulni, ha először használja. Ön is végrehajthatja a `az ml experiment prepare` parancsot a parancssori ablakban a számítási cél előkészítheti a kernel sokkal gyorsabb elindítása után készen áll a testreszabásra a számítási cél.

![Szerkesztési módban](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Ez az egy teljes mértékben Jupyter Notebook felületet. Ebben az ablakban kivételével néhány fájlműveletek végezheti el a munkaterületet üzemeltető keresztül rendszeres notebook műveletek és a billentyűparancsok támogatottak **notebookok** lap és **fájl** lapon.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Jupyter kiszolgáló indítása a parancssorból
A notebook munkamenet is elindíthatja kiállításával `az ml notebook start` a parancssori ablakból:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Az alapértelmezett böngésző automatikusan megnyitja a Jupyter kiszolgálóval a projekt kezdőkönyvtár mutat. URL-cím és a parancssori ablakban jelenik meg a token használatával is nyissa meg a helyi más böngészőablakot. 

![Projekt irányítópultján](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Mostantól kiválaszthat egy `.ipynb` notebook fájlt, nyissa meg, állítsa be a rendszermag (Ha még nem lett beállítva), és az interaktív munkamenet elindításához.

![Projekt irányítópultján](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>-Parancsokkal magic kísérletek kezelése

Használhat [magic parancsok](http://ipython.readthedocs.io/en/stable/interactive/magics.html) a a notebook cellák nyomon követheti a futtatási előzményei, és mentse a kimeneti például modellek és adathalmazokat.

Nyomon követi az egyes notebook cella fut, használja a `%azureml history on` magic parancsot. Az előzmények bekapcsolása után minden egyes cella futtatásához futtatási előzményeit bejegyzés jelenik meg:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Futtatás cella követési kikapcsolásához használja a `%azureml history off` magic parancsot.

Használhatja a `%azureml upload` magic modell-és adatfájlok menteni a futtatható parancs. A mentett objektumok kimenetek a futtatási előzményei nézetben jelennek meg:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>A kimenetek mentse nevű *kimenete.*

## <a name="next-steps"></a>További lépések
- Jupyter Notebook használatával kapcsolatban lásd: a [Jupyter dokumentációs](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Ahhoz, hogy az Azure Machine Learning kísérletezhet végrehajtási környezet bemutatják, lásd: [konfigurálása Azure Machine Learning kísérletezhet szolgáltatás](experimentation-service-configuration.md).

