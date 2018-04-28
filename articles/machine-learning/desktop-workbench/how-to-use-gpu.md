---
title: Az Azure Machine Learning GPU használata |} Microsoft Docs
description: Ez a cikk ismerteti a grafikus feldolgozása egységek (GPU) kell még betanítani mély Neurális hálózatokat az Azure Machine Learning-munkaterület használata.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 1bd8f28b472361470fa8020149a9af0c690fbc52
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Az Azure Machine Learning GPU használata
Grafikus processzorra (GPU) széles körben használt, amely általában akkor szükséges, ha bizonyos mély Neurális hálózat modellek betanítása számításilag intenzív feladatok feldolgozásához. Feldolgozóegységekkel használatával jelentősen csökkentheti a modellek képzési idején. Ebben a dokumentumban megismerheti, hogyan konfigurálhatja az Azure ML munkaterület használandó [DSVM (adatok tudományos virtuális gép)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) Feldolgozóegységekkel felszerelt végrehajtási célként. 

## <a name="prerequisites"></a>Előfeltételek
- Ez az útmutató Útmutató lépéseit, kell első [telepítse az Azure ML munkaterület](../service/quickstart-installation.md).
- Ön hozzáféréssel kell rendelkeznie NVidia Feldolgozóegységekkel felszerelt számítógépeken.
    - Közvetlenül a helyi számítógépen (Windows vagy macOS) a parancsfájlok futtathatók a GPU-k tekintetében.
    - A virtuális gép Feldolgozóegységekkel futtathatja parancsfájlok egy Docker-tároló.

## <a name="execute-in-local-environment-with-gpus"></a>A végrehajtást _helyi_ Feldolgozóegységekkel környezet
Azure ML munkaterület Feldolgozóegységekkel felszerelt számítógépre telepítheti, és elleni végrehajtása _helyi_ környezetben. Ez lehet:
- Egy fizikai Windows vagy macOS gép.
- Windows virtuális gép (virtuális gép) például a Windows DSVM kiépített Azure NC sorozatú virtuális gépek sablon használatával.

Ebben az esetben nincsenek Azure ML-munkaterület szükséges semmilyen speciális konfigurációra. Csak győződjön meg arról, hogy a szükséges illesztőprogramok, eszközök gazdag és GPU-kompatibilis gépi tanulási helyileg telepített könyvtárak. Egyszerűen hajtható végre elleni _helyi_ környezetben, ahol a Python-futtatókörnyezet közvetlenül hozzáférhetnek a helyi GPU-hardveres.

