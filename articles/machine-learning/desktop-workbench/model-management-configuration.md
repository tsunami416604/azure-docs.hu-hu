---
title: Az Azure Machine Learning-modell felügyeleti beállítás és konfiguráció |} A Microsoft Docs
description: Ez a dokumentum ismerteti a lépéseket és fogalmak vesz részt az Azure Machine Learning Modellkezelés telepítési és beállítási folyamatán.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/29/2017
ms.openlocfilehash: 883e3d2c5945a38c8fbca5c9f0f5e8a1e4093be1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646079"
---
# <a name="model-management-setup"></a>Modell-kezelés beállítása

## <a name="overview"></a>Áttekintés
Ez a dokumentum megkönnyíti az Azure Machine Learning modellkezelés használatával helyezheti üzembe és felügyelheti a gépi tanulási modellek webszolgáltatásként. 

Azure Machine Learning modellkezelés használatával, hogy hatékonyan telepítheti és kezelheti beépített több különféle keretrendszereket, beleértve a könnyen használható, a Keras, TensorFlow, a Microsoft Cognitive Toolkit vagy a Python Machine Learning-modellek. 

Ez a dokumentum végén a modell felügyeleti környezetének beállítása és a gépi tanulási modellek üzembe helyezésére kész eseményhalmazt kell lennie.

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez
A lehető leghatékonyabban Ez az útmutató, az Azure-előfizetéssel, amely központilag telepíthető a modellek tulajdonosi hozzáféréssel kell.
A parancssori felület előre telepítve áll rendelkezésre az Azure Machine Learning Workbench, majd a [Azure Dsvm](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>A parancssori felületről
A parancssori felületen (CLI-k) a Workbench használatához kattintson **fájl** -] **nyílt forráskódú parancssori felületének**. 

Egy Data Science virtuális gépen, csatlakozzon, és nyissa meg a parancssort. Típus `az ml -h` beállításainak megjelenítéséhez. A parancsokkal kapcsolatos további részletekért használja a--help jelző.

Az összes többi rendszeren kellene telepíteni a felületekre.

### <a name="installing-or-updating-on-windows"></a>A Windows telepítése (vagy frissítése)

Telepítse a Pythont a https://www.python.org/. Győződjön meg arról, hogy instalace pip kiválasztott.

Nyisson meg egy parancssort, használja a Futtatás rendszergazdaként, és futtassa a következő parancsokat:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Ha egy korábbi verziója van, távolítsa el először a következő paranccsal:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Linux rendszeren telepíti (vagy frissítése)
Futtassa a következő parancsot a parancssorból, és kövesse az utasításokat:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

Miután befejeződött a telepítőből, futtassa a következő parancsot:

```bash
sudo /opt/microsoft/azureml/initial_setup.sh
```

>[!NOTE]
>Jelentkezzen ki, majd jelentkezzen be újra, az SSH-munkamenet a módosítások érvénybe léptetéséhez.
>Az előző parancs segítségével frissítse a CLI-k a dsvm-hez a korábbi verzióját.
>

## <a name="deploying-your-model"></a>A modell üzembe helyezése
A CLI-k használatával helyezhet üzembe modelleket webszolgáltatásként. A webes szolgáltatások telepíthetők helyileg vagy a fürthöz.

Indítsa el a helyi telepítés, ellenőrizze, hogy a modell és a kód működik, majd üzembe helyezése egy fürtön méretezési éles környezetben való használatra.

Először szüksége az üzembe helyezési környezetet. A környezet beállítása, adott ideje feladat. A telepítés befejezése után újból felhasználhatja a környezetet a későbbi üzembe helyezéséhez. A következő részben további részleteket talál.

Ha a környezet a telepítés befejezése:
- Jelentkezzen be Azure kéri. Jelentkezzen be, használja egy webböngészőben nyissa meg a https://aka.ms/devicelogin , és adja meg a megadott kódot a hitelesítéshez.
- A hitelesítési folyamat során kell megadnia egy olyan fiók való hitelesítéshez. Fontos: Válasszon egy érvényes Azure-előfizetést és -erőforrások létrehozása a fiókban. megfelelő engedélyekkel rendelkező fiók – a bejelentkezési befejeződése után az előfizetési adatai jelennek meg, és a rendszer megkérdezi, hogy folytatni szeretné a kiválasztott fiók.

### <a name="environment-setup"></a>Környezet beállítása
A telepítés megkezdéséhez szüksége a környezet-szolgáltató regisztrálásához a következő parancs beírásával:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```

#### <a name="local-deployment"></a>Helyi üzembe helyezés
A helyi gépen a webszolgáltatás üzembe helyezése és teszteléséhez a következő paranccsal helyi környezet beállítása:

```azurecli
az ml env setup -l [location of Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Helyi webszolgáltatás üzembe van szükség a helyi gépen telepítheti a Dockert. 
>

A helyi környezetben setup parancs az előfizetésben hoz létre az alábbi forrásanyagokat:
- Egy erőforráscsoport (Ha nincs megadva)
- Storage-fiók
- Egy Azure Container Registryt (ACR)
- Application Insights

Miután a telepítés sikeresen befejeződött, állítsa be a környezetben a következő parancs használható:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Fürt üzembe helyezése
A nagy léptékű termelési forgatókönyvekhez használja a fürt üzembe helyezése. Az orchestrator, beállít egy ACS-fürtben Kubernetes-szel. Az ACS-fürt kiterjeszthető a webszolgáltatás-hívás nagyobb átviteli sebesség kezelésére.

Éles környezetben üzembe helyezni webszolgáltatását, először állítsa be a környezetet, a következő paranccsal:

```azurecli
az ml env setup -c --name [your environment name] --location [Azure region e.g. eastus2] [-g [resource group]]
```

A fürt környezet setup parancs az előfizetésben hoz létre az alábbi forrásanyagokat:
- Egy erőforráscsoport (Ha nincs megadva)
- Storage-fiók
- Egy Azure Container Registryt (ACR)
- Egy üzemelő Kubernetes-példányt az Azure Container Service (ACS) fürtön
- Application Insights

Az erőforráscsoport, a storage-fiók és az ACR gyorsan jönnek létre. Az ACS telepítési akár 20 percet is igénybe vehet. 

A telepítő befejezése után állítsa be a környezetet a telepítéshez használandó kell. Használja az alábbi parancsot:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> További telepítések esetén a környezet létrehozása után csak szeretné használni a fenti set parancsban az ismételt használatát.
>

>[!NOTE] 
>HTTPS-végpont létrehozásához adja meg az SSL-tanúsítványt, a fürt létrehozásakor a – tanúsítvány-nevét és – tanúsítvány-pem lehetőségek az ml env beállításában. Ez beállítja a fürt https, a megadott tanúsítvány védi a kérések kiszolgálására. A telepítő befejeződése után hozzon létre egy CNAME DNS-rekordot, amely a fürt teljes Tartományneve.

### <a name="create-an-account"></a>Fiók létrehozása
Modellek üzembe helyezéséhez szükség egy fiókra. Kell tennie egyszer fiókonként, és felhasználhatja ugyanazt a fiókot több üzemelő példányban.

Hozzon létre egy új fiókot, használja a következő parancsot:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Egy meglévő fiókot használ, használja a következő parancsot:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>A modell üzembe helyezése
Most már készen áll a mentett modellt webszolgáltatásként üzembe helyezéséhez. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

### <a name="next-steps"></a>További lépések
Próbálja ki a katalógus számos minták egyikét.
