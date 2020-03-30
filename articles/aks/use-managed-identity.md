---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja a felügyelt identitásokat az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369973"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes-szolgáltatás (AKS) fürt (különösen a Kubernetes felhőszolgáltató) igényel egy *egyszerű szolgáltatás* hoz létre további erőforrásokat, például a terheléselosztók és a felügyelt lemezek az Azure-ban. Vagy meg kell adnia egy egyszerű szolgáltatás, vagy az AKS létrehoz egyet az Ön nevében. A szolgáltatásnévi tagok nak általában lejárati dátuma van. A fürtök végül elérik azt az állapotot, amelyben a szolgáltatásegyszerű szolgáltatást meg kell újítani a fürt működésének fenntartásához. A szolgáltatásnévi tagok kezelése összetetté teszi a szolgáltatást.

*A felügyelt identitások* lényegében a szolgáltatástagok köré, és egyszerűbbé teszik a felügyeletüket. További információ: [Azure-erőforrások felügyelt identitások.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az AKS két felügyelt identitást hoz létre:

- **Rendszer által hozzárendelt felügyelt identitás**: Az identitás, amely a Kubernetes felhőszolgáltató használja az Azure-erőforrások létrehozásához a felhasználó nevében. A rendszer által hozzárendelt identitás életciklusa a fürt életciklusához van kötve. Az identitás törlődik a fürt törlésekor.
- **Felhasználó által hozzárendelt felügyelt identitás**: A fürtben az engedélyezéshez használt identitás. Például a felhasználó által kijelölt identitás segítségével engedélyezheti az AKS-t az Azure Container Regisz-nyilvántartások (AR) használatára, vagy a kubelet engedélyezésére metaadatok azure-ból való lekérni.

A bővítmények felügyelt identitással is hitelesítik magukat. Minden bővítményhez az AKS felügyelt identitást hoz létre, és a bővítmény élettartamára tart. Saját virtuális hálózat, statikus IP-cím vagy csatlakoztatott Azure-lemez létrehozásához és használatához, ahol az erőforrások kívül esnek a MC_* erőforráscsoporton, használja a fürt Rendszerazonosítóját egy szerepkör-hozzárendelés végrehajtásához. A szerepkör-hozzárendeléssel kapcsolatos további információkért [lásd: Hozzáférés delegálása más Azure-erőforrásokhoz.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrással kell rendelkeznie:

- Az Azure CLI 2.2.0-s vagy újabb verziója

## <a name="create-an-aks-cluster-with-managed-identities"></a>Felügyelt identitásokkal rendelkező AKS-fürt létrehozása

Most már létrehozhat egy Felügyelt identitású AKS-fürtöt a következő CLI-parancsokkal.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AKS-fürtöt:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

A felügyelt identitások használatával sikeres fürtlétrehozás a szolgáltatásegyszerű profiladatait tartalmazza:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Végül a fürt höz való hozzáféréshez hitelesítő adatokat szerezzen be:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

A fürt néhány percen belül létrejön. Ezután üzembe helyezheti az alkalmazás-számítási feladatokat az új fürtre, és ugyanúgy kezelheti azt, ahogy azt a szolgáltatásegyszerű alapú AKS-fürtökkel tette.

> [!IMPORTANT]
>
> - A felügyelt identitással rendelkező AKS-fürtök csak a fürt létrehozása során engedélyezhetők.
> - A meglévő AKS-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
