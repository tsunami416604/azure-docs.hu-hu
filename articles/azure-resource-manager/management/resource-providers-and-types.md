---
title: Erőforrás-szolgáltatók és-erőforrástípusok
description: A Resource Managert, a sémákat és az elérhető API-verziókat támogató erőforrás-szolgáltatókat, valamint az erőforrásokat tároló régiókat ismerteti.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 581b653c6d4769f7777b0ca56f136d25443c1ae4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500010"
---
# <a name="azure-resource-providers-and-types"></a>Azure-beli erőforrás-szolgáltatók és -típusok

Erőforrások telepítésekor gyakran kell lekérnie az erőforrás-szolgáltatókkal és-típusokkal kapcsolatos információkat. Ha például kulcsokat és titkos kulcsokat szeretne tárolni, a Microsoft.KeyVault erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy vaults nevű erőforrástípust biztosít a kulcstároló létrehozásához.

Az erőforrástípus nevének formátuma: **{erőforrás-szolgáltató}/{erőforrástípus}**. A kulcstartó erőforrástípus a Microsoft. Key Vault **/Vaults**.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Az összes erőforrás-szolgáltató megtekintése az Azure-ban
* Erőforrás-szolgáltató regisztrációs állapotának keresése
* Erőforrás-szolgáltató regisztrálása
* Erőforrás-szolgáltatók erőforrás-típusainak megtekintése
* Egy adott erőforrástípus érvényes helyeinek megtekintése
* Egy adott erőforrástípus érvényes API-verzióinak megtekintése

Ezeket a lépéseket a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával hajthatja végre.

Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz leképező listán tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](azure-services-resource-providers.md)ismertető témakört.

## <a name="azure-portal"></a>Azure Portal

Az összes erőforrás-szolgáltató megjelenítéséhez és az előfizetés regisztrációs állapotának megtekintéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

    ![előfizetések kiválasztása](./media/resource-providers-and-types/select-all-services.png)

3. A **minden szolgáltatás** mezőben adja meg az **előfizetés**elemet, majd válassza az **előfizetések**lehetőséget.
4. Válassza ki az előfizetést az előfizetés listából a megtekintéshez.
5. Válassza az **erőforrás-szolgáltatók** lehetőséget, és tekintse meg az elérhető erőforrás-szolgáltatók listáját.

    ![erőforrás-szolgáltatók megjelenítése](./media/resource-providers-and-types/show-resource-providers.png)

6. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint számos erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie a művelet végrehajtásához `/register/action` az erőforrás-szolgáltatón. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Erőforrás-szolgáltató regisztrálásához válassza a **regisztráció**lehetőséget. Az előző képernyőképen a **regisztráció** hivatkozás ki van emelve a **Microsoft. Blueprint**számára.

    Nem törölheti az erőforrás-szolgáltató regisztrációját, ha továbbra is az adott erőforrás-szolgáltatótól származó erőforrástípusok vannak az előfizetésben.

Egy adott erőforrás-szolgáltató információinak megtekintéséhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.
3. A **minden szolgáltatás** mezőben adja meg az **erőforrás-kezelőt**, majd válassza a **erőforrás-kezelő**lehetőséget.

    ![Minden szolgáltatás kiválasztása](./media/resource-providers-and-types/select-resource-explorer.png)

4. Bontsa ki a **szolgáltatók** elemet a jobbra mutató nyíl kiválasztásával.

    ![Szolgáltatók kiválasztása](./media/resource-providers-and-types/select-providers.png)

5. Bontsa ki a megtekinteni kívánt erőforrás-szolgáltatót és erőforrás-típust.

    ![Erőforrás típusának kiválasztása](./media/resource-providers-and-types/select-resource-type.png)

6. A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetése korlátozásokkal rendelkezik, amely megakadályozza, hogy az erőforrást támogató régiókat használja. Az erőforrás-kezelő az erőforrástípus érvényes helyét jeleníti meg.

    ![Helyszínek megjelenítése](./media/resource-providers-and-types/show-locations.png)

7. Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki. Az erőforrás-kezelő az erőforrástípus érvényes API-verzióit jeleníti meg.

    ![API-verziók megjelenítése](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőt:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint számos erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie a művelet végrehajtásához `/register/action` az erőforrás-szolgáltatón. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Nem törölheti az erőforrás-szolgáltató regisztrációját, ha továbbra is az adott erőforrás-szolgáltatótól származó erőforrástípusok vannak az előfizetésben.

Egy adott erőforrás-szolgáltató információinak megtekintéséhez használja az alábbiakat:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Az erőforrás-szolgáltató erőforrásainak megtekintéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Amely a következőket adja vissza:

```output
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki.

Az erőforrástípus elérhető API-verzióinak beszerzéséhez használja a következőt:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Amely a következőket adja vissza:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetése korlátozásokkal rendelkezik, amely megakadályozza, hogy az erőforrást támogató régiókat használja.

Az erőforrástípus támogatott helyeinek beszerzéséhez használja a következőt:.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Amely a következőket adja vissza:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőt:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint számos erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Az erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie a művelet végrehajtásához `/register/action` az erőforrás-szolgáltatón. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

```azurecli
az provider register --namespace Microsoft.Batch
```

Ekkor egy üzenetet ad vissza, amely szerint a regisztráció folyamatban van.

Nem törölheti az erőforrás-szolgáltató regisztrációját, ha továbbra is az adott erőforrás-szolgáltatótól származó erőforrástípusok vannak az előfizetésben.

Egy adott erőforrás-szolgáltató információinak megtekintéséhez használja az alábbiakat:

```azurecli
az provider show --namespace Microsoft.Batch
```

Amely a következőhöz hasonló eredményeket ad vissza:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Az erőforrás-szolgáltató erőforrásainak megtekintéséhez használja a következőt:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió az erőforrás-szolgáltató által kiadott REST API műveletek egyik verziójára vonatkozik. Mivel az erőforrás-szolgáltató új funkciókat tesz lehetővé, a REST API új verzióját bocsátja ki.

Az erőforrástípus elérhető API-verzióinak beszerzéséhez használja a következőt:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

A Resource Manager minden régióban támogatott, de előfordulhat, hogy az Ön által telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetése korlátozásokkal rendelkezik, amely megakadályozza, hogy az erőforrást támogató régiókat használja.

Az erőforrástípus támogatott helyeinek beszerzéséhez használja a következőt:.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Amely a következőket adja vissza:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>További lépések

* A Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok](../templates/template-syntax.md)készítése. 
* Az erőforrás-szolgáltatói sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).
* Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz leképező listán tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](azure-services-resource-providers.md)ismertető témakört.
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez lásd: [Azure REST API](/rest/api/).
