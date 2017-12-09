---
title: "Az Azure gépi tanulási modell felügyeleti beállítás és konfiguráció |} Microsoft Docs"
description: "Ez a dokumentum ismerteti a lépéseket és fogalmak beállításáról és konfigurálásáról a modell kezelése az Azure Machine Learning részt."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/6/2017
ms.openlocfilehash: fe03a24b0d9f5ef6d0f20dac15ea980a8663a7b2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="model-management-setup"></a>Modell kezelésének beállítása

Ez a dokumentum megismerteti Azure ML-modell felügyeleti használatával történő telepítéséhez és kezeléséhez a gépi tanulási modellek webszolgáltatásként. 

Azure ML-modell eszköz használatával hatékonyan telepítheti és kezelheti a Machine Learning modellek, amelyek többek között a SparkML Keras, TensorFlow, a Microsoft kognitív eszközkészlet vagy a Python keretrendszerek számos használatával készített. 

Ez a dokumentum végéig a modell felügyeleti környezet beállítása és készen áll a központi telepítése a gépi tanulási modellek képesnek kell lennie.

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez
Ahhoz, hogy ez az útmutató a legtöbbet, Azure-előfizetés vagy egy erőforráscsoport, amely központilag telepíthető a modellek közreműködői hozzáférést kell rendelkeznie.
A parancssori felület előre előre telepített, az Azure Machine Learning-munkaterület és a [Azure DSVMs](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).

## <a name="using-the-cli"></a>A parancssori felület használatával
A parancssori felületen (CLIs) az a munkaterület használatához kattintson **fájl** -> **nyissa meg a parancssort**. 

Az adatok tudományos virtuális gép, csatlakozzon, és nyissa meg a parancssort. Típus `az ml -h` a beállítások megtekintéséhez. További részletek a parancsok, használja a--Súgó jelzőt.

Az összes többi rendszeren akkor kell telepíteni a CLIs.

### <a name="installing-or-updating-on-windows"></a>A Windows telepítése (vagy frissítése)

Python https://www.python.org/ telepítse. Győződjön meg arról, hogy megadta a pip telepítése.

Nyisson meg egy parancssort, használja a Futtatás rendszergazdaként, és futtassa a következő parancsokat:

```cmd
pip install azure-cli
pip install azure-cli-ml
```
 
>[!NOTE]
>Ha egy korábbi verziója van, távolítsa el azt először a következő paranccsal:
>

```cmd
pip uninstall azure-cli-ml
```

### <a name="installing-or-updating-on-linux"></a>Linux telepítése (vagy frissítése)
A következő parancsot a parancssorból, és kövesse az utasításokat:

```bash
sudo -i
pip install azure-cli
pip install azure-cli-ml
```

