---
title: Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-ben
description: Ismerje meg, hogy az Azure Cosmos DB hogyan nyújt adatbázis-védelmet az Active Directory-integráció (RBAC) segítségével.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445097"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Szerepköralapú hozzáférés-vezérlés az Azure Cosmos DB-ben

Az Azure Cosmos DB beépített szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az Azure Cosmos DB gyakori felügyeleti forgatókönyveihez. Az Azure Active Directoryban profillal rendelkező egyén hozzárendelheti ezeket az RBAC-szerepköröket a felhasználókhoz, csoportokhoz, szolgáltatástagokhoz vagy felügyelt identitásokhoz az Azure Cosmos DB-erőforrások erőforrásaihoz és műveleteihez való hozzáférés biztosításához vagy megtagadásához. Szerepkör-hozzárendelések hatóköre csak a vezérlősík-hozzáférés, amely magában foglalja az Azure Cosmos-fiókok, adatbázisok, tárolók és ajánlatok (átviteli érték) eléréséhez való hozzáférést.

## <a name="built-in-roles"></a>Beépített szerepkörök

Az Azure Cosmos DB által támogatott beépített szerepkörök a következők:

|**Beépített szerepkör**  |**Leírás**  |
|---------|---------|
|[DocumentDB-fiók közreműködője](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Az Azure Cosmos DB-fiókok kezelése.|
|[Cosmos DB-fiókolvasó](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Az Azure Cosmos DB-fiók adatai olvasása.|
|[Cosmos biztonsági mentési operátor](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Az Azure Cosmos-adatbázis vagy egy tároló visszaállítási kérelmet küldhet.|
|[Cosmos DB operátor](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos-fiókok, adatbázisok és tárolók kiépítése, de nem férhet hozzá az adatok eléréséhez szükséges kulcsokat.|

> [!IMPORTANT]
> Az RBAC-támogatás az Azure Cosmos DB-ben csak a vezérlősík-műveletekre vonatkozik. Az adatsík-műveletek főkulcsokkal vagy erőforrás-jogkivonatokkal vannak biztosítva. További információ: [Biztonságos hozzáférés az adatokhoz az Azure Cosmos DB-ben](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitás- és hozzáférés-kezelés (IAM)

A **hozzáférés-vezérlési (IAM)** ablaktábla az Azure Portalon az Azure Cosmos-erőforrások szerepköralapú hozzáférés-vezérlés konfigurálására szolgál. A szerepkörök az Active Directoryban lévő felhasználókra, csoportokra, egyszerű szolgáltatástagokra és felügyelt identitásokra vonatkoznak. A beépített szerepköröket vagy egyéni szerepköröket egyénekhez és csoportokhoz használhatja. Az alábbi képernyőképen az Active Directory integráció (RBAC) hozzáférés-vezérléssel (IAM) látható az Azure Portalon:

![Hozzáférés-vezérlés (IAM) az Azure Portalon – az adatbázis biztonságának bemutatása](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Egyéni szerepkörök

A beépített szerepkörökön kívül a felhasználók [egyéni szerepköröket](../role-based-access-control/custom-roles.md) is létrehozhatnak az Azure-ban, és ezeket a szerepköröket az Active Directory-bérlőn belüli összes előfizetésben alkalmazhatják. Az egyéni szerepkörök lehetővé teszik a felhasználók számára, hogy RBAC szerepkör-definíciókat hozzanak létre az erőforrás-szolgáltatói műveletek egyéni készletével. Ha meg szeretné tudni, hogy mely műveletek érhetők el az Azure Cosmos DB egyéni szerepkörök létrehozásához, lásd: [Azure Cosmos DB erőforrás-szolgáltató i.](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>A Cosmos SDK változásainak megakadályozása

A Cosmos erőforrás-szolgáltató zárolható, hogy megakadályozzák az erőforrások módosításait, beleértve a Cosmos-fiók, adatbázisok, tárolók és átviteli bármely ügyfél fiókkulcsokon keresztül csatlakozik (azaz a Cosmos SDK-n keresztül csatlakozó alkalmazások). Ha be van állítva, a módosításokat bármely erőforrás kell a megfelelő RBAC szerepkörrel és hitelesítő adatokkal rendelkező felhasználó. Ez a képesség `disableKeyBasedMetadataWriteAccess` a Cosmos erőforrás-szolgáltató tulajdonságértékével van beállítva. Egy példa egy Azure Resource Manager sablon ezzel a tulajdonság beállítással az alábbiakban.

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

## <a name="next-steps"></a>További lépések

- [Mi a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz?](../role-based-access-control/overview.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](../role-based-access-control/custom-roles.md)
- [Az Azure Cosmos DB erőforrás-szolgáltatói műveletei](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
