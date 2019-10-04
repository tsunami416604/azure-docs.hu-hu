---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827545"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Előzetes verzió – felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg a felhasználóknak meg kell adniuk egy egyszerű szolgáltatásnevet, vagy az AK-t az Ön nevében, hogy az AK-fürt (különösen a Kubernetes Cloud Provider) olyan további erőforrásokat hozzon létre, mint például a terheléselosztó és a felügyelt lemezek az Azure-ban. Az egyszerű szolgáltatások általában lejárati dátummal jönnek létre. A fürtök végül olyan állapotba kerülnek, amelyben a szolgáltatásnevet meg kell újítani, ellenkező esetben a fürt nem fog működni. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít. A felügyelt identitások lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. További információ a [felügyelt identitásokról](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .

Az AK két felügyelt identitást hoz létre, amelyek egy rendszerhez rendelt felügyelt identitást és a másik felhasználó által hozzárendelt identitást alkotnak. A kubernetes Cloud Provider egy rendszerhez rendelt felügyelt identitást használ az Azure-erőforrások létrehozásához a felhasználó nevében. A rendszerhez rendelt felügyelt identitás életciklusa a fürthöz van kötve, és a fürt törlése után törlődik. Az AK létrehoz egy felhasználóhoz rendelt felügyelt identitást is, amely a fürtben a ACR-EK elérésére, az Azure-ból származó metaadatok beszerzésére és a kubelet való hozzáférés engedélyezésére szolgál.

Ebben az előzetes verzióban még egy egyszerű szolgáltatásnév szükséges. Ez a bővítmények, például a figyelés, a virtuális csomópont, az Azure-házirend és a http-alkalmazás útválasztása engedélyezésére szolgál. Folyamatban van a bővítmények függőségének eltávolítása az SPN-ben, és végül az AK-ban lévő SPN-re vonatkozó követelmény teljesen el lesz távolítva.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok](support-policies.md)
> * [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

A következőkkel kell rendelkeznie:

* Szüksége lesz az Azure CLI 2.0.70 vagy újabb verziójára, valamint az AK-előnézet 0.4.14-bővítményre

## <a name="install-latest-aks-cli-preview-extension"></a>A legújabb AK CLI előnézet bővítmény telepítése

Szüksége lesz az **AK-előnézet 0.4.14-** bővítményre vagy újabb verzióra.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

A *regisztrált*állapot megjelenítéséhez több percet is igénybe vehet. A regisztrációs állapotot az [az Feature List] [az-Feature-List] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva van, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az [az Provider Register] [az-Provider-Register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>AK-fürt létrehozása felügyelt identitással

Most már létrehozhat egy AK-fürtöt a felügyelt identitásokkal a következő CLI-parancs használatával
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Hitelesítő adatok beszerzése a fürt eléréséhez
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Ha a fürtöt néhány percen belül létrehozták, üzembe helyezheti az alkalmazás számítási feladatait, és használhatja azt, ahogy az egyszerű szolgáltatásnév-fürtökkel rendelkezik. 

> [!IMPORTANT]
> * A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők
> * A meglévő AK-fürtök nem frissíthetők/nem frissíthetők a felügyelt identitások engedélyezéséhez