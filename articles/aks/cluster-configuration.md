---
title: Fürtkonfiguráció az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan konfigurálhat fürtöt az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: conceptual
ms.date: 06/20/2020
ms.openlocfilehash: 43aadd52f17367b488fcec086404caaba9158f33
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205775"
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

Regisztrálja a `UseCustomizedUbuntuPreview` szolgáltatást:

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

### <a name="new-clusters"></a>Új fürtök

Konfigurálja a fürtöt az Ubuntu 18,04 használatára a fürt létrehozásakor. Az `--aks-custom-headers` Ubuntu 18,04 alapértelmezett operációs rendszerként való beállításához használja a jelzőt.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha egy normál Ubuntu 16,04-fürtöt szeretne létrehozni, ezt az egyéni címke kihagyása alapján teheti meg `--aks-custom-headers` .

### <a name="existing-clusters"></a>Meglévő fürtök

Konfigurálja az új csomópont-készletet az Ubuntu 18,04 használatára. Használja a `--aks-custom-headers` jelzőt az Ubuntu 18,04 alapértelmezett operációs rendszerként való beállításához az adott csomópont-készlethez.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha egy normál Ubuntu 16,04 Node-készletet szeretne létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` .

## <a name="generation-2-virtual-machines-preview"></a>2. generációs virtuális gépek (előzetes verzió)
Az Azure támogatja a [2. generációs (Gen2) virtuális gépeket (VM)](../virtual-machines/windows/generation-2.md). A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépeken (Gen1) nem támogatott főbb funkciókat. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM).

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett.
A Gen2 virtuális gépeket csak a megadott SKU-és méretek támogatják. Tekintse át a [támogatott méretek listáját](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes), és ellenőrizze, hogy az SKU támogatja vagy igényli-e a Gen2.

Emellett nem minden virtuálisgép-lemezkép támogatja a Gen2-t, az AK-Gen2 virtuális gépeken az új [AK Ubuntu 18,04-lemezképet](#os-configuration-preview)fogja használni. Ez a rendszerkép az összes Gen2 SKU-t és méretet támogatja.

Ha az előzetes verzióban szeretné használni a Gen2 virtuális gépeket, a következőkre lesz szüksége:
- A `aks-preview` CLI-bővítmény telepítve van.
- A `Gen2VMPreview` szolgáltatás jelzője regisztrálva van.

Regisztrálja a `Gen2VMPreview` szolgáltatást:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

A következő Azure CLI-parancsokkal telepítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension add --name aks-preview
```

A következő Azure CLI-parancsokkal frissítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension update --name aks-preview
```

### <a name="new-clusters"></a>Új fürtök
Konfigurálja úgy a fürtöt, hogy Gen2 virtuális gépeket használjon a kiválasztott SKU-hoz a fürt létrehozásakor. A `--aks-custom-headers` jelzővel beállíthatja, hogy a Gen2 a virtuális gép létrehozásakor egy új fürtön legyen.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Ha az 1. generációs (Gen1) virtuális gépek használatával szeretne normál fürtöt létrehozni, ezt az egyéni címke kihagyása mellett teheti meg `--aks-custom-headers` . Azt is megteheti, hogy az alábbi módon további Gen1 vagy Gen2 virtuális gépeket ad hozzá.

### <a name="existing-clusters"></a>Meglévő fürtök
Konfiguráljon egy új csomópont-készletet a Gen2 virtuális gépek használatára. A `--aks-custom-headers` jelzővel állítsa be a Gen2 virtuálisgép-generációként az adott csomópont-készlethez.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Ha normál Gen1-csomópont-készleteket szeretne létrehozni, ezt az egyéni címke kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Ha Azure Kubernetes Service-fürtöt helyez üzembe az Azure-ban, a rendszer létrehoz egy második erőforráscsoportot a munkavégző csomópontokhoz. Alapértelmezés szerint az AK a csomópont erőforráscsoportot nevezi el `MC_resourcegroupname_clustername_location` , de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az AK – előzetes verziójú Azure CLI-bővítmény 0.3.2 vagy újabb verzióját. Az Azure CLI használatával a `--node-resource-group` parancs paraméterével `az aks create` adhatja meg az erőforráscsoport egyéni nevét. Ha Azure Resource Manager sablont használ egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a `nodeResourceGroup` tulajdonság használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében. Az egyéni erőforráscsoport neve csak a fürt létrehozásakor adható meg. 

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
