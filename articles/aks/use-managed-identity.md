---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja a felügyelt identitásokat az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 7a71d3bd70d97df884f1bc962c0ef9897d7fd2cb
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024404"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (AKS) fürt (különösen a Kubernetes felhőszolgáltató) identitást igényel további erőforrások, például terheléselosztók és felügyelt lemezek létrehozásához az Azure-ban, ez az identitás lehet *felügyelt identitás* vagy *egyszerű szolgáltatás.* Ha egyszerű [szolgáltatást](kubernetes-service-principal.md)használ, meg kell adnia egyet, vagy az AKS létrehoz egyet az Ön nevében. Ha felügyelt identitást használ, az AKS automatikusan létrehozza ezt. A szolgáltatásnévi tagokat használó fürtök végül elérik azt az állapotot, amelyben a szolgáltatásegyszerű szolgáltatást meg kell újítani a fürt működésének fenntartásához. A szolgáltatásegyszerű szolgáltatások kezelése összetettséget ad hozzá, ezért egyszerűbb a felügyelt identitások használata. Ugyanazok az engedélykövetelmények vonatkoznak a szolgáltatásnévi és a felügyelt identitásokra is.

*A felügyelt identitások* lényegében a szolgáltatástagok köré, és egyszerűbbé teszik a felügyeletüket. További információ: [Azure-erőforrások felügyelt identitások.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az AKS két felügyelt identitást hoz létre:

- **Rendszer által hozzárendelt felügyelt identitás**: Az identitás, amely a Kubernetes felhőszolgáltató használja az Azure-erőforrások létrehozásához a felhasználó nevében. A rendszer által hozzárendelt identitás életciklusa a fürt életciklusához van kötve. Az identitás törlődik a fürt törlésekor.
- **Felhasználó által hozzárendelt felügyelt identitás**: A fürtben az engedélyezéshez használt identitás. Például a felhasználó által kijelölt identitás segítségével engedélyezheti az AKS-t az Azure Container Regisz-nyilvántartások (AR) használatára, vagy a kubelet engedélyezésére metaadatok azure-ból való lekérni.

A bővítmények felügyelt identitással is hitelesítik magukat. Minden bővítményhez az AKS felügyelt identitást hoz létre, és a bővítmény élettartamára tart. 

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

> [!NOTE]
> Saját virtuális hálózat, statikus IP-cím vagy csatlakoztatott Azure-lemez létrehozásához és használatához, ahol az erőforrások kívül esnek a MC_* erőforráscsoporton, használja a fürtrendszer felügyelt identitásának Rendszerazonosítóját egy szerepkör-hozzárendelés végrehajtásához. A szerepkör-hozzárendeléssel kapcsolatos további információkért [lásd: Hozzáférés delegálása más Azure-erőforrásokhoz.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

Végül a fürt höz való hozzáféréshez hitelesítő adatokat szerezzen be:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

A fürt néhány percen belül létrejön. Ezután üzembe helyezheti az alkalmazás-számítási feladatokat az új fürtre, és ugyanúgy kezelheti azt, ahogy azt a szolgáltatásegyszerű alapú AKS-fürtökkel tette.

> [!IMPORTANT]
>
> - A felügyelt identitással rendelkező AKS-fürtök csak a fürt létrehozása során engedélyezhetők.
> - A meglévő AKS-fürtök nem frissíthetők és nem frissíthetők a felügyelt identitások engedélyezéséhez.
