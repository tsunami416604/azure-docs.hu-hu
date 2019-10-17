---
title: Az Azure erőforrás-szolgáltató regisztrációs hibái | Microsoft Docs
description: Ismerteti, Hogyan oldhatók fel az Azure erőforrás-szolgáltató regisztrációs hibái az erőforrások Azure Resource Manager használatával történő telepítésekor.
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
ms.openlocfilehash: fcdcfdfe736f29f18ea2dc240a66fd7fa6bc404b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390266"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Az erőforrás-szolgáltató regisztrálásával kapcsolatos hibák elhárítása

Ez a cikk az előfizetésében korábban még nem használt erőforrás-szolgáltató használata során felmerülő hibákat ismerteti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Az erőforrás telepítésekor a következő hibakód és üzenet jelenhet meg:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Vagy a következőhöz hasonló üzenet jelenhet meg:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

A hibaüzenetnek meg kell adnia a támogatott helyszínekre és API-verzióra vonatkozó javaslatokat. A sablon a javasolt értékek egyikére módosítható. A legtöbb szolgáltatót automatikusan regisztrálja a Azure Portal vagy a használt parancssori felület, de nem az összes. Ha korábban még nem használta egy adott erőforrás-szolgáltatót, akkor előfordulhat, hogy regisztrálnia kell a szolgáltatót.

Vagy ha letiltja a virtuális gépek automatikus leállítását, a következőhöz hasonló hibaüzenet jelenhet meg:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Ok

Ezeket a hibákat a következő okok egyike miatt kapja meg:

* A szükséges erőforrás-szolgáltató nincs regisztrálva az előfizetéséhez
* Az erőforrás típusa nem támogatja az API-verziót
* Az erőforrás típusa nem támogatja a helyet
* A virtuális gépek automatikus leállításához regisztrálni kell a Microsoft. segédösszetevője erőforrás-szolgáltatót.

## <a name="solution-1---powershell"></a>1\. megoldás – PowerShell

A PowerShell esetében a **Get-AzResourceProvider** használatával tekintheti meg a regisztrációs állapotát.

```powershell
Get-AzResourceProvider -ListAvailable
```

A szolgáltató regisztrálásához használja a **Register-AzResourceProvider** nevet, és adja meg a regisztrálni kívánt erőforrás-szolgáltató nevét.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Egy adott típusú erőforrás támogatott helyeinek beszerzéséhez használja a következőt:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Egy adott típusú erőforrás támogatott API-verzióinak beszerzéséhez használja a következőt:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>2\. megoldás – Azure CLI

Ha szeretné megtekinteni, hogy a szolgáltató regisztrálva van-e, használja a `az provider list` parancsot.

```azurecli-interactive
az provider list
```

Erőforrás-szolgáltató regisztrálásához használja a `az provider register` parancsot, és határozza meg a regisztrálni kívánt *névteret* .

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Az erőforrástípus támogatott helyeinek és API-verzióinak megtekintéséhez használja a következőt:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>3\. megoldás – Azure Portal

Megtekintheti a regisztrációs állapotot, és regisztrálhatja az erőforrás-szolgáltatói névteret a portálon keresztül.

1. A portálon válassza a **minden szolgáltatás**lehetőséget.

   ![Minden szolgáltatás kiválasztása](./media/resource-manager-register-provider-errors/select-all-services.png)

1. Válassza az **Előfizetések** lehetőséget.

   ![Előfizetések kiválasztása](./media/resource-manager-register-provider-errors/select-subscriptions.png)

1. Az előfizetések listájából válassza ki az erőforrás-szolgáltató regisztrálásához használni kívánt előfizetést.

   ![Előfizetés kiválasztása az erőforrás-szolgáltató regisztrálásához](./media/resource-manager-register-provider-errors/select-subscription-to-register.png)

1. Az előfizetéséhez válassza az **erőforrás-szolgáltatók**lehetőséget.

   ![Erőforrás-szolgáltatók kiválasztása](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Tekintse meg az erőforrás-szolgáltatók listáját, és szükség esetén válassza a **regisztrálás** hivatkozást a telepíteni kívánt típus erőforrás-szolgáltatójának regisztrálásához.

   ![Erőforrás-szolgáltatók listázása](./media/resource-manager-register-provider-errors/list-resource-providers.png)
