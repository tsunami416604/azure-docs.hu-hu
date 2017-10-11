---
title: "Az Azure erőforrás-szolgáltatók és erőforrástípusok |} Microsoft Docs"
description: "Az erőforrás-szolgáltató, amely támogatja az erőforrás-kezelő, hogy a sémáikat és elérhető API-verzió és az erőforrásokat is üzemeltető régiók ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6a9128f45d4199404019cee594842d59c7f1aaf3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="resource-providers-and-types"></a>Erőforrás-szolgáltatók és típusát

Erőforrások való telepítésekor, gyakran kell az erőforrás-szolgáltatók és típusok vonatkozó információk lekéréséhez. Ebből a cikkből megismerheti, hogy:

* Megtekintheti az összes erőforrás-szolgáltató az Azure-ban
* Egy erőforrás-szolgáltató regisztrációs állapotának ellenőrzése
* Egy erőforrás-szolgáltató regisztrálása
* Egy erőforrás-szolgáltató típusú erőforrások megtekintése
* Egy erőforrástípus érvényes helyek megtekintése
* Az erőforrástípus érvénytelen API-verziók megtekintése

Ezeket a lépéseket a portálon, PowerShell vagy az Azure parancssori felület használatával végezheti el.

## <a name="powershell"></a>PowerShell

Azure-ban, és a regisztrációs állapotot az előfizetéshez tartozó összes erőforrás-szolgáltató megjelenítéséhez használja:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
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

Az erőforrás-szolgáltató dolgozni az előfizetés egy erőforrás-szolgáltató regisztrálása konfigurálja. A regisztrálási hatóköre mindig az előfizetést. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Szükség lehet, hogy manuálisan kell regisztrálni egy erőforrás-szolgáltató. Egy erőforrás-szolgáltató regisztrálása, engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató. A közreműködői és tulajdonos szerepkör tartalmazza ezt a műveletet.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely hasonló eredményeket ad vissza:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Egy erőforrás-szolgáltató nem törölhető, ha az előfizetés erőforrástípusok adott erőforrás-szolgáltató továbbra is fennáll.

Egy adott erőforrás-szolgáltató adatainak megjelenítéséhez használja:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely hasonló eredményeket ad vissza:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Erőforrás-szolgáltató az erőforrástípusok megjelenítéséhez használja:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Amely adja vissza:

