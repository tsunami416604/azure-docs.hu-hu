---
title: Erőforrás-szolgáltató regisztrációs hibái
description: Bemutatja, hogyan oldható meg az Azure-erőforrás-szolgáltató regisztrációs hibái az Azure Resource Manager használatával történő üzembe helyezéskor.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.openlocfilehash: a9182be53cc91240a62ab201efc53d674f7cf427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273773"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák elhárítása

Ez a cikk ismerteti azokat a hibákat, amelyek az előfizetésben korábban nem használt erőforrás-szolgáltató használata során találkozhatnak.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Az erőforrás telepítésekor a következő hibaüzenet és üzenet jelenhet meg:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Vagy hasonló üzenetet is kaphat, amely a következőket mondja:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

A hibaüzenet javaslatokat ad a támogatott helyekre és API-verziókra vonatkozóan. A sablont módosíthatja a javasolt értékek egyikére. A legtöbb szolgáltatót automatikusan regisztrálja az Azure Portal vagy az Ön által használt parancssori felület, de nem az összes. Ha még nem használt egy adott erőforrás-szolgáltatóelőtt, előfordulhat, hogy regisztrálnia kell a szolgáltatót.

Vagy a virtuális gépek automatikus letiltásának letiltásakor a következőhöz hasonló hibaüzenet jelenhet meg:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Ok

Az alábbi okok valamelyike miatt kapja meg ezeket a hibákat:

* A szükséges erőforrás-szolgáltató nincs regisztrálva az előfizetéshez
* Az API-verzió nem támogatott az erőforrástípushoz
* Az erőforrástípushoz nem támogatott hely
* A virtuális gépek automatikus leállításához a Microsoft.DevTestLab erőforrás-szolgáltatót regisztrálni kell.

## <a name="solution-1---powershell"></a>1. megoldás – PowerShell

A PowerShell esetében a **Get-AzResourceProvider** használatával tekintse meg a regisztrációs állapotát.

```powershell
Get-AzResourceProvider -ListAvailable
```

Szolgáltató regisztrálásához használja a **Register-AzResourceProvider szolgáltatást,** és adja meg a regisztrálni kívánt erőforrás-szolgáltató nevét.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Egy adott típusú erőforrás támogatott helyeinek lekérnie, használja a következőket:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Egy adott típusú erőforrás támogatott API-verzióinak bekéréséhez használja a következőket:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>2. megoldás - Azure CLI

Ha meg szeretné tudni, hogy `az provider list` a szolgáltató regisztrálva van-e, használja a parancsot.

```azurecli-interactive
az provider list
```

Erőforrás-szolgáltató regisztrálásához használja `az provider register` a parancsot, és adja meg a regisztrálandó *névteret.*

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Az erőforrástípus támogatott helyeinek és API-verzióinak megtekintéséhez használja a következőket:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>3. megoldás – Azure-portál

Megtekintheti a regisztrációs állapotot, és regisztrálhat egy erőforrás-szolgáltató névteret a portálon keresztül.

1. A portálon válassza a **Minden szolgáltatás lehetőséget.**

   ![Az összes szolgáltatás kijelölése](./media/error-register-resource-provider/select-all-services.png)

1. Válassza az **Előfizetések** lehetőséget.

   ![Előfizetések kiválasztása](./media/error-register-resource-provider/select-subscriptions.png)

1. Az előfizetések listájából válassza ki az erőforrás-szolgáltató regisztrálásához használni kívánt előfizetést.

   ![Az erőforrás-szolgáltató regisztrálásához szükséges előfizetés kiválasztása](./media/error-register-resource-provider/select-subscription-to-register.png)

1. Az előfizetéshez válassza az **Erőforrás-szolgáltatók**lehetőséget.

   ![Erőforrás-szolgáltatók kiválasztása](./media/error-register-resource-provider/select-resource-provider.png)

1. Tekintse meg az erőforrás-szolgáltatók listáját, és ha szükséges, válassza a **Regisztráció** hivatkozást az üzembe helyezni kívánt típusú erőforrás-szolgáltató regisztrálásához.

   ![Erőforrás-szolgáltatók listázása](./media/error-register-resource-provider/list-resource-providers.png)
