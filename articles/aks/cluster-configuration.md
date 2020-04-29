---
title: Fürtkonfiguráció az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan konfigurálhat fürtöt az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479162"
---
# <a name="configure-an-aks-cluster"></a>AKS-fürt konfigurálása

Az AK-fürtök létrehozásának részeként előfordulhat, hogy a fürt konfigurációját testre kell szabnia az igényeinek megfelelően. Ez a cikk néhány lehetőséget mutat be az AK-fürt testreszabásához.

## <a name="os-configuration-preview"></a>Operációs rendszer konfigurációja (előzetes verzió)

Az AK mostantól támogatja az Ubuntu 18,04-as verziót a Node operációs rendszerként (OS) az előzetes verzióban. Az előzetes verzió ideje alatt az Ubuntu 16,04 és az Ubuntu 18,04 egyaránt elérhető.

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, a 2.2.0 vagy újabb verzió
- Az AK – előzetes verziójú 0.4.35 bővítmény

A következő Azure CLI-parancsokkal telepítheti a 0.4.35-bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

Regisztrálja `UseCustomizedUbuntuPreview` a szolgáltatást:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Konfigurálja a fürtöt az Ubuntu 18,04 használatára a fürt létrehozásakor. Az Ubuntu `--aks-custom-headers` 18,04 alapértelmezett operációs rendszerként való beállításához használja a jelzőt.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha egy normál Ubuntu 16,04-fürtöt szeretne létrehozni, ezt az egyéni `--aks-custom-headers` címke kihagyása alapján teheti meg.

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Ha Azure Kubernetes Service-fürtöt helyez üzembe az Azure-ban, a rendszer létrehoz egy második erőforráscsoportot a munkavégző csomópontokhoz. Alapértelmezés szerint az AK a csomópont erőforráscsoportot nevezi el `MC_resourcegroupname_clustername_location`, de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az AK – előzetes verziójú Azure CLI-bővítmény 0.3.2 vagy újabb verzióját. Az Azure CLI használatával a `--node-resource-group` `az aks create` parancs paraméterével adhatja meg az erőforráscsoport egyéni nevét. Ha Azure Resource Manager sablont használ egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a `nodeResourceGroup` tulajdonság használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében. Vegye figyelembe, hogy a fürt létrehozásakor csak az egyéni erőforráscsoport nevét lehet megadnia. 

A csomópont-erőforráscsoport használata során ne feledje, hogy a következők nem használhatók:

- Válasszon egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
- Eltérő előfizetést kell megadni a csomópont-erőforráscsoport számára.
- Módosítsa a csomópont-erőforráscsoport nevét a fürt létrehozása után.
- A csomópont erőforráscsoporthoz tartozó felügyelt erőforrások nevének megadása.
- Módosíthatja vagy törölheti az Azure-ban létrehozott, felügyelt erőforrások címkéit a csomópont-erőforráscsoporton belül.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan `Kured` [alkalmazhatja a biztonsági és a kernel-frissítéseket a fürt Linux-csomópontjaira](node-updates-kured.md) .
- Lásd: [Azure Kubernetes Service-(ak-) fürt frissítése](upgrade-cluster.md) , amelyből megtudhatja, hogyan frissítheti a fürtöt a Kubernetes legújabb verziójára.
- Tekintse meg az [AK-val kapcsolatos gyakori kérdések](faq.md) listáját, ahol válaszokat talál a gyakori AK-kérdésekre.