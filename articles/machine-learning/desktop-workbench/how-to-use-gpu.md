---
title: Hogyan használható az Azure Machine Learning GPU |} A Microsoft Docs
description: Ez a cikk ismerteti a grafikus feldolgozási egységek (GPU) használja a Neurális hálózatokat az Azure Machine Learning Workbenchben.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 09d8e3da543cdf4433d986b321697abcad88eb22
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157990"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Az Azure Machine Learning GPU használata
Grafikus feldolgozóegység (GPU) széles körben használt, nagy számítási igényű feladatokat, amelyek általában fordulhat elő, amikor bizonyos Neurális hálózat modellek betanítása feldolgozásához. Gpu-k használatával jelentősen csökkentheti a modellek képzési idején. Ebből a dokumentumból megismerheti, hogyan konfigurálhatja az Azure Machine Learning Workbench használata [DSVM (adatelemző virtuális gép)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) gpu-kkal felszerelt végrehajtási célként. 

## <a name="prerequisites"></a>Előfeltételek
- Ez az útmutató elvégezhető, először szüksége [Azure Machine Learning Workbench telepítése](../service/quickstart-installation.md).
- Szüksége lesz az NVidia gpu-kkal felszerelt számítógépeken való hozzáférést.
    - Gpu-k használatával közvetlenül a helyi gépen (Windows vagy macOS) futtathatók a parancsprogramok.
    - Gpu-k használatával Linux rendszerű gépen a Docker-tárolóban is futtathatja parancsfájlok...

## <a name="execute-in-local-environment-with-gpus"></a>Hajtsa végre a _helyi_ gpu-környezet
Az Azure Machine Learning Workbench telepítése a GPU-kkal felszerelt számítógépre, és hajtja végre _helyi_ környezetben. Ez lehet:
- Egy fizikai Windows- vagy MacOS rendszerű gépre.
- Például egy Windows DSVM Windows VM (virtuális gép) üzembe helyezett Azure NC sorozatú virtuális gépek sablon használatával.

Ebben az esetben nem szükséges az Azure ML Workbenchben speciális konfiguráció vannak. Csak a ellenőrizze, hogy Ön rendelkezik a szükséges illesztőprogramok, eszközkészletek és GPU-kompatibilis machine learning-kódtárak helyben telepítve legyen. Egyszerűen hajtja végre _helyi_ környezetben, ahol a Python-futtatókörnyezet közvetlenül hozzáférhet a helyi GPU-hardveres.

1. Nyissa meg az AML Workbench. Lépjen a **fájl** és **parancssor megnyitása**. 
2. A parancssorból telepítse a deep learning GPU-kompatibilis keretrendszert, például a Microsoft Cognitive Toolkit, tensorflow-hoz és stb. Példa:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.5 with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

3. Python-kód, amely a deep learning-kódtárak írni.
4. Válasszon _helyi_ számítási környezet, és hajtsa végre a Python-kódban.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Hajtsa végre a _docker_ környezetben, a Linux rendszerű virtuális gépek a GPU-kkal
Az Azure Machine Learning Workbench is végrehajtása támogatja a Docker az Azure Linux virtuális gép. Itt van egy remek megoldást futtatható nagy számítási igényű feladatok nagy teljesítményű virtuális hardvereszköz, és csak azért kell fizetnie a használati kikapcsolásával, ha ezzel elkészült. Bár elvileg gpu-k használata Linux rendszerű virtuális gépeken, az Ubuntu-alapú DSVM tartalmaz a szükséges CUDA-illesztőprogramok és kódtárak előre telepítve van, így sokkal könnyebb a telepítési. Kövesse az alábbi lépéseket:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Egy Ubuntu-alapú Linux rendszerű adatelemző virtuális gép létrehozása az Azure-ban
1. Nyissa meg a webböngészőjét, és nyissa meg a [Azure Portalon](https://portal.azure.com)

2. Válassza ki **+ új** bal oldalán a Portalon.

3. Keressen a "Adatelemző virtuális gép Linux (Ubuntu)" a Marketplace-en.

4. Kattintson a **létrehozás** hozhat létre egy Ubuntu dsvm-hez.

5. Töltse ki a **alapjai** űrlapot a szükséges információkkal.
A virtuális gép helyének kiválasztásakor vegye figyelembe, hogy GPU-beli virtuális gépek csak elérhető egyes Azure-régióban, például **USA déli középső Régiójában**. Lásd: [elérhető termékek régiók szerint számítási](https://azure.microsoft.com/regions/services/).
Az OK gombra kattintva mentse a **alapjai** információkat.

6. Válassza ki a virtuális gép méretét. Válasszon egyet a különböző méretű virtuális gépekkel NC-előtaggal, amely NVidia GPU-lapkáihoz vannak ellátva.  Kattintson a **nézet összes** igény szerint teljes listájának megtekintéséhez. Tudjon meg többet [GPU-megkönnyíti az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Fejezze be a többi beállításnál, és tekintse át a vásárlási információk. Kattintson a vásárlás a virtuális gép létrehozásához. Jegyezze fel a virtuális gép számára lefoglalt IP-cím. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Új projekt létrehozása az Azure ML Workbenchben 
Használhatja a _TensorFlow használatával Írisz MNIST_ például vagy a _MNIST Írisz adatkészletet a CNTK_ példa.

### <a name="create-a-new-compute-target"></a>Hozzon létre egy új számítási célt
Indítsa el az Azure Machine Learning Workbench parancssori. Adja meg a következő parancsot. A helyőrző szöveget az alábbi példában cserélje le a saját nevét, IP címe, felhasználónév és jelszó értékeit. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Az Azure ML Workbenchben való hozzáférés GPU konfigurálása
Lépjen vissza a projektet, és nyissa meg **Fájlnézetben**, majd kattintson a **frissítése** gombra. Ekkor megjelenik a két új konfigurációs fájlok `my_dsvm.compute` és `my_dsvm.runconfig`.
 
Nyissa meg a `my_dsvm.compute`. Módosítsa a `baseDockerImage` való `microsoft/mmlspark:plus-gpu-0.9.9` , és adjon hozzá egy új sort `nvidiaDocker: true`. Ezért a fájl alábbi két sorral kell rendelkeznie:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Most nyissa meg a `my_dsvm.runconfig`, módosítsa `Framework` értéket `PySpark` való `Python`. Ha nem látja ezt a sort, adja hozzá, mivel az alapértelmezett érték `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Hivatkozás Deep Learning keretrendszer 
Nyissa meg `conda_dependencies.yml` fájlt, és győződjön meg arról, a deep learning-keretrendszer Python-csomagok GPU verzióját használja. A minta-projektek listázása a CPU-verziók, ezért ezt a módosítást kell.

Példa a tensorflow-hoz: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Példa a Microsoft Cognitive Toolkit:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.5 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

### <a name="execute"></a>Végrehajtás
Most már készen áll a Python-szkriptek futtatásához. Az Azure Machine Learning Workbench használatával belül futtathatja őket a `my_dsvm` környezet, vagy Ön is indítsa el az eszközt a parancssorból:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Győződjön meg arról, hogy a grafikus Processzor szolgál, vizsgálja meg a futtatási kimenet megtekintéséhez a következőhöz hasonló:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Gratulálunk! A szkript csak egy Docker-tároló útján GPU hatékonyságát harnessed!

## <a name="next-steps"></a>További lépések
Gyorsítsa fel a Neurális hálózat-betanítást szeretne Azure Machine Learning-katalógus használatával GPU példafájlt.
