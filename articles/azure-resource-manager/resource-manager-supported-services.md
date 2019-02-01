---
title: Az Azure erőforrás-szolgáltatók és erőforrástípusok |} A Microsoft Docs
description: Az erőforrás-szolgáltatók, amelyek támogatják az erőforrás-kezelő, sémáikat és elérhető API-verzió és a régiók, amelyek az erőforrásokat is ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 109c740ee92e82b6d18879da6839ce6341353cba
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495513"
---
# <a name="resource-providers-and-types"></a>Erőforrás-szolgáltatók és típusaik

Erőforrások üzembe helyezésekor, gyakran kell az erőforrás-szolgáltatókat és típusaikat vonatkozó információk lekéréséhez. Ebből a cikkből megtudhatja, hogy:

* Az összes erőforrás-szolgáltatók megtekintése az Azure-ban
* Erőforrás-szolgáltató regisztrációs állapotának ellenőrzése
* Egy erőforrás-szolgáltató regisztrálása
* Erőforrás-szolgáltató erőforrás nézettípusok
* Az erőforrástípushoz érvényes helyek megtekintése
* Megtekintheti az erőforrástípushoz érvényes API-verziók

Ezen lépéseket a portálon, a PowerShell vagy az Azure CLI használatával végezheti el.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell"></a>PowerShell

Az Azure és a regisztrációs állapot az előfizetéshez tartozó összes erőforrás-szolgáltatót használja:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Amely hasonló eredményeket ad vissza:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az előfizetés használata az erőforrás-szolgáltató erőforrás-szolgáltató regisztrálása konfigurálja. Regisztráció a hatókör, mindig az előfizetést. Sok erőforrás-szolgáltató alapértelmezés szerint automatikusan regisztrálva. Azonban szükség lehet néhány erőforrás-szolgáltatókat manuálisan regisztrálni. Erőforrás-szolgáltató regisztrálásához végrehajtásához engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató számára. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely hasonló eredményeket ad vissza:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Erőforrás-szolgáltató regisztrációja nem törölhető, ha továbbra is rendelkezik az adott erőforrás-szolgáltató erőforrástípusok az előfizetésében.

Egy adott erőforrás-szolgáltató adatait használja:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely hasonló eredményeket ad vissza:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Az erőforrás-szolgáltató erőforrástípusainak megtekintéséhez, használja:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Amely értéket ad vissza:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió megfelel egy REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Erőforrás-szolgáltató új funkciókat tesz elérhetővé, mivel felszabadítja a REST API új verziója.

Az elérhető API-verziók az erőforrástípushoz használja:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Amely értéket ad vissza:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager az összes régióban támogatott, de előfordulhat, hogy az erőforrások telepítése nem támogatott az összes régióban. Emellett előfordulhat olyan korlátozások az előfizetéséhez, amelyek meggátolják, hogy bizonyos régiókban, amely támogatja az erőforrás használatával.

A támogatott helyek, az erőforrástípushoz használja.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Amely értéket ad vissza:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Az Azure és a regisztrációs állapot az előfizetéshez tartozó összes erőforrás-szolgáltatót használja:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Amely hasonló eredményeket ad vissza:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az előfizetés használata az erőforrás-szolgáltató erőforrás-szolgáltató regisztrálása konfigurálja. Regisztráció a hatókör, mindig az előfizetést. Sok erőforrás-szolgáltató alapértelmezés szerint automatikusan regisztrálva. Azonban szükség lehet néhány erőforrás-szolgáltatókat manuálisan regisztrálni. Erőforrás-szolgáltató regisztrálásához végrehajtásához engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató számára. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

```azurecli
az provider register --namespace Microsoft.Batch
```

Amely egy üzenetet ad vissza a regisztrációs folyamatban.

Erőforrás-szolgáltató regisztrációja nem törölhető, ha továbbra is rendelkezik az adott erőforrás-szolgáltató erőforrástípusok az előfizetésében.

Egy adott erőforrás-szolgáltató adatait használja:

```azurecli
az provider show --namespace Microsoft.Batch
```

