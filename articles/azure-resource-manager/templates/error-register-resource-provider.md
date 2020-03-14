---
title: Erőforrás-szolgáltató regisztrációs hibái
description: Ismerteti, Hogyan oldhatók fel az Azure erőforrás-szolgáltató regisztrációs hibái az erőforrások Azure Resource Manager használatával történő telepítésekor.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273773"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Javítsa ki a hibákat az erőforrás-szolgáltatói regisztrációt

Ez a cikk ismerteti a hibák jelentkezhetnek, ha korábban még nem használta az előfizetés erőforrás-szolgáltató használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Erőforrások üzembe helyezésekor, a következő hibakóddal és üzenet jelenhet meg:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Vagy egy hasonló üzenet jelenhet meg:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

A következő hibaüzenet jelenik a támogatott helyek és az API-verziók javaslatok adjon meg. A sablon a javasolt értékeket módosíthatja. A legtöbb szolgáltatók a következők: automatikusan, az Azure portal vagy a parancssori felület használata regisztrált, de nem minden. Ha még nem használta az egy adott erőforrás-szolgáltató előtt, szükség lehet, hogy a szolgáltató regisztrálásához.

Vagy ha letiltja a virtuális gépek automatikus leállítását, a következőhöz hasonló hibaüzenet jelenhet meg:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Ok

Ezeket a hibákat a következő okok egyike miatt kapja meg:

* A szükséges erőforrás-szolgáltató nincs regisztrálva az előfizetéséhez
* Az erőforrástípus esetében nem támogatott API-verzió
* Az erőforrástípus esetében nem támogatott helye
* A virtuális gépek automatikus leállításához regisztrálni kell a Microsoft. segédösszetevője erőforrás-szolgáltatót.

## <a name="solution-1---powershell"></a>Megoldás 1 – PowerShell

A PowerShell esetében a **Get-AzResourceProvider** használatával tekintheti meg a regisztrációs állapotát.

```powershell
Get-AzResourceProvider -ListAvailable
```

A szolgáltató regisztrálásához használja a **Register-AzResourceProvider** nevet, és adja meg a regisztrálni kívánt erőforrás-szolgáltató nevét.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

A támogatott helyek egy adott típusú erőforrás használja:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

A támogatott API-verziók egy adott típusú erőforrás használja:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>2 – Azure CLI megoldás

Ha szeretné megtekinteni, hogy a szolgáltató regisztrálva van-e, használja a `az provider list` parancsot.

```azurecli-interactive
az provider list
```

Erőforrás-szolgáltató regisztrálásához használja a `az provider register` parancsot, és határozza meg a regisztrálni kívánt *névteret* .

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

A támogatott helyek és a egy erőforrástípus API-verzióit használja:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>3 – Azure portal megoldás

Tekintse meg a regisztrációs állapotát, és regisztrálja a portálon keresztül egy erőforrás-szolgáltató névtere.

1. A portálon válassza a **minden szolgáltatás**lehetőséget.

   ![Válassza ki az összes szolgáltatás](./media/error-register-resource-provider/select-all-services.png)

1. Válassza az **Előfizetések** lehetőséget.

   ![Keresés az előfizetések között](./media/error-register-resource-provider/select-subscriptions.png)

1. Előfizetések listájából válassza ki a kívánt előfizetést, az erőforrás-szolgáltató regisztrálásához.

   ![Válassza ki az előfizetést az erőforrás-szolgáltató regisztrálása](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Az előfizetéséhez válassza az **erőforrás-szolgáltatók**lehetőséget.

   ![Válassza ki az erőforrás-szolgáltatók](./media/error-register-resource-provider/select-resource-provider.png)

1. Tekintse meg az erőforrás-szolgáltatók listáját, és szükség esetén válassza a **regisztrálás** hivatkozást a telepíteni kívánt típus erőforrás-szolgáltatójának regisztrálásához.

   ![Erőforrás-szolgáltatók listája](./media/error-register-resource-provider/list-resource-providers.png)
