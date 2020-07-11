---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 27ae1d1a2c6309bdac2410dca4b48abf27d8ea0b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231981"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (konkrétan a Kubernetes Cloud Provider) identitást igényel további erőforrások, például terheléselosztó és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás *felügyelt identitás* vagy *egyszerű szolgáltatásnév*lehet. Ha [szolgáltatásnevet](kubernetes-service-principal.md)használ, meg kell adnia egy vagy AK-t az Ön nevében. Ha felügyelt identitást használ, a rendszer automatikusan létrehozza ezt. Az egyszerű szolgáltatásokat használó fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. A hitelesítő adatok elforgatása az Azure Active Directory alapértelmezett értékének megfelelően automatikusan megtörténik a 46 naponta. Az AK rendszerhez hozzárendelt és felhasználó által hozzárendelt felügyelt identitási típusokat használ. Ezek az identitások jelenleg nem változtathatók meg. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)ismertető témakört.

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

- Az Azure CLI, a 2.8.0 vagy újabb verzió

## <a name="limitations"></a>Korlátozások

* A saját felügyelt identitások használata jelenleg nem támogatott.
* A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők.
* A meglévő AK-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
* A fürt **frissítési** műveletei során a felügyelt identitás átmenetileg nem érhető el.

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összefoglalása

Az AK számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Név    | Használati eset | Alapértelmezett engedélyek | Saját identitás használata
|----------------------------|-----------|----------|
| Vezérlősík | nem látható | A hálózati erőforrások kezeléséhez, például a Load Balancer létrehozásához, a nyilvános IP-címekhez stb.| A csomópont-erőforráscsoport közreműködői szerepköre | Egyelőre nem támogatott
| Kubelet | AK-fürt neve – agentpool | Hitelesítés Azure Container Registry (ACR) | A csomópont-erőforráscsoport olvasói szerepköre | Egyelőre nem támogatott
| Bővítmény | AzureNPM | Nincs szükség identitásra | NA | Nem
| Bővítmény | AzureCNI-hálózat figyelése | Nincs szükség identitásra | NA | Nem
| Bővítmény | azurepolicy (forgalomirányító) | Nincs szükség identitásra | NA | Nem
| Bővítmény | azurepolicy | Nincs szükség identitásra | NA | Nem
| Bővítmény | Calico | Nincs szükség identitásra | NA | Nem
| Bővítmény | Irányítópult | Nincs szükség identitásra | NA | Nem
| Bővítmény | HTTPApplicationRouting | A szükséges hálózati erőforrások kezelése | A DNS-zónához tartozó csomópont-erőforráscsoport, közreműködői szerepkör olvasói szerepköre | Nem
| Bővítmény | Bejövő alkalmazások átjárója | A szükséges hálózati erőforrások kezelése| A csomópont-erőforráscsoport közreműködői szerepköre | Nem
| Bővítmény | omsagent | AK-metrikák küldésére szolgál Azure Monitor | A metrikák figyelése – közzétevői szerepkör | Nem
| Bővítmény | Virtuális csomópont (ACIConnector) | Azure Container Instances (ACI) szükséges hálózati erőforrásait kezeli | A csomópont-erőforráscsoport közreműködői szerepköre | Nem


## <a name="create-an-aks-cluster-with-managed-identities"></a>AK-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat egy AK-fürtöt a felügyelt identitásokkal a következő CLI-parancsok használatával.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AK-fürtöt:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

A felügyelt identitásokat használó sikeres fürtök az egyszerű szolgáltatás profiljának adatait tartalmazzák:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Az alábbi paranccsal lekérdezheti a vezérlési sík felügyelt identitásának ObjectId:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
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

## <a name="next-steps"></a>További lépések
* Felügyelt identitást használó fürtök létrehozásához használjon [Azure Resource Manager (ARM) sablonokat][aks-arm-template] .

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters
