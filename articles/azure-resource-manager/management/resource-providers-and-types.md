---
title: Erőforrás-szolgáltatók és erőforrástípusok
description: Ez a témakör az erőforrás-kezelőt, a sémákat és a rendelkezésre álló API-verziókat, valamint az erőforrásokat üzemeltető régiókat ismerteti.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273981"
---
# <a name="azure-resource-providers-and-types"></a>Azure-beli erőforrás-szolgáltatók és -típusok

Erőforrások üzembe helyezésekor gyakran kell letölteni az erőforrás-szolgáltatók és típusok adatait. Ha például kulcsokat és titkos kulcsokat szeretne tárolni, a Microsoft.KeyVault erőforrás-szolgáltatót fogja használni. Ez az erőforrás-szolgáltató egy vaults nevű erőforrástípust biztosít a kulcstároló létrehozásához.

Az erőforrástípus nevének formátuma: **{erőforrás-szolgáltató}/{erőforrástípus}**. A kulcstartó konstitusa a **Microsoft.KeyVault/vaults.**

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Az Azure összes erőforrás-szolgáltatójának megtekintése
* Erőforrás-szolgáltató regisztrációs állapotának ellenőrzése
* Erőforrás-szolgáltató regisztrálása
* Erőforrás-szolgáltató erőforrástípusainak megtekintése
* Erőforrástípus érvényes helyeinek megtekintése
* Erőforrástípus érvényes API-verzióinak megtekintése

Ezeket a lépéseket az Azure Portalon, az Azure PowerShellen vagy az Azure CLI-n keresztül hajthatja végre.

Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz rendelő lista az [Azure-szolgáltatások erőforrás-szolgáltatói című](azure-services-resource-providers.md)témakörben található.

## <a name="azure-portal"></a>Azure portál

Az összes erőforrás-szolgáltató és az előfizetés regisztrációs állapotának megtekintése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

    ![előfizetések kiválasztása](./media/resource-providers-and-types/select-all-services.png)

3. A **Minden szolgáltatás** mezőbe írja be az **előfizetést,** és válassza **az Előfizetések**lehetőséget.
4. Válassza ki a megtekinteni kívánt előfizetési listából az előfizetést.
5. Válassza az **Erőforrás-szolgáltatók lehetőséget,** és tekintse meg az elérhető erőforrás-szolgáltatók listáját.

    ![erőforrás-szolgáltatók megjelenítése](./media/resource-providers-and-types/show-resource-providers.png)

6. Az erőforrás-szolgáltató regisztrálása úgy konfigurálja az előfizetést, hogy működjön együtt az erőforrás-szolgáltatóval. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az `/register/action` erőforrás-szolgáltató műveletéhez. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Erőforrás-szolgáltató regisztrálásához válassza a **Regisztráció**lehetőséget. Az előző képernyőképen a **Regisztráció** hivatkozás ki van emelve a **Microsoft.Blueprint**programhoz.

    Nem távolodhat le az erőforrás-szolgáltató regisztrációjáról, ha az előfizetésben továbbra is vannak az adott erőforrás-szolgáltatóerőforrás-típusok.

Egy adott erőforrás-szolgáltató adatainak megtekintése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.
3. A **Minden szolgáltatás** mezőbe írja be az **erőforrás-kezelőt**, majd válassza **az Erőforrás-kezelő**lehetőséget.

    ![válassza az Összes szolgáltatás lehetőséget](./media/resource-providers-and-types/select-resource-explorer.png)

4. Bontsa ki **a Szolgáltatók** csomópontot a megfelelő nyíl kiválasztásával.

    ![Szolgáltatók kiválasztása](./media/resource-providers-and-types/select-providers.png)

5. Bontsa ki a megtekinteni kívánt erőforrás-szolgáltatót és erőforrástípust.

    ![Erőforrástípus kiválasztása](./media/resource-providers-and-types/select-resource-type.png)

6. Az Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy a telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetés, amely megakadályozza, hogy egyes régiók, amelyek támogatják az erőforrást. Az erőforrás-kezelő érvényes helyeket jelenít meg az erőforrástípushoz.

    ![Helyek megjelenítése](./media/resource-providers-and-types/show-locations.png)

7. Az API-verzió a REST API-műveletek azon verziójának felel meg, amelyet az erőforrás-szolgáltató szabadít fel. Mivel az erőforrás-szolgáltató engedélyezi az új funkciókat, kiadja a REST API új verzióját. Az erőforrás-kezelő érvényes API-verziókat jelenít meg az erőforrástípushoz.

    ![API-verziók megjelenítése](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőket:

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

Az erőforrás-szolgáltató regisztrálása úgy konfigurálja az előfizetést, hogy működjön együtt az erőforrás-szolgáltatóval. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az `/register/action` erőforrás-szolgáltató műveletéhez. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

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

Nem távolodhat le az erőforrás-szolgáltató regisztrációjáról, ha az előfizetésben továbbra is vannak az adott erőforrás-szolgáltatóerőforrás-típusok.

Egy adott erőforrás-szolgáltató adatainak megtekintéséhez használja a következőket:

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

Az erőforrás-szolgáltató erőforrástípusainak megtekintéséhez használja a következőket:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Melyik adja vissza:

```output
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió a REST API-műveletek azon verziójának felel meg, amelyet az erőforrás-szolgáltató szabadít fel. Mivel az erőforrás-szolgáltató engedélyezi az új funkciókat, kiadja a REST API új verzióját.

Az erőforrástípushoz elérhető API-verziók beszerzéséhez használja a következőket:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Melyik adja vissza:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Az Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy a telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetés, amely megakadályozza, hogy egyes régiók, amelyek támogatják az erőforrást.

Az erőforrástípus támogatott helyeinek lekérnie használja.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Melyik adja vissza:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Az Azure összes erőforrás-szolgáltatójának és az előfizetés regisztrációs állapotának megtekintéséhez használja a következőket:

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

Az erőforrás-szolgáltató regisztrálása úgy konfigurálja az előfizetést, hogy működjön együtt az erőforrás-szolgáltatóval. A regisztráció hatóköre mindig az előfizetés. Alapértelmezés szerint sok erőforrás-szolgáltató automatikusan regisztrálva van. Előfordulhat azonban, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az `/register/action` erőforrás-szolgáltató műveletéhez. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

```azurecli
az provider register --namespace Microsoft.Batch
```

Amely azt az üzenetet adja vissza, hogy a regisztráció folyamatos.

Nem távolodhat le az erőforrás-szolgáltató regisztrációjáról, ha az előfizetésben továbbra is vannak az adott erőforrás-szolgáltatóerőforrás-típusok.

Egy adott erőforrás-szolgáltató adatainak megtekintéséhez használja a következőket:

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

Az erőforrás-szolgáltató erőforrástípusainak megtekintéséhez használja a következőket:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Melyik adja vissza:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Az API-verzió a REST API-műveletek azon verziójának felel meg, amelyet az erőforrás-szolgáltató szabadít fel. Mivel az erőforrás-szolgáltató engedélyezi az új funkciókat, kiadja a REST API új verzióját.

Az erőforrástípushoz elérhető API-verziók beszerzéséhez használja a következőket:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Melyik adja vissza:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Az Erőforrás-kezelő minden régióban támogatott, de előfordulhat, hogy a telepített erőforrások nem minden régióban támogatottak. Emellett előfordulhat, hogy az előfizetés, amely megakadályozza, hogy egyes régiók, amelyek támogatják az erőforrást.

Az erőforrástípus támogatott helyeinek lekérnie használja.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Melyik adja vissza:

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

* Az Erőforrás-kezelő sablonok létrehozásáról az [Azure Resource Manager-sablonok létrehozása című témakörben olvashat.](../templates/template-syntax.md) 
* Az erőforrás-szolgáltató sablonsémaimegtekintéséhez olvassa el a Sablon hivatkozása című [témakört.](/azure/templates/)
* Az erőforrás-szolgáltatókat az Azure-szolgáltatásokhoz rendelő lista az [Azure-szolgáltatások erőforrás-szolgáltatói című](azure-services-resource-providers.md)témakörben található.
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez tekintse meg az [Azure REST API-t.](/rest/api/)