```powershell
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió felel meg a REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Egy erőforrás-szolgáltató lehetővé teszi, hogy az új funkciók, mivel feloldja a REST API egy új verziója. 

Az elérhető API-verzió az erőforrástípus használatához:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Amely adja vissza:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy minden régióban nem támogatott az erőforrások telepítése. Ezenkívül előfordulhat, az előfizetés, amelyek meggátolják, hogy az erőforrás-t támogató egyes régiókban használatával korlátozásait. 

A támogatott helyek az erőforrástípus használatához.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Amely adja vissza:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI
Azure-ban, és a regisztrációs állapotot az előfizetéshez tartozó összes erőforrás-szolgáltató megjelenítéséhez használja:

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

Az erőforrás-szolgáltató dolgozni az előfizetés egy erőforrás-szolgáltató regisztrálása konfigurálja. A regisztrálási hatóköre mindig az előfizetést. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Szükség lehet, hogy manuálisan kell regisztrálni egy erőforrás-szolgáltató. Egy erőforrás-szolgáltató regisztrálása, engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató. A közreműködői és tulajdonos szerepkör tartalmazza ezt a műveletet.

```azurecli
az provider register --namespace Microsoft.Batch
```

Egy üzenet, hogy a regisztrációs visszaadó folyamatban.

Egy erőforrás-szolgáltató nem törölhető, ha az előfizetés erőforrástípusok adott erőforrás-szolgáltató továbbra is fennáll.

Egy adott erőforrás-szolgáltató adatainak megjelenítéséhez használja:

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

Erőforrás-szolgáltató az erőforrástípusok megjelenítéséhez használja:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Amely adja vissza:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió felel meg a REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Egy erőforrás-szolgáltató lehetővé teszi, hogy az új funkciók, mivel feloldja a REST API egy új verziója. 

Az elérhető API-verzió az erőforrástípus használatához:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Amely adja vissza:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy minden régióban nem támogatott az erőforrások telepítése. Ezenkívül előfordulhat, az előfizetés, amelyek meggátolják, hogy az erőforrás-t támogató egyes régiókban használatával korlátozásait. 

A támogatott helyek az erőforrástípus használatához.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Amely adja vissza:

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

Azure-ban, és a regisztrációs állapotot az előfizetéshez tartozó összes erőforrás-szolgáltató megtekintéséhez válasszon **előfizetések**.

![az előfizetések kiválasztása](./media/resource-manager-supported-services/select-subscriptions.png)

Válassza ki az előfizetés megtekintéséhez.

![Adja meg az előfizetést](./media/resource-manager-supported-services/subscription.png)

Válassza ki **erőforrás-szolgáltató** és megtekintheti az elérhető erőforrás-szolgáltatók listáját.

![erőforrás-szolgáltatók megjelenítése](./media/resource-manager-supported-services/show-resource-providers.png)

Az erőforrás-szolgáltató dolgozni az előfizetés egy erőforrás-szolgáltató regisztrálása konfigurálja. A regisztrálási hatóköre mindig az előfizetést. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Szükség lehet, hogy manuálisan kell regisztrálni egy erőforrás-szolgáltató. Egy erőforrás-szolgáltató regisztrálása, engedéllyel kell rendelkeznie a `/register/action` műveletet az erőforrás-szolgáltató. A közreműködői és tulajdonos szerepkör tartalmazza ezt a műveletet. Válasszon egy erőforrás-szolgáltató regisztrálásához **regisztrálása**.

![erőforrás-szolgáltató regisztrálása](./media/resource-manager-supported-services/register-provider.png)

Egy erőforrás-szolgáltató nem törölhető, ha az előfizetés erőforrástípusok adott erőforrás-szolgáltató továbbra is fennáll.

Megtekintéséhez válasszon egy adott erőforrás-szolgáltató adatait **további szolgáltatások**.

![Jelölje ki a további szolgáltatások](./media/resource-manager-supported-services/more-services.png)

Keresse meg **erőforrás-kezelő** , és jelölje ki az elérhető lehetőségek közül.

![Válassza ki az erőforrás-kezelő](./media/resource-manager-supported-services/select-resource-explorer.png)

Válassza ki **szolgáltatók**.

![Szolgáltatók kiválasztása](./media/resource-manager-supported-services/select-providers.png)

Válassza ki az erőforrás-szolgáltató és a megtekinteni kívánt erőforrástípust.

![Válassza ki az erőforrás típusa](./media/resource-manager-supported-services/select-resource-type.png)

Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy minden régióban nem támogatott az erőforrások telepítése. Ezenkívül előfordulhat, az előfizetés, amelyek meggátolják, hogy az erőforrás-t támogató egyes régiókban használatával korlátozásait. Az erőforrás-kezelő az erőforrástípushoz érvényes helyek jeleníti meg.

![Hely megjelenítése](./media/resource-manager-supported-services/show-locations.png)

Az API-verzió felel meg a REST API-műveleteket az erőforrás-szolgáltató által kiadott verzióját. Egy erőforrás-szolgáltató lehetővé teszi, hogy az új funkciók, mivel feloldja a REST API egy új verziója. Az erőforrás-kezelő jeleníti meg az erőforrástípus érvénytelen API-verziók.

![API-verziók megjelenítése](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Következő lépések
* Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Erőforrások telepítésével kapcsolatos további tudnivalókért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).
* Az erőforrás-szolgáltató műveletek megtekintése: [Azure REST API](/rest/api/).

