---
title: Az Azure resource provider regisztrációs hibák |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure-erőforrás-szolgáltató regisztrációs hibákat oldja meg.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: 2f3db5e6260b065c83f0e337306d38dca6e5ff51
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341402"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Javítsa ki a hibákat az erőforrás-szolgáltatói regisztrációt

Ez a cikk ismerteti a hibák jelentkezhetnek, ha korábban még nem használta az előfizetés erőforrás-szolgáltató használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Jelenség

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

Vagy, ha letiltja a virtuális gépek automatikus leállítása, előfordulhat, hogy kap egy ehhez hasonló hibaüzenetet:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Ok

Ezeket a hibákat az alábbi okok egyike jelenhet meg:

* A szükséges erőforrás-szolgáltató nem lett regisztrálva az előfizetéshez
* Az erőforrástípus esetében nem támogatott API-verzió
* Az erőforrástípus esetében nem támogatott helye
* A virtuális gépek automatikus leállítása a Microsoft.DevTestLab erőforrás-szolgáltató regisztrálva kell lennie.

## <a name="solution-1---powershell"></a>Megoldás 1 – PowerShell

A PowerShell esetében használja **Get-AzResourceProvider** a regisztrációs állapot megtekintéséhez.

```powershell
Get-AzResourceProvider -ListAvailable
```

Regisztrálja a szolgáltatót, használja a **Register-AzResourceProvider** , és adja meg a regisztrálni kívánt erőforrás-szolgáltató nevét.

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

Tekintse meg, hogy a szolgáltató regisztrálva van-e, használja a `az provider list` parancsot.

```azurecli-interactive
az provider list
```

Erőforrás-szolgáltató regisztrálásához használja a `az provider register` parancsot, és adja meg a *névtér* regisztrálásához.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

A támogatott helyek és a egy erőforrástípus API-verzióit használja:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>3 – Azure portal megoldás

Tekintse meg a regisztrációs állapotát, és regisztrálja a portálon keresztül egy erőforrás-szolgáltató névtere.

1. Válassza ki a portálról, **minden szolgáltatás**.

   ![Válassza ki az összes szolgáltatás](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Válassza az **Előfizetések** lehetőséget.

   ![Keresés az előfizetések között](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Előfizetések listájából válassza ki a kívánt előfizetést, az erőforrás-szolgáltató regisztrálásához.

   ![Válassza ki az előfizetést az erőforrás-szolgáltató regisztrálása](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Válassza ki az előfizetéshez tartozó **erőforrás-szolgáltatók**.

   ![Válassza ki az erőforrás-szolgáltatók](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Tekintse meg az erőforrás-szolgáltatók listáját, és ha szükséges, válassza ki a **regisztrálása** hivatkozásra a telepíteni kívánt típusú erőforrás-szolgáltató regisztrálásához.

   ![Erőforrás-szolgáltatók listája](./media/resource-manager-register-provider-errors/list-resource-providers.png)
