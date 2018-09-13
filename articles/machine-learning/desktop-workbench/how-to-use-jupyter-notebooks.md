---
title: Jupyter notebookok használata az Azure Machine Learning Workbench |} A Microsoft Docs
description: A Jupyter Notebook szolgáltatás az Azure Machine Learning Workbench használatával készült útmutató
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 712cdaa65487620b2f8af4a0ad57c01c24b9a965
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646143"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>A Jupyter notebookok használata az Azure Machine Learning Workbenchben

Az Azure Machine Learning Workbench interaktív adatelemzés Kísérletezési a Jupyter notebookok integrációjával támogatja. Ez a cikk ismerteti a sebesség és az interaktív data science Kísérletezési minőségének növelése érdekében ez a funkció hatékony felhasználása.

## <a name="prerequisites"></a>Előfeltételek
- [Azure Machine Learning-fiókok létrehozása és telepítése az Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Ismernie kell a [Jupyter Notebook](http://jupyter.org/). Ez a cikk nem kapcsolatos Jupyter használatának elsajátítását.

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook architektúra
Magas szinten a Jupyter Notebook architektúra tartalmaz három összetevőt. Minden egyes különböző számítási környezetben futtathatja:

- **Ügyfél**: megkapja a felhasználói bevitel és a renderelt kimenet jeleníti meg.
- **Kiszolgáló**: Webalkalmazás-kiszolgáló, amelyen a notebook (.ipynb fájlok).
- **Kernel**: futtatókörnyezet melyik végrehajtását a notebookot a cellák történik.

További információkért tekintse meg a hivatalos [Jupyter dokumentáció](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Az alábbi ábra bemutatja, hogyan képezi le az ügyfél, a kiszolgáló és a kernel architektúra az Azure Machine Learning-összetevőkkel:

![Jupyter Notebook architektúra](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Az Azure Machine Learning Workbench notebookok kernelekkel
Elérheti a különböző kernelekkel, az Azure Machine Learning Workbench futtatási konfiguráció definiálásával és számítási célt a `aml_config` mappát a projekt. Egy új számítási célnak hozzáadása kiállításával a `az ml computetarget attach` parancs megegyezik a hozzáadása egy új rendszermag.

>[!NOTE]
>Felülvizsgálat [konfigurálása az Azure Machine Learning-kísérletezés szolgáltatás](experimentation-service-configuration.md) további részleteiért futtatási konfigurációkat, és számítási céljainak.

### <a name="kernel-naming-convention"></a>Kernel elnevezési egyezmény
Az Azure Machine Learning Workbench egyéni Jupyter-kerneleket állít elő. A kernelekkel nevesített  *\<projekt neve > \<futtassa a konfiguráció neve >*. Például, ha egy futtatási konfigurációt nevű _docker-python_ nevű projekt _myIris_, az Azure Machine Learning megkönnyíti az elérhető kernel nevű *myIris docker-python.* A Jupyter Notebook állítsa be a futó kernel **Kernel** menüben, a a **módosítása kernel** almenü. A futó kernel neve jelenik meg a menüsor jobb szélén.
 
Jelenleg az Azure Machine Learning Workbench a következő típusú kernelekkel támogatja.

### <a name="local-python-kernel"></a>Helyi Python kernel
A Python-kernel végrehajtása támogatja a helyi gépen. Integrálva van az Azure Machine Learning futtatási előzmények támogatásával. A kernel neve általában nem *my_project_name helyi.*

>[!NOTE]
>Ne használja a Python 3 kernel. Egy önálló kernel alapértelmezés szerint a Jupyter által biztosított, és nincs integrálva az Azure Machine Learning-képességeket. Ha például a `%azureml` Jupyter magic funkciók "nem található" hibát ad vissza. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python-kernel docker (helyi vagy távoli)
A Python-kernel fut egy Docker-tároló vagy a helyi gépen, vagy egy távoli Linux rendszerű virtuális gépen (VM). A kernel neve általában nem *my_project docker.* A társított `docker.runconfig` fájl a `Framework` mező értéke `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark kernelt docker (helyi vagy távoli)
A PySpark kernel parancsfájlok végrehajtása egy Docker-tárolóban, vagy a helyi gépen, vagy egy távoli Linux rendszerű virtuális gépen futó Spark-környezetben. A kernel név általában *my_project docker.* A társított `docker.runconfig` fájl a `Framework` mező értéke `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>PySpark kernelt egy Azure HDInsight-fürtön
A kernel fut a távoli Azure HDInsight-fürtön a projekthez kapcsolódó számítási célként. A kernel név általában *my_project my_hdi.* 

>[!IMPORTANT]
>Az a `.compute` fájlt a HDI számítási célt, módosítania kell a `yarnDeployMode` mezőt `client` (az alapértelmezett érték `cluster`) a kernel használatára. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Az Azure Machine Learning Workbench Jupyter kiszolgáló indítása
Az Azure Machine Learning Workbench, notebookok használatával érheti el a **notebookok** fülre. A _Írisz osztályozása_ mintaprojekt tartalmaz egy `iris.ipynb` minta notebookot.

![Notebookok lap](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Amikor megnyit egy jegyzetfüzetet az Azure Machine Learning Workbenchben, a saját dokumentum lapján megjelenik **az előnézeti módot**. Ez a csak olvasható nézet, amely nem igényel egy futó Jupyter-kiszolgáló és a kernel.

![Notebook előzetes verzió](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Válassza a **Start Notebook Server** gomb elindítja a Jupyter-kiszolgálót, és a kapcsolók a notebookot az **szerkesztési módot**. A jól ismert Jupyter Notebook felhasználói felület megjelenik a Workbench beágyazott. Most már beállíthatja a kernel a **Kernel** menüben, és az interaktív jegyzetfüzetek munkamenet elindításához. 

>[!NOTE]
>A nem helyi kernelekkel egy-két elindítani, ha először használ percig is eltarthat. Hajthat végre a `az ml experiment prepare` paranccsal, készítse elő a számítási célnak a kernel sokkal gyorsabban indul, miután a számítási célnak készen áll arra, hogy a parancssori felület ablakából.

![Szerkesztési módban](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Ez a szolgáltatás teljes mértékben interaktív Jupyter Notebook nyújt lehetőséget. Ezt az ablakot, bizonyos a Workbench-n keresztül is elvégezhető fájlműveletek kivételével minden rendszeres jegyzetfüzet-műveletet és billentyűparancsok támogatottak **notebookok** fülre, és **fájl** fülre.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Jupyter kiszolgáló indítása a parancssorból
A notebook munkamenet kiállításával is elindíthatja `az ml notebook start` a parancssori ablakban:
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
Az alapértelmezett böngésző automatikusan megnyitja a projektet kezdőkönyvtár mutató Jupyter-kiszolgálóval. Az URL-CÍMÉT és a parancssori felület ablakában jelenik meg a token használatával is nyissa meg a helyi más böngészőablakot. 

![Projekt-irányítópult](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Most kiválaszthatja egy `.ipynb` notebook fájlt, nyissa meg, állítsa be a kernelbe (Ha még nem állították), és az interaktív munkamenet elindításához.

![Projekt-irányítópult](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Kísérletek kezeléséhez magic parancsok használatával

Használhat [parancsok magic](http://ipython.readthedocs.io/en/stable/interactive/magics.html) a a notebook cellák nyomon követheti a futtatási előzményeket, és mentse a kimenetek például modellek vagy adatkészletek.

Egyes notebook cella futtatások nyomon követéséhez használhatja a `%azureml history on` magic parancsot. Az előzmények bekapcsolása után minden cella futtassa a futtatási előzményekben bejegyzés jelenik meg:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Kapcsolja ki a futtatni cella nyomkövetést, használja a `%azureml history off` magic parancsot.

Használhatja a `%azureml upload` magic menteni a modell és az adatfájlokat a futtatható parancs. A mentett objektumok kimenetként a futtatási előzmények nézetben jelennek meg:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>A kimenetek kell menteni nevű *adja vissza.*

## <a name="next-steps"></a>További lépések
- Jupyter Notebook használatával kapcsolatban lásd: a [Jupyter dokumentációs](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Az Azure Machine Learning-Kísérletezési végrehajtási környezetet egy mélyrehatóbb ismereteket szerezhet, lásd: [konfigurálása az Azure Machine Learning-kísérletezés szolgáltatás](experimentation-service-configuration.md).