1. Nyissa meg a AML munkaterületet. Ugrás a **fájl** és **nyissa meg a parancssort**. 
2. A parancssorból telepítse a mély tanulási GPU-kompatibilis keretrendszer például a Microsoft kognitív eszközkészlet TensorFlow vagy stb. Példa:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Írja be, amely kihasználja a szalagtárak tanulási mély Python-kódot.
4. Válasszon _helyi_ számítási környezetet, és futtassa a Python-kódot.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>A végrehajtást _docker_ Feldolgozóegységekkel rendelkező Linux virtuális gépet környezete
Az Azure ML munkaterület végrehajtási is támogatja a Docker egy Azure Linux virtuális gép. Itt lehetősége van egy nagyszerű számításilag időigényes feladat futtatható hatékony virtuális hardvereszköz, és csak azért fizessen a használati által, hogy, amikor hajtja végre. Alapvetően is Feldolgozóegységekkel használata a Linux virtuális gépen, az Ubuntu alapú DSVM rendelkezik a szükséges CUDA illesztőprogramok és előre telepített, könyvtárak így sokkal egyszerűbb, mivel a telepítési. Kövesse az alábbi lépéseket:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Ubuntu Linux adatokhoz tudományos virtuális gép létrehozása az Azure-ban
1. Nyissa meg a webböngészőt, és navigáljon a [Azure-portálon](https://portal.azure.com)

2. Válassza ki **+ új** a portál bal oldalon található.

3. Keresse meg az "Adatok tudományos virtuális gép Linux (Ubuntu)" a piactéren.

4. Kattintson a **létrehozása** egy Ubuntu DSVM létrehozásához.

5. Töltse ki a **alapjai** a szükséges információt a képernyőn.
Lehetőséget választva a virtuális gép helyét, vegye figyelembe, hogy GPU virtuális gépek találhatók csak bizonyos Azure-régiók, például **déli középső Régiójában**. Lásd: [régiónként rendelkezésre álló termékek számítási](https://azure.microsoft.com/regions/services/).
Az OK gombra kattintva mentse a **alapjai** információkat.

6. Válassza ki a virtuális gép méretét. Válasszon ki egy virtuális hálózati vezérlő által az utóbbiak NVidia GPU modulok felszerelt méretét.  Kattintson a **nézet összes** a teljes lista megtekintéséhez, igény szerint. További információ [GPU-ellátva Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. A többi beállítás befejezéséhez, és a vásárlási információk áttekintéséhez. Kattintson a beszerzési a virtuális gép létrehozásához. Jegyezze fel a virtuális gép számára lefoglalt IP-címét. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Hozzon létre egy új projektet az Azure ML munkaterület 
Használhatja a _TensorFlow használatával zárolásának MNIST_ példa, vagy a _CNTK zárolásának MNIST adatkészlet_ példa.

### <a name="create-a-new-compute-target"></a>Hozzon létre egy új számítási cél
Nyissa meg a parancssor az Azure ML munkaterületet. Adja meg a következő parancsot. Az alábbi példa a helyőrzőket cserélje le a saját értékeit a neve, a IP-cím, a felhasználónév és a jelszó. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Azure ML-munkaterület GPU hozzáférés konfigurálása
Lépjen vissza a projektet, és nyissa meg **fájlnézetéhez**, és találat a **frissítése** gombra. Most megjelenik a két új konfigurációs fájlok `my_dsvm.compute` és `my_dsvm.runconfig`.
 
Nyissa meg a `my_dsvm.compute`. Módosítsa a `baseDockerImage` való `microsoft/mmlspark:plus-gpu-0.7.9` , és adja hozzá egy új sor `nvidiaDocker: true`. Ezért a fájl két sort kell rendelkezniük:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Most nyissa meg a `my_dsvm.runconfig`, módosítsa `Framework` értéket `PySpark` való `Python`. Ha ezt a sort nem talál, vegye fel, mert az alapértelmezett érték lesz `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Hivatkozás a részletes oktatási keretrendszer 
Nyissa meg `conda_dependencies.yml` fájlt, és ellenőrizze, hogy a tanulási keretrendszer Python-csomagokat, átfogó a GPU verzióját használja. A mintaprojektjeit CPU-verziók listában, ezért meg kell ezt a módosítást.

Példa a TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Példa a Microsoft kognitív eszközkészlet:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

A Microsoft kognitív eszközkészlet, amely biztosít a teljesítménnyel kapcsolatos fejlesztések multi-GPU virtuális gépeken a 1 bit-SGD verziója is használható. Vegye figyelembe [1 bit-SGD licenc kapcsolatos követelmények](https://docs.microsoft.com/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Végrehajtás
Most már készen áll a Python-parancsfájl futtatását. Az Azure ML munkaterület használatával belül futtathatja a `my_dsvm` környezetben, vagy indíthatja el azt a parancssorból:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Győződjön meg arról, hogy használja-e a GPU, vizsgálja meg a futtatási kimenet az alábbihoz hasonló a következő:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Gratulálunk! A parancsfájl csak egy Docker-tároló keresztül GPU hatványa harnessed!

## <a name="next-steps"></a>További lépések
Tekintse meg a GPU annak érdekében, mély Neurális hálózat oktatóanyagokat Azure ML Gallery használatának példája.
