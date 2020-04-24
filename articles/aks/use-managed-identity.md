---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112966"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (konkrétan a Kubernetes Cloud Provider) identitást igényel további erőforrások, például terheléselosztó és felügyelt lemezek létrehozásához az Azure-ban, ez az identitás *felügyelt identitás* vagy *egyszerű szolgáltatásnév*lehet. Ha [szolgáltatásnevet](kubernetes-service-principal.md)használ, meg kell adnia egy vagy AK-t az Ön nevében. Ha felügyelt identitást használ, a rendszer automatikusan létrehozza ezt. Az egyszerű szolgáltatásokat használó fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)ismertető témakört.

Az AK két felügyelt identitást hoz létre:

- **Rendszer által hozzárendelt felügyelt identitás**: az az identitás, amelyet a Kubernetes a felhasználó nevében az Azure-erőforrások létrehozásához használ. A rendszer által hozzárendelt identitás életciklusa a fürthöz van kötve. A rendszer törli az identitást a fürt törlésekor.
- **Felhasználó által hozzárendelt felügyelt identitás**: az engedélyezéshez használt identitás a fürtben. A felhasználó által hozzárendelt identitás például feljogosítja az AK-t az Azure Container-jegyzékek (ACR-EK) használatára, vagy a kubelet engedélyezéséhez az Azure-ból származó metaadatok beszerzéséhez.

A bővítmények felügyelt identitás használatával is hitelesíthetők. Az egyes bővítmények esetében a felügyelt identitást az AK hozza létre, és a bővítmény élettartamára tart. 

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

- Az Azure CLI, a 2.2.0 vagy újabb verzió

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

A felügyelt identitásokat használó sikeres fürtök az egyszerű szolgáltatás profiljának adatait tartalmazzák:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Saját VNet, statikus IP-cím vagy csatolt Azure-lemez létrehozásához és használatához, ahol az erőforrások a MC_ * erőforráscsoporthoz tartoznak, a szerepkör-hozzárendelés végrehajtásához használja a fürthöz rendelt felügyelt identitás PrincipalID. A szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [hozzáférés delegálása más Azure-erőforrásokhoz](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Az engedélyek az Azure Cloud Provider által használt fürtözött felügyelt identitáshoz való jogosultsága akár 60 percet is igénybe vehet.

Végül kapjon hitelesítő adatokat a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

A fürtöt néhány percen belül létrehozza a rendszer. Ezután üzembe helyezheti az alkalmazás számítási feladatait az új fürtön, és ugyanúgy kezelheti, mint a Service-Principal-alapú AK-fürtökkel.

> [!IMPORTANT]
>
> - A felügyelt identitásokkal rendelkező AK-fürtök csak a fürt létrehozásakor engedélyezhetők.
> - A meglévő AK-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
