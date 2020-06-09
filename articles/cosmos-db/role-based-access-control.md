---
title: Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-ben
description: Ismerje meg, hogyan biztosítja az Azure Cosmos DB az adatbázis-védelmet az Active Directory-integrációval (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 0782d5f091671a235df1ab85a8b9706c7efe9170
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509032"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-ben

A Azure Cosmos DB beépített szerepköralapú hozzáférés-vezérlést (RBAC) biztosít a Azure Cosmos DB gyakori felügyeleti eseteihez. A Azure Active Directory profillal rendelkező személy a RBAC-szerepköröket hozzárendelheti a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz, így biztosíthatja vagy megtagadhatja a hozzáférést az erőforrásokhoz és műveletekhez Azure Cosmos DB erőforrásokon. A szerepkör-hozzárendelések hatóköre csak a csak vezérlőre vonatkozik, amely hozzáférést biztosít az Azure Cosmos-fiókok,-adatbázisok,-tárolók és-ajánlatok (átviteli sebesség) számára.

## <a name="built-in-roles"></a>Beépített szerepkörök

A Azure Cosmos DB által támogatott beépített szerepkörök a következők:

|**Beépített szerepkör**  |**Leírás**  |
|---------|---------|
|[DocumentDB-fiók közreműködői](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Felügyelheti Azure Cosmos DB fiókokat.|
|[Cosmos DB-fiók olvasója](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB fiókadatok olvasása.|
|[Cosmos Backup operátor](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Visszaállíthatja az Azure Cosmos-adatbázis vagy-tároló visszaállítási kérelmét.|
|[Cosmos DB operátor](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kiépítheti az Azure Cosmos-fiókokat,-adatbázisokat és-tárolókat, de nem férhet hozzá az adathozzáféréshez szükséges kulcsokhoz.|

> [!IMPORTANT]
> A Azure Cosmos DB RBAC-támogatása csak a vezérlési sík műveleteire vonatkozik. Az adatsíkok műveletei a főkulcsok vagy az erőforrás-tokenek használatával biztonságosak. További információ: az [adathozzáférés biztonságossá tétele Azure Cosmos db](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitás-és hozzáférés-kezelés (IAM)

A Azure Portal hozzáférés-vezérlés **(iam)** ablaktáblája az Azure Cosmos-erőforrások szerepköralapú hozzáférés-vezérlésének konfigurálására szolgál. A szerepköröket a rendszer a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra alkalmazza Active Directoryban. A felhasználók és csoportok számára beépített szerepköröket vagy egyéni szerepköröket is használhat. Az alábbi képernyőfelvételen a Azure Portal hozzáférés-vezérlés (IAM) használatával Active Directory integráció (RBAC) látható:

![Hozzáférés-vezérlés (IAM) a Azure Portal – az adatbázis biztonságának bemutatása](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Egyéni szerepkörök

A beépített szerepkörökön kívül a felhasználók [Egyéni szerepköröket](../role-based-access-control/custom-roles.md) is létrehozhatnak az Azure-ban, és ezeket a szerepköröket a Active Directory bérlőn belüli összes előfizetéshez alkalmazhatják az egyes szolgáltatásokra. Az egyéni szerepkörök lehetővé teszik a felhasználók számára, hogy RBAC-szerepkör-definíciókat hozzanak létre az erőforrás-szolgáltatói műveletek egyéni készletével. Annak megismeréséhez, hogy mely műveletek érhetők el a Azure Cosmos DB egyéni szerepköreinek létrehozásához: [Azure Cosmos db erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>A Cosmos SDK változásainak megakadályozása

> [!WARNING]
> A funkció engedélyezése veszélyes hatással lehet az alkalmazására. A funkció engedélyezése előtt alaposan olvassa el a következőt:.

A Azure Cosmos DB erőforrás-szolgáltató zárolható úgy, hogy megakadályozza a fiók kulcsainak (például a Cosmos SDK-n keresztül csatlakozó alkalmazások) használatával létrehozott összes ügyféltől érkező erőforrások változását. Ez magában foglalja a Azure Portal által végzett módosítást is. Ez olyan felhasználók számára lehet hasznos, akik magasabb fokú irányítást és irányítást kívánnak az éles környezetekben, és lehetővé teszik olyan funkciók használatát, mint például az erőforrás-zárolások, valamint a vezérlési sík műveleteinek diagnosztikai naplóinak engedélyezése is. Az Cosmos DB SDK-val csatlakozó ügyfeleket a Cosmos-fiókok,-adatbázisok,-tárolók és az átviteli sebességek bármely tulajdonságának módosításával megakadályozhatja. Az adatok Cosmos-tárolóba való olvasását és írását magában foglaló műveletek nincsenek hatással a rendszerre.

Ha be van állítva, bármely erőforrás módosítása csak a megfelelő RBAC szerepkörrel rendelkező felhasználótól és Azure Active Directory hitelesítő adatokkal, beleértve a felügyelt szolgáltatás identitásait is.

### <a name="check-list-before-enabling"></a>Az engedélyezés előtt keresse meg a listát

Ezzel a beállítással megakadályozható, hogy bármely Cosmos-erőforrás bármely, a fiók kulcsait, például a Cosmos DB SDK-t, a fiók kulcsain keresztül csatlakozó eszközöket, illetve a Azure Portal. A szolgáltatás engedélyezése után az alkalmazásokkal kapcsolatos problémák és hibák megelőzése érdekében ellenőrizze, hogy az alkalmazások vagy Azure Portal-felhasználók a következő műveletek bármelyikét végrehajtják-e a funkció engedélyezése előtt, beleértve a következőket:

- A Cosmos-fiók bármilyen módosítása, beleértve a tulajdonságokat, illetve a régiók hozzáadását vagy eltávolítását.

- Alárendelt erőforrások, például adatbázisok és tárolók létrehozása, törlése. Ide tartoznak a más API-k, például a Cassandra, a MongoDB, a Gremlin és a Table-erőforrások erőforrásai.

- Adatbázis vagy tároló szintű erőforrások átviteli sebességének frissítése.

- A tároló tulajdonságainak módosítása, beleértve az index szabályzatot, az ÉLETTARTAMot és az egyedi kulcsokat.

- Tárolt eljárások, eseményindítók vagy felhasználó által definiált függvények módosítása.

Ha az alkalmazások (vagy Azure Portalon keresztül a felhasználók) végrehajtják ezeket a műveleteket, azokat át kell telepíteni az ARM- [sablonok](manage-sql-with-resource-manager.md), a [PowerShell](manage-with-powershell.md), [Az Azure CLI](manage-with-cli.md), a [Rest](/rest/api/cosmos-db-resource-provider/) vagy az [Azure felügyeleti könyvtár](https://github.com/Azure-Samples/cosmos-management-net)használatával. Vegye figyelembe, hogy az Azure-felügyelet [több nyelven](https://docs.microsoft.com/azure/?product=featured#languages-and-tools)is elérhető.

### <a name="set-via-arm-template"></a>Beállítás ARM-sablonnal

Ha ezt a tulajdonságot ARM-sablonnal szeretné beállítani, frissítse meglévő sablonját, vagy exportáljon egy új sablont a jelenlegi központi telepítéshez, majd adja meg a `"disableKeyBasedMetadataWriteAccess": true` databaseAccounts erőforrásainak tulajdonságait. Az alábbi példa egy Azure Resource Manager sablon alapszintű példája ennek a tulajdonságnak a beállításával.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Ügyeljen rá, hogy a fiókja és a gyermek erőforrásainak egyéb tulajdonságait is tartalmazza, ha redploying ezzel a tulajdonsággal. Ne telepítse a sablont úgy, hogy az az legyen, vagy alaphelyzetbe állítja az összes fiók tulajdonságait.

### <a name="set-via-azure-cli"></a>Beállítás az Azure CLI-n keresztül

Az Azure CLI használatának engedélyezéséhez használja az alábbi parancsot:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Beállítás a PowerShell használatával

A Azure PowerShell használatának engedélyezéséhez használja az alábbi parancsot:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Következő lépések

- [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz](../role-based-access-control/overview.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
- [Erőforrás-szolgáltatói műveletek Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
