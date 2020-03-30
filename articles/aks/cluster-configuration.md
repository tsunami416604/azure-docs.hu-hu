---
title: Fürtkonfiguráció az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg, hogyan konfigurálhat fürtöt az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479162"
---
# <a name="configure-an-aks-cluster"></a>AKS-fürt konfigurálása

Az AKS-fürt létrehozásának részeként szükség lehet a fürt konfigurációjának testreszabására az igényeinek megfelelően. Ez a cikk néhány lehetőséget mutat be az AKS-fürt testreszabására.

## <a name="os-configuration-preview"></a>Operációs rendszer konfigurációja (előzetes verzió)

Az AKS mostantól támogatja az Ubuntu 18.04 operációs rendszert előnézetben. Az előnézeti időszak alatt mind az Ubuntu 16.04, mind az Ubuntu 18.04 elérhető.

A következő erőforrásokat kell telepítenie:

- Az Azure CLI 2.2.0-s vagy újabb verziója
- Az aks-preview 0.4.35 kiterjesztés

Az aks-preview 0.4.35-ös vagy újabb verzió telepítéséhez használja a következő Azure CLI-parancsokat:

```azurecli
az extension add --name aks-preview
az extension list
```

Regisztrálja `UseCustomizedUbuntuPreview` a funkciót:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Az állapot **regisztrált**állapotként való megjelenítése több percig is eltarthat. A regisztrációs állapotot az [az szolgáltatáslista](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) paranccsal ellenőrizheti:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztráltként jelenik `Microsoft.ContainerService` meg, frissítse az erőforrás-szolgáltató regisztrációját az [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Állítsa be a fürtöt úgy, hogy az Ubuntu 18.04-et használja a fürt létrehozásakor. Használja `--aks-custom-headers` a jelzőt az Ubuntu 18.04 alapértelmezett operációs rendszerként való beállításához.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha rendszeres Ubuntu 16.04 fürtöt szeretne létrehozni, ezt az egyéni `--aks-custom-headers` címke elhagyásával teheti meg.

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Amikor egy Azure Kubernetes-szolgáltatásfürtöt telepít az Azure-ban, egy második erőforráscsoport jön létre a feldolgozócsomópontok hoz létre. Alapértelmezés szerint az AKS elnevezi `MC_resourcegroupname_clustername_location`a csomópont erőforráscsoportot, de megis adja a saját nevét.

Saját erőforráscsoport nevének megadásához telepítse az aks-preview Azure CLI bővítmény 0.3.2-es vagy újabb verzióját. Az Azure CLI használatával `--node-resource-group` a `az aks create` parancs paraméterével adja meg az erőforráscsoport egyéni nevét. Ha egy AKS-fürt üzembe helyezéséhez egy Azure Resource Manager-sablont `nodeResourceGroup` használ, megadhatja az erőforráscsoport nevét a tulajdonság használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

A másodlagos erőforráscsoportot automatikusan létrehozza az Azure-erőforrás-szolgáltató a saját előfizetésében. Ne feledje, hogy az egyéni erőforráscsoport nevét csak a fürt létrehozásakor adhatja meg. 

A csomópont erőforráscsoportnal végzett munka közben ne feledje, hogy a következők nem tehető:

- Adjon meg egy meglévő erőforráscsoportot a csomópont erőforráscsoporthoz.
- Adjon meg egy másik előfizetést a csomópont erőforráscsoporthoz.
- Módosítsa a csomópont erőforráscsoportjának nevét a fürt létrehozása után.
- Adja meg a csomóponterőforrás-csoporton belüli felügyelt erőforrások nevét.
- Módosítsa vagy törölje a felügyelt erőforrások Azure által létrehozott címkéit a csomóponterőforrás-csoporton belül.

## <a name="next-steps"></a>További lépések

- Ismerje meg, `Kured` hogyan [alkalmazhat biztonsági és kernelfrissítéseket linuxos csomópontokra](node-updates-kured.md) a fürtben.
- Tekintse [meg az Azure Kubernetes-szolgáltatás (AKS) fürt frissítése című témakört,](upgrade-cluster.md) amelyből megtudhatja, hogyan frissítheti a fürtöt a Kubernetes legújabb verziójára.
- Tekintse meg az [AKS-sel kapcsolatos gyakori kérdések](faq.md) listáját, hogy választ találjon néhány gyakori AKS-kérdésre.