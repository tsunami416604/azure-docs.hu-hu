---
title: Az Azure Machine Learning-modell felügyeleti beállítás és konfiguráció |} A Microsoft Docs
description: Ez a dokumentum ismerteti a lépéseket és fogalmak vesz részt az Azure Machine Learning Modellkezelés telepítési és beállítási folyamatán.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: 150114184f6f04f22aa9da409758daa6a0d175b5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369050"
---
# <a name="model-management-setup"></a>Modell-kezelés beállítása

Ez a dokumentum megkönnyíti az Azure Machine Learning modellkezelés használatával helyezheti üzembe és felügyelheti a gépi tanulási modellek webszolgáltatásként. 

Azure Machine Learning modellkezelés használatával, hogy hatékonyan telepítheti és kezelheti beépített több különféle keretrendszereket, beleértve a könnyen használható, a Keras, TensorFlow, a Microsoft Cognitive Toolkit vagy a Python Machine Learning-modellek. 

Ez a dokumentum végén a modell felügyeleti környezetének beállítása és a gépi tanulási modellek üzembe helyezésére kész eseményhalmazt kell lennie.

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez
Ez az útmutató kinyerhessen kell közreműködői hozzáférés az Azure-előfizetéssel, vagy egy erőforráscsoport, a modellek üzembe helyezhető.
A parancssori felület előre telepítve áll rendelkezésre az Azure Machine Learning Workbench, majd a [Azure Dsvm](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>A parancssori felületről
A parancssori felületen (CLI-k) a Workbench használatához kattintson **fájl** -> **parancssor megnyitása**. 

Egy Data Science virtuális gépen, csatlakozzon, és nyissa meg a parancssort. Típus `az ml -h` beállításainak megjelenítéséhez. A parancsokkal kapcsolatos további részletekért használja a--help jelző.

Az összes többi rendszeren kellene telepíteni a felületekre.

>[!NOTE]
> Egy Linuxos DSVM Jupyter Notebookban elérheti az Azure CLI és az Azure ML CLI a következő parancsot az alábbi formátumban.  **Ez az egy Linux rendszerű adatelemző virtuális GÉPET, a Jupyter notebook számára kifejezetten**.  Ezek a parancsok a jelenlegi Python-kernel a jegyzetfüzet eléréséhez (pl. a conda `py35` környezet)
>```
>import sys
>! {sys.executable} -m azure.cli login
>! {sys.executable} -m azure.cli ml -h
>```

### <a name="installing-or-updating-on-windows"></a>A Windows telepítése (vagy frissítése)

Telepítse a Pythont a https://www.python.org/. Győződjön meg arról, hogy instalace pip kiválasztott.

Nyisson meg egy parancssort, használja a Futtatás rendszergazdaként, és futtassa a következő parancsokat:

```cmd
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="installing-or-updating-on-linux"></a>Linux rendszeren telepíti (vagy frissítése)
Futtassa a következő parancsot a parancssorból, és kövesse az utasításokat:

```bash
sudo -i
pip install -r https://aka.ms/az-ml-o16n-cli-requirements-file
```

### <a name="configuring-docker-on-linux"></a>Docker konfigurálása linuxon
Annak érdekében, hogy konfigurálja a Docker Linux rendszeren nem legfelső szintű felhasználók általi használatra, az alábbi utasításokat: [telepítés utáni lépések Linux rendszeren](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> Egy Linux rendszerű adatelemző virtuális GÉPET a Docker megfelelően állítja be az alábbi szkriptet futtathatja. **Ne felejtse el, jelentkezzen ki, és jelentkezzen be újra a szkript futtatása után.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>A modell üzembe helyezése
A CLI használatával helyezhet üzembe modelleket webszolgáltatásként. A webes szolgáltatások telepíthetők helyileg vagy a fürthöz.

Indítsa el a helyi telepítés, ellenőrizze, hogy a modell és a kód működik, majd üzembe helyezése egy fürtön méretezési éles környezetben való használatra.

Először szüksége az üzembe helyezési környezetet. A környezet beállítása, adott ideje feladat. A telepítés befejezése után újból felhasználhatja a környezetet a későbbi üzembe helyezéséhez. A következő részben további részleteket talál.

Ha a környezet a telepítés befejezése:
- Jelentkezzen be Azure kéri. Jelentkezzen be, használja egy webböngészőben nyissa meg a https://aka.ms/devicelogin , és adja meg a megadott kódot a hitelesítéshez.
- A hitelesítési folyamat során kell megadnia egy olyan fiók való hitelesítéshez. Fontos: Válasszon egy érvényes Azure-előfizetést és a fiókban lévő erőforrások létrehozásához szükséges engedélyekkel rendelkező fiókkal. A bejelentkezési befejeződése után az előfizetési adatai jelennek meg, és a rendszer megkérdezi, hogy folytatja az adott fiókkal.

### <a name="environment-setup"></a>Környezet beállítása
A telepítési folyamat elindításához a következő parancsok beírásával néhány környezet szolgáltatót regisztrálnia kell:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
```
#### <a name="local-deployment"></a>Helyi üzembe helyezés
Telepítse, és tesztelje a webszolgáltatást a helyi gépen, állítsa be a következő parancsot a helyi környezetben. Az erőforráscsoport nevét nem kötelező.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Helyi webszolgáltatás üzembe megköveteli, hogy a helyi gépen telepítheti a Dockert. 
>

A helyi környezetben setup parancs az előfizetésben hoz létre az alábbi forrásanyagokat:
- Egy erőforráscsoport (Ha nincs megadva, vagy ha a megadott név nem létezik.)
- Storage-fiók
- Egy Azure Container Registryt (ACR)
- Egy Application insights-fiók

Miután a telepítés sikeresen befejeződött, állítsa be a környezetben a következő parancs használható:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Fürt üzembe helyezése
A nagy léptékű termelési forgatókönyvekhez használja a fürt üzembe helyezése. Az orchestrator, beállít egy ACS-fürtben Kubernetes-szel. Az ACS-fürt kiterjeszthető a webszolgáltatás-hívás nagyobb átviteli sebesség kezelésére.

Éles környezetben üzembe helyezni webszolgáltatását, először állítsa be a környezetet, a következő paranccsal:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

A fürt környezet setup parancs az előfizetésben hoz létre az alábbi forrásanyagokat:
- Egy erőforráscsoport (Ha nincs megadva, vagy ha a megadott név nem létezik.)
- Storage-fiók
- Egy Azure Container Registryt (ACR)
- Egy üzemelő Kubernetes-példányt az Azure Container Service (ACS) fürtön
- Egy Application insights-fiók

>[!IMPORTANT]
> Annak érdekében, hogy a fürt környezet létrehozása sikeresen megtörtént, szüksége lesz az Azure-előfizetés vagy az erőforráscsoport közreműködői hozzáféréssel kell rendelkeznie.

Az erőforráscsoport, a storage-fiók és az ACR gyorsan jönnek létre. Az ACS telepítési akár 20 percet is igénybe vehet. 

Egy folyamatban lévő fürtfelügyeleti kiépítési állapotának ellenőrzéséhez használja a következő parancsot:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

A telepítő befejezése után állítsa be a környezetet a telepítéshez használandó kell. Használja az alábbi parancsot:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> További telepítések esetén a környezet létrehozása után csak szeretné használni a fenti set parancsban az ismételt használatát.
>

### <a name="create-a-model-management-account"></a>Hozzon létre egy Modellkezelési fiókot
Modellkezelési fiók szükség a modellek üzembe helyezéséhez. Kell tennie egyszer előfizetésenként, és felhasználhatja ugyanazt a fiókot több üzemelő példányban.

Hozzon létre egy új fiókot, használja a következő parancsot:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Egy meglévő fiókot használ, használja a következő parancsot:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

Ez a folyamat eredményeként a környezet készen áll, és a Machine Learning-modellek üzembe helyezheti és kezelheti szükséges szolgáltatások biztosításához létrehozását modellkezelési fiók (lásd: [Azure Machine Learning Modellkezelés](model-management-overview.md) számára egy – áttekintés).

## <a name="next-steps"></a>További lépések

* A webszolgáltatások üzembe helyezése a helyi számítógépre vagy egy fürtön való futtatásához útmutatást továbbra is a [egy gépi tanulási modellt webszolgáltatásként üzembe helyezése](model-management-service-deploy.md).
* Próbálja ki az egyik a katalógusban számos mintát.