Amely hasonló eredményeket ad vissza:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Az erőforrás-szolgáltató erőforrástípusainak megtekintéséhez, használja:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Amely értéket ad vissza:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió megfelel egy REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Erőforrás-szolgáltató új funkciókat tesz elérhetővé, mivel felszabadítja a REST API új verziója.

Az elérhető API-verziók az erőforrástípushoz használja:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Amely értéket ad vissza:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager az összes régióban támogatott, de előfordulhat, hogy az erőforrások telepítése nem támogatott az összes régióban. Emellett előfordulhat olyan korlátozások az előfizetéséhez, amelyek meggátolják, hogy bizonyos régiókban, amely támogatja az erőforrás használatával.

A támogatott helyek, az erőforrástípushoz használja.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Amely értéket ad vissza:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portál

Az Azure és a regisztrációs állapot az előfizetéshez tartozó összes erőforrás-szolgáltató megtekintéséhez válasszon **előfizetések**.

![előfizetések kiválasztása](./media/resource-manager-supported-services/select-subscriptions.png)

Válassza ki az előfizetés megtekintéséhez.

![Adja meg az előfizetés](./media/resource-manager-supported-services/subscription.png)

Válassza ki **erőforrás-szolgáltatók** és elérhető erőforrás-szolgáltatók listájának megtekintéséhez.

![Erőforrás-szolgáltatók megjelenítése](./media/resource-manager-supported-services/show-resource-providers.png)

Az előfizetés használata az erőforrás-szolgáltató erőforrás-szolgáltató regisztrálása konfigurálja. Regisztráció a hatókör, mindig az előfizetést. Sok erőforrás-szolgáltató alapértelmezés szerint automatikusan regisztrálva. Azonban szükség lehet néhány erőforrás-szolgáltatókat manuálisan regisztrálni. Erőforrás-szolgáltató regisztrálásához végrehajtásához engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató számára. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Erőforrás-szolgáltató regisztrálásához válassza **regisztrálása**.

![Erőforrás-szolgáltató regisztrálása](./media/resource-manager-supported-services/register-provider.png)

Erőforrás-szolgáltató regisztrációja nem törölhető, ha továbbra is rendelkezik az adott erőforrás-szolgáltató erőforrástípusok az előfizetésében.

Egy adott erőforrás-szolgáltató adatainak megtekintéséhez válasszon **minden szolgáltatás**.

![Válassza ki az összes szolgáltatás](./media/resource-manager-supported-services/more-services.png)

Keresse meg **erőforrás-kezelő** , és jelölje ki az elérhető lehetőségek közül.

![Válassza ki az erőforrás-kezelő](./media/resource-manager-supported-services/select-resource-explorer.png)

Válassza a **Szolgáltatók** elemet.

![Szolgáltatók kiválasztása](./media/resource-manager-supported-services/select-providers.png)

Válassza ki az erőforrás-szolgáltató és az erőforrás típusa, amely meg szeretné jeleníteni.

![Erőforrás típusának kiválasztása](./media/resource-manager-supported-services/select-resource-type.png)

Resource Manager az összes régióban támogatott, de előfordulhat, hogy az erőforrások telepítése nem támogatott az összes régióban. Emellett előfordulhat olyan korlátozások az előfizetéséhez, amelyek meggátolják, hogy bizonyos régiókban, amely támogatja az erőforrás használatával. Az erőforrás-kezelő az erőforrástípushoz érvényes helyeit jeleníti meg.

![Hely megjelenítése](./media/resource-manager-supported-services/show-locations.png)

Az API-verzió megfelel egy REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Erőforrás-szolgáltató új funkciókat tesz elérhetővé, mivel felszabadítja a REST API új verziója. Az erőforrás-kezelő érvényes API-verziók az erőforrástípushoz jeleníti meg.

![API-verzió megjelenítése](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>További lépések

* Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Erőforrások üzembe helyezése kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).
* A műveletek esetében egy erőforrás-szolgáltató megtekintése: [Azure REST API](/rest/api/).
