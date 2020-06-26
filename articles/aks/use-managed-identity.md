---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/04/2020
ms.author: mlearned
ms.openlocfilehash: 1a5069259e631d1cc33aeebc56164d8407b9ca6c
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413586"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (konkrétan a Kubernetes Cloud Provider) identitást igényel további erőforrások, például terheléselosztó és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás *felügyelt identitás* vagy *egyszerű szolgáltatásnév*lehet. Ha [szolgáltatásnevet](kubernetes-service-principal.md)használ, meg kell adnia egy vagy AK-t az Ön nevében. Ha felügyelt identitást használ, a rendszer automatikusan létrehozza ezt. Az egyszerű szolgáltatásokat használó fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. A hitelesítő adatok elforgatása az Azure Active Directory alapértelmezett értékének megfelelően automatikusan megtörténik a 46 naponta. Az AK rendszerhez hozzárendelt és felhasználó által hozzárendelt felügyelt identitási típusokat használ. Ezek az identitások jelenleg nem változtathatók meg. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)ismertető témakört.

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

- Az Azure CLI, a 2.2.0 vagy újabb verzió

## <a name="limitations"></a>Korlátozások

* A saját felügyelt identitások használata jelenleg nem támogatott.
* A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők.
* A meglévő AK-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
* A fürt **frissítési** műveletei során a felügyelt identitás átmenetileg nem érhető el.

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összefoglalása

Az AK számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Name    | Használati eset | Alapértelmezett engedélyek | Saját identitás használata
|----------------------------|-----------|----------|
| Vezérlő síkja | nem látható | A hálózati erőforrások kezeléséhez, például a Load Balancer létrehozásához, a nyilvános IP-címekhez stb.| A csomópont-erőforráscsoport közreműködői szerepköre | Egyelőre nem támogatott
| Kubelet | AK-fürt neve – agentpool | Hitelesítés Azure Container Registry (ACR) | A csomópont-erőforráscsoport olvasói szerepköre | Egyelőre nem támogatott
| Bővítmény | AzureNPM | Nincs szükség identitásra | NA | No
| Bővítmény | AzureCNI-hálózat figyelése | Nincs szükség identitásra | NA | No
| Bővítmény | azurepolicy (forgalomirányító) | Nincs szükség identitásra | NA | No
| Bővítmény | azurepolicy | Nincs szükség identitásra | NA | No
| Bővítmény | Calico | Nincs szükség identitásra | NA | No
| Bővítmény | Irányítópult | Nincs szükség identitásra | NA | No
| Bővítmény | HTTPApplicationRouting | A szükséges hálózati erőforrások kezelése | A DNS-zónához tartozó csomópont-erőforráscsoport, közreműködői szerepkör olvasói szerepköre | No
| Bővítmény | Bejövő alkalmazások átjárója | A szükséges hálózati erőforrások kezelése| A csomópont-erőforráscsoport közreműködői szerepköre | No
| Bővítmény | omsagent | AK-metrikák küldésére szolgál Azure Monitor | A metrikák figyelése – közzétevői szerepkör | No
| Bővítmény | Virtuális csomópont (ACIConnector) | Azure Container Instances (ACI) szükséges hálózati erőforrásait kezeli | A csomópont-erőforráscsoport közreműködői szerepköre | No


## <a name="create-an-aks-cluster-with-managed-identities"></a>AK-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat egy AK-fürtöt a felügyelt identitásokkal a következő CLI-parancsok használatával.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AK-fürtöt:

```azurecli-interactive
az aks create -g myResourceGroup -n MyManagedCluster --enable-managed-identity
```

A felügyelt identitásokat használó sikeres fürtök az egyszerű szolgáltatás profiljának adatait tartalmazzák:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

> [!NOTE]
> Saját VNet, statikus IP-cím vagy csatolt Azure-lemez létrehozásához és használatához, ahol az erőforrások a munkavégző csomópont-erőforráscsoporton kívül esnek, a szerepkör-hozzárendelés végrehajtásához használja a fürthöz rendelt felügyelt identitás PrincipalID. A szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [hozzáférés delegálása más Azure-erőforrásokhoz](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Az engedélyek az Azure Cloud Provider által használt fürtözött felügyelt identitáshoz való jogosultsága akár 60 percet is igénybe vehet.

Végül kapjon hitelesítő adatokat a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

A fürtöt néhány percen belül létrehozza a rendszer. Ezután üzembe helyezheti az alkalmazás számítási feladatait az új fürtön, és ugyanúgy kezelheti, mint a Service-Principal-alapú AK-fürtökkel.
