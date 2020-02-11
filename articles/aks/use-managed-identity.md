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
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121429"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Előzetes verzió – felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (pontosabban a Kubernetes Cloud Provider) megköveteli, hogy az *egyszerű szolgáltatás* olyan további erőforrásokat hozzon létre, mint a terheléselosztó és a felügyelt lemezek az Azure-ban. Meg kell adnia egy egyszerű szolgáltatásnevet, vagy az AK-t az Ön nevében. Az egyszerű szolgáltatások általában lejárati dátummal rendelkeznek. A fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)ismertető témakört.

Az AK két felügyelt identitást hoz létre:

- **Rendszer által hozzárendelt felügyelt identitás**: az az identitás, amelyet a Kubernetes a felhasználó nevében az Azure-erőforrások létrehozásához használ. A rendszer által hozzárendelt identitás életciklusa a fürthöz van kötve. A rendszer törli az identitást a fürt törlésekor.
- **Felhasználó által hozzárendelt felügyelt identitás**: az engedélyezéshez használt identitás a fürtben. A felhasználó által hozzárendelt identitás például feljogosítja az AK-t a hozzáférés-vezérlési rekordok (ACR-EK) használatára, vagy ha engedélyezi a kubelet számára, hogy metaadatokat szerezzen az Azure-ból.

Ebben az előzetes verzióban még egy egyszerű szolgáltatásnév szükséges. Ez a bővítmények, például a figyelés, a virtuális csomópontok, a Azure Policy és a HTTP-alkalmazások útválasztásának engedélyezéséhez használatos. Folyamatban van a bővítmények függőségének eltávolítása az egyszerű szolgáltatásnév (SPN) nevében. Végül az AK-ban lévő egyszerű szolgáltatásnév követelménye teljesen el lesz távolítva.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A kétrészes előzetes verziókra az ügyfélszolgálat a lehető leghatékonyabban vonatkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásokat kell telepítenie:

- Az Azure CLI, 2.0.70 vagy újabb verzió
- Az AK – előzetes verziójú 0.4.14 bővítmény

A következő Azure CLI-parancsokkal telepítheti a 0.4.14-bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Miután regisztrált egy szolgáltatást egy előfizetéshez, jelenleg nem tudja törölni a szolgáltatást. Az előzetes verziójú funkciók engedélyezésekor az alapértelmezett beállítások az előfizetésben később létrehozott AK-fürtökhöz is használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Ehelyett használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse a `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>AK-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat egy AK-fürtöt a felügyelt identitásokkal a következő CLI-parancsok használatával.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AK-fürtöt:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Végül kapjon hitelesítő adatokat a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

A fürtöt néhány percen belül létrehozza a rendszer. Ezután üzembe helyezheti az alkalmazás számítási feladatait az új fürtön, és ugyanúgy kezelheti, mint a Service-Principal-alapú AK-fürtökkel.

> [!IMPORTANT]
>
> - A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők.
> - A meglévő AK-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
