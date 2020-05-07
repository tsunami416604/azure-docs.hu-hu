---
title: Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-ben
description: Ismerje meg, hogyan biztosítja az Azure Cosmos DB az adatbázis-védelmet az Active Directory-integrációval (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4e028e7a5e7e7b8f747d7a1cfb36c553a8113544
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583735"
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

A Cosmos erőforrás-szolgáltató zárolható úgy, hogy megakadályozza az erőforrások, például a Cosmos-fiók, az adatbázisok, a tárolók és az átviteli sebességek változását a fiók kulcsainak (például a Cosmos SDK-n keresztül csatlakozó alkalmazások) használatával csatlakozó ügyfelektől. Ha be van állítva, minden erőforrás módosítása csak a megfelelő RBAC szerepkörrel és hitelesítő adatokkal rendelkező felhasználótól lehet. Ez a képesség a Cosmos `disableKeyBasedMetadataWriteAccess` erőforrás-szolgáltató tulajdonság értékével van beállítva. Alább látható egy példa erre a tulajdonság-beállításra Azure Resource Manager sablonra.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
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
Ha egy meglévő Resource Manager-sablont exportál, és ezt a tulajdonságot frissíti, akkor teljes mértékben lecserélheti a sablon funkcióit. Ha tehát az összes érték nem szerepel, a rendszer alaphelyzetbe állítja az alapértelmezett értéket. A kulcs alapú metaadatok írási hozzáférésének letiltásának másik módja az Azure CLI használata az alábbi parancsban látható módon:

```cli
az cosmosdb update  --name CosmosDBAccountName --resource-group ResourceGroupName  --disable-key-based-metadata-write-access true

```

## <a name="next-steps"></a>További lépések

- [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz](../role-based-access-control/overview.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
- [Erőforrás-szolgáltatói műveletek Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