### <a name="configuring-docker-on-linux"></a>Linux Docker konfigurálása
Ahhoz, hogy konfigurálja a Docker Linux a nem gyökér felhasználók számára, kövesse az utasításokat itt: [telepítés utáni lépések Linux](https://docs.docker.com/engine/installation/linux/linux-postinstall/)

>[!NOTE]
> A Linux DSVM a Docker megfelelően be az alábbi parancsfájlt is futtathatja. **Ne felejtse el jelentkezzen ki, majd ismét be a parancsfájl futtatása után.**
>```
>sudo /opt/microsoft/azureml/initial_setup.sh
>```

## <a name="deploying-your-model"></a>A modell központi telepítése
A parancssori felület segítségével telepítheti a modellek webszolgáltatásként. A webszolgáltatások telepíthető a helyi számítógépre vagy egy fürtöt.

Indítsa el a helyi telepítés, ellenőrizze, hogy a modell és kód működik, majd telepítése egy fürt méretezési használható.

Elindítani, akkor be kell állítania a telepítési környezet. A környezetet, adott ideje feladat. A telepítés befejezése után újra felhasználhatja a környezetben a következő központi telepítések elvégzéséhez. A következő részben részletesebben.

Ha a környezetben a telepítés befejezése:
- Jelentkezzen be Azure kéri. A bejelentkezéshez egy webböngésző segítségével nyissa meg a lap https://aka.ms/devicelogin, és adja meg a hitelesítéshez a megadott kód.
- A hitelesítési folyamat során kéri egy olyan fiók való hitelesítéshez szükséges. Fontos: Válasszon egy érvényes Azure-előfizetés és a fiókban. az erőforrások létrehozásához szükséges engedélyekkel rendelkező fiók - napló a befejeződése után az előfizetési adatai számára jelenik meg, és a rendszer megkérdezi, hogy folytatni szeretné a kiválasztott fiókot.

### <a name="environment-setup"></a>Környezet beállítása
A telepítés megkezdéséhez szüksége a környezet szolgáltató regisztrálásához a következő parancs beírásával:

```azurecli
az provider register -n Microsoft.MachineLearningCompute
```
#### <a name="local-deployment"></a>Helyi telepítés
A webes szolgáltatás a helyi számítógép telepítéséhez és teszteléséhez a következő paranccsal helyi környezet beállítása. Az erőforráscsoport neve nem kötelező megadni.

```azurecli
az ml env setup -l [Azure Region, e.g. eastus2] -n [your environment name] [-g [existing resource group]]
```
>[!NOTE] 
>Helyi webkiszolgáló szolgáltatás telepítéséhez szükséges Docker telepítését a helyi számítógépen. 
>

A helyi környezet setup parancs az előfizetésében hoz létre a következőket:
- Egy erőforráscsoport (Ha nincs megadva, vagy ha a megadott név nem létezik.)
- a storage-fiók
- Egy Azure-tárolót beállításjegyzék (ACR)
- Az Application insights-fiókot

Telepítés sikeres befejezése után állítsa be a környezet használható a következő parancsot:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

#### <a name="cluster-deployment"></a>Fürttelepítés
Fürttelepítés nagy léptékű termelési helyzetekben használhatja. Az orchestrator, létrehozza a Kubernetes egy ACS-fürthöz. Az ACS-fürthöz kiterjeszthető a webszolgáltatás-hívás a nagyobb átviteli sebesség kezelésére.

A webszolgáltatás éles környezetben történő központi telepítéséhez először készítse elő a környezetben a következő parancsot:

```azurecli
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. eastus2] [-g [resource group]]
```

A fürt környezet setup parancs az előfizetésében hoz létre a következőket:
- Egy erőforráscsoport (Ha nincs megadva, vagy ha a megadott név nem létezik.)
- a storage-fiók
- Egy Azure-tárolót beállításjegyzék (ACR)
- Egy Azure tároló szolgáltatás (ACS) fürt Kubernetes központi telepítés
- Az Application insights-fiókot

>[!IMPORTANT]
> Fürtkörnyezetben sikeresen létrehozásához szüksége lesz az Azure-előfizetés vagy az erőforráscsoport közreműködői elérhető kell legyen.

Az erőforráscsoport, a tárfiók, valamint a ACR gyorsan hoz létre. Az ACS telepítési akár 20 percig is tarthat. 

Az egy folyamatban lévő fürtfelügyeleti kiépítési állapotának ellenőrzéséhez használja a következő parancsot:

```azurecli
az ml env show -n [environment name] -g [resource group]
```

A telepítő befejezése után a környezetben a központi telepítéshez használt be kell. Használja az alábbi parancsot:

```azurecli
az ml env set -n [environment name] -g [resource group]
```

>[!NOTE] 
> További telepítések esetén a környezet létrehozása után csak kell újra felhasználhatja a fenti set parancs használatával.
>

### <a name="create-a-model-management-account"></a>A modell felügyeleti fiók létrehozása
A modell felügyeleti fiókra akkor szükség a modellek telepítéséről. Meg kell megtennie egyszer előfizetésenként, és ugyanazt a fiókot, több telepítések esetén is felhasználhatja.

Hozzon létre egy új fiókot, használja a következő parancsot:

```azurecli
az ml account modelmanagement create -l [Azure region, e.g. eastus2] -n [your account name] -g [resource group name] --sku-instances [number of instances, e.g. 1] --sku-name [Pricing tier for example S1]
```

Egy meglévő fiókot használja, használja a következő parancsot:
```azurecli
az ml account modelmanagement set -n [your account name] -g [resource group it was created in]
```

### <a name="deploy-your-model"></a>A modell rendszerbe állítása
Most már készen áll a mentett modell webszolgáltatásként központi telepítése. 

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a sok minták a gyűjteményben.
