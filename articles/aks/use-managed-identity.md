---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: e96126d1516e8a1e20e6f6db9b3a448b94c71cd7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050595"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (konkrétan a Kubernetes Cloud Provider) identitást igényel további erőforrások, például terheléselosztó és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás *felügyelt identitás* vagy *egyszerű szolgáltatásnév*lehet. Ha [szolgáltatásnevet](kubernetes-service-principal.md)használ, meg kell adnia egy vagy AK-t az Ön nevében. Ha felügyelt identitást használ, a rendszer automatikusan létrehozza ezt. Az egyszerű szolgáltatásokat használó fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. A hitelesítő adatok elforgatása az Azure Active Directory alapértelmezett értékének megfelelően automatikusan megtörténik a 46 naponta. Az AK rendszerhez hozzárendelt és felhasználó által hozzárendelt felügyelt identitási típusokat használ. Ezek az identitások jelenleg nem változtathatók meg. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/overview.md)ismertető témakört.

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

- Az Azure CLI, a 2.8.0 vagy újabb verzió

## <a name="limitations"></a>Korlátozások

* A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők.
* A meglévő AK-fürtök nem telepíthetők át felügyelt identitásokra.
* A fürt **frissítési** műveletei során a felügyelt identitás átmenetileg nem érhető el.
* A felügyelt identitást engedélyező fürtök áthelyezési/áttelepíti a bérlők nem támogatottak.

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összefoglalása

Az AK számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Name    | Használati eset | Alapértelmezett engedélyek | Saját identitás használata
|----------------------------|-----------|----------|
| Vezérlősík | nem látható | Az AK által használt felügyelt hálózati erőforrások, beleértve a bejövő terheléselosztó és az AK által felügyelt nyilvános IP-címek | A csomópont-erőforráscsoport közreműködői szerepköre | Előnézet
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
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

A felügyelt identitásokat használó sikeres fürtök az egyszerű szolgáltatás profiljának adatait tartalmazzák:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Az alábbi paranccsal lekérdezheti a vezérlési sík felügyelt identitásának ObjectId:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Miután létrehozta a fürtöt, az alkalmazás számítási feladatait telepítheti az új fürtre, és ugyanúgy kezelheti azt, mint a Service-Principal-alapú AK-fürtökkel.

> [!NOTE]
> Saját VNet, statikus IP-cím vagy csatolt Azure-lemez létrehozásához és használatához, ahol az erőforrások a munkavégző csomópont-erőforráscsoporton kívül esnek, a szerepkör-hozzárendelés végrehajtásához használja a fürthöz rendelt felügyelt identitás PrincipalID. A szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [hozzáférés delegálása más Azure-erőforrásokhoz](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Az engedélyek az Azure Cloud Provider által használt fürtözött felügyelt identitáshoz való jogosultsága akár 60 percet is igénybe vehet.

Végül kapjon hitelesítő adatokat a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Saját vezérlési sík használata (előzetes verzió)
Az egyéni vezérlő sík identitása lehetővé teszi, hogy a fürt létrehozása előtt hozzáférést biztosítson a meglévő identitáshoz. Ez olyan forgatókönyveket tesz lehetővé, mint például egy egyéni VNET vagy egy felügyelt identitással rendelkező UDR outboundType használata.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói az önkiszolgáló, a választható lehetőségek alapján érhetők el. Az előzetes verziók az "adott állapotban" és "elérhetőként" jelennek meg, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A (z) és az ügyfél-támogatási szolgálatok a lehető leghatékonyabban fedezik az előzetes verziókat. Ezért ezeket a funkciókat nem éles használatra szánták. További információkért lásd a következő támogatási cikkeket:
>
> - [AK-támogatási szabályzatok](support-policies.md)
> - [Azure-támogatás – gyakori kérdések](faq.md)

A következő erőforrásokat kell telepítenie:
- Az Azure CLI, 2.9.0 vagy újabb verzió
- Az AK – előzetes verziójú 0.4.57 bővítmény

A saját vezérlő síkja (előzetes verzió) használatának korlátai:
* A Azure Government jelenleg nem támogatott.
* Az Azure China 21Vianet jelenleg nem támogatott.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Ha még nem rendelkezik felügyelt identitással, folytassa a következő lépéssel, és hozzon létre egyet például az [az Identity CLI][az-identity-create]használatával.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Az eredménynek a következőhöz hasonlóan kell kinéznie:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Ha a felügyelt identitás része az előfizetésnek, akkor az [az Identity CLI paranccsal][az-identity-list] kérdezheti le.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Most a következő paranccsal hozhatja létre a fürtöt a meglévő identitásával:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

A saját felügyelt identitások használatával történő sikeres fürtök a userAssignedIdentities-profil adatait tartalmazzák:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>További lépések
* Felügyelt identitást használó fürtök létrehozásához használjon [Azure Resource Manager (ARM) sablonokat][aks-arm-template] .

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: https://docs.microsoft.com/cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-list]: https://docs.microsoft.com/cli/azure/identity?view=azure-cli-latest#az-identity-list
