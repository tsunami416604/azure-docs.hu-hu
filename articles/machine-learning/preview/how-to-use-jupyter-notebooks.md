---
title: "Jupyter notebookok használata az Azure Machine Learning-munkaterület |} Microsoft Docs"
description: "Útmutató az Azure Machine Learning-munkaterület a Jupyter notebookok funkció használata"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 4a8681bfdfe6b387d5790446d8b6dce04aaec580
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Jupyter notebook használata az Azure Machine Learning-munkaterület

Az Azure Machine Learning-munkaterület adatok interaktív tudományos kísérletezhet a Jupyter notebook integrációs keresztül támogatja. A cikkből megtudhatja, hogyan teheti növeléséhez sebessége és az adatok interaktív tudományos kísérletezhet minőségének hatékony használatára.

## <a name="prerequisites"></a>Előfeltételek
- [Telepítse, és hozzon létre az Azure Machine Learning](quickstart-installation.md).
- Ismernie kell a [Jupyter notebook](http://jupyter.org/), mivel ez a cikk nem oktatási Jupyter használatával kapcsolatban.

## <a name="jupyter-notebook-architecture"></a>Jupyter notebook architektúrája
Magas szinten Jupyter notebook architektúra tartalmaz három összetevővel, mindegyik különböző számítási környezetben futtathatja:

- **Ügyfél**: megkapja a felhasználói bevitel és jeleníti meg a kimeneti megjelenítése
- **Kiszolgáló**: a notebook fájlok (.ipynb) tároló kiszolgáló
- **Kernel**: a futtatási környezetet, ahol a notebook cellák tényleges végrehajtása történik,

További részletekért tekintse át a hivatalos [Jupyter dokumentáció](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Az alábbiakban látható, hogyan az ügyfél, a kiszolgáló és a kernel-architektúra hozzárendelése az Azure ML összetevői ábrázoló diagram.

![notebook architektúrája](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Az Azure ML munkaterület notebook kernelek
Azure ML munkaterület számos különböző kernelek elérheti futtatási konfigurációk konfigurálásával és számítási célt a `aml_config` a projekt mappájára. Új számítási cél hozzáadása kiállításával `az ml computetarget attach` parancs megegyezik a hozzáadása egy új rendszermag.

>[!NOTE]
>Tekintse át a [konfigurálása végrehajtási](experimentation-service-configuration.md) vonatkozó részletes információért futtassa a konfiguráció és célok számítási.

### <a name="kernel-naming-convention"></a>Kernel elnevezési egyezmény
Az Azure ML munkaterület egyéni Jupyter kernelek állít elő.  Ilyen nevű "\<projekt neve > \<futtassa a konfiguráció neve >". Például, ha nevű futtatási konfigurációt _docker-python_ nevű projekt _myIris_, Azure ML elérhetővé teszi a kernel "myIris docker-python" nevű.  A futó kernel beállítása a Jupyter Notebook "Kernel" menüben, a "Módosítás kernel" a menüben. A futó kernel neve jelenik meg a jobb szélén az egérrel a menüsoron.
 
A munkaterület jelenleg a következő típusú kernelek.

### <a name="local-python-kernel"></a>Helyi Python kernel
A Python kernel végrehajtási támogatja a helyi számítógépen. Integrált Azure Machine Learning futtatása előzmények támogatásával. A kernel neve nem általában a "helyi my_project_name".

>[!NOTE]
>Ne használja a "Python 3" kernel. Alapértelmezés szerint a Jupyter által biztosított önálló kernel. Nincs integrálva az Azure Machine Learning képességeit. Például a _% azureml_ Jupyter magic függvények visszatérési "nem található" hibák. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python Kernel a Docker (helyi vagy távoli)
A Python kernel futtat egy Docker-tároló vagy a helyi számítógépen, vagy egy távoli Linux virtuális gépre. A kernel értéke általában "my_project docker". A társított `docker.runconfig` fájl a `Framework` mező értéke `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>PySpark kernel a Docker (helyi vagy távoli)
A PySpark kernel parancsfájlok végrehajtja a Docker-tároló, vagy a helyi számítógépen, vagy egy távoli Linux virtuális gépen belül futtatott Spark környezetben. A kernel értéke általában "my_project docker". A társított `docker.runconfig` fájl a `Framework` mező értéke `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>PySpark kernel HDInsight-fürt
A kernel futtatja a távoli HDInsight-fürthöz, a mellékelt számítási célként a projekthez. A kernel értéke általában "my_project my_hdi". 

>[!IMPORTANT]
>Az a `.compute` a HDI fájlt a cél számítási, módosítania kell a `yarnDeployMode` mezőről `client` (az alapértelmezett érték `cluster`) ahhoz, hogy a kernel. 

## <a name="start-jupyter-server-from-the-workbench"></a>A munkaterület a Jupyter kiszolgáló indítása
Az Azure Machine Learning-munkaterület, jegyzetfüzeteket a munkaterület keresztül elért **notebookok** fülre. A _zárolásának Iris_ mintaprojektet tartalmaz egy `iris.ipynb` minta notebookot.

![notebookok lap](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

A notebook az Azure Machine Learning-munkaterület van megnyitva, amikor az megjelenik a saját dokumentum lapján **előkép mód**. Ez egy csak olvasható nézetű, egy futó Jupyter kiszolgáló és a kernel nem igényel.

![notebook előzetes verzió](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Kattintson a **Start Notebook Server** gomb Jupyter server elindul, és azokat a notebook vált **szerkesztési módot**. Az ismerős Jupyter notebook felhasználói felületet a munkaterület beágyazott jelenik meg. Most már beállíthat egy kernel a **Kernel** menü és a notebook interaktív munkamenet elindításához. 

>[!NOTE]
>Megjegyzés: nem helyi mag, indítsa el a használata, először egy percig is eltarthat. Végrehajthat `az ml experiment prepare` parancssori ablak a számítási cél előkészítheti a kernel elindíthatja sokkal gyorsabb után készen áll a testreszabásra a számítási cél parancsot.

![Szerkesztési módban](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Ez az egy teljes mértékben Jupyter notebook felületet. Rendszeres notebook műveletek és a billentyűparancsok támogatottak az egyes fájlműveletek, kivéve ablakot óta a munkaterületet üzemeltető keresztül végezhető **notebookok** lap és **fájl** fülre.

## <a name="start-jupyter-server-from-command-line"></a>Jupyter kiszolgáló indítása a parancssorból
A notebook munkamenet kiállításával is elindíthatja egy `az ml notebook start` a parancssori ablakból:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Az alapértelmezett böngésző automatikusan elindult a projekt kezdőkönyvtár mutató Jupyter kiszolgálóval. URL-cím és a parancssori ablakban jelenik meg a token használatával is böngészőablakok más helyileg. 

![Projekt irányítópultján](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Most rákattinthat a egy `.ipynb` notebook fájl megnyitásához, és állítsa be a rendszermag (Ha még nem lett beállítva), és indítsa el az interaktív munkamenet.

![Projekt irányítópultján](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>-Parancsokkal magic kísérletek kezelése

Használhat [magic parancsok](http://ipython.readthedocs.io/en/stable/interactive/magics.html) a notebook celláiban nyomon követheti a futtatási előzményei, és mentse a kimeneti, például a modellek és adathalmazokat.

Egyes notebook követéséhez cella, használja "% azureml előzmények" magic parancsot futtatja. Az előzmények bekapcsolása után minden egyes cella futtatásához megjelenik bejegyzésének futtatási előzményei.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Kapcsolja be a nyomon követési futtassa cella, használja a "% azureml előzmények" off"magic parancsot.

"% Azureml feltöltés" magic parancs segítségével futni, hanem a modell-és adatfájlok mentéséhez. A mentett objektumok jelennek meg kimenetek futtatási előzményei nézetben a megadott futtató.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>A kimenetek "kimenetek" nevű mappába kell menteni.

## <a name="next-steps"></a>További lépések
- Jupyter notebook használatával megismeréséhez látogasson el a [Jupyter dokumentációs](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Ahhoz, hogy az Azure ML kísérletezhet végrehajtási környezet bemutatják, tekintse át a [áttekintés az Azure Machine Learning kísérletezhet szolgáltatás](experimentation-service-configuration.md)

