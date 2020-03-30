---
title: 'Egyéni szerepkörök: Online SQL Server az SQL felügyelt példányáttelepítések'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az SQL Server és az Azure SQL Database felügyelt példányok online áttelepítéséhez való egyéni szerepkörök használatát.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254942"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Egyéni szerepkörök az SQL Server és az SQL Database felügyelt példányonline áttelepítéséhez

Az Azure Database Migration Service egy APP-azonosítót használ az Azure Services használatával. Az APP-azonosító hoz vagy a közreműködői szerepkör az előfizetés szintjén (amely et sok vállalati biztonsági részlegek nem engedélyeznek), vagy egyéni szerepkörök létrehozása, amelyek az Azure database Migrations Service által igényelt engedélyeket adják. Mivel az Azure Active Directoryban legfeljebb 2000 egyéni szerepkör lehet, érdemes lehet az app-azonosító által kifejezetten szükséges összes engedélyt egy vagy két egyéni szerepkörbe egyesíteni, majd az APP-azonosítónak egyéni szerepkört adni adott objektumokon vagy erőforráscsoportokon (szemben a előfizetési szint). Ha az egyéni szerepkörök száma nem jelent problémát, feloszthatja az egyéni szerepköröket erőforrástípus szerint, így összesen három egyéni szerepkört hozhat létre az alábbiakszerint.

A szerepkör-definíciójjának jsonkarakterláncának AssignableScopes szakasza lehetővé teszi annak szabályozását, hogy az engedélyek hol jelenjenek meg a **szerepkör-hozzárendelés felhasználói** felületén a portálon. Valószínűleg meg szeretné határozni a szerepkört az erőforráscsoportban vagy akár az erőforrás szintjén, hogy elkerülje a felhasználói felület további szerepkörökkel való zsúfoltságát. Ne feledje, hogy ez nem hajtja végre a tényleges szerepkör-hozzárendelést.

## <a name="minimum-number-of-roles"></a>A szerepkörök minimális száma

Jelenleg azt javasoljuk, hogy hozzon létre legalább két egyéni szerepkört az APP-azonosítóhoz, az egyiket az erőforrás szintjén, a másikat pedig az előfizetés szintjén.

> [!NOTE]
> Az utolsó egyéni szerepkör követelmény eközben el kell távolítani, mivel az új SQL Database felügyelt példánykód telepítve van az Azure-ban.

**Az APP-azonosító egyéni szerepköre**. Ez a szerepkör szükséges az Azure Database Migration Service áttelepítéséhez *az erőforrás-* vagy *erőforráscsoport* szintjén (az APP-azonosítóval kapcsolatos további információkért lásd a [Portál használata azure AD-alkalmazás és egyszerű szolgáltatás létrehozásához, amely képes hozzáférni az erőforrásokhoz).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Egyéni szerepkör az APP-azonosító - előfizetéshez.** Ez a szerepkör az Azure Database Migration Service *előfizetési* szintű áttelepítéséhez szükséges.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

A fenti json három szöveges fájlban kell tárolni, és használhatja az AzureRM, az AZ PowerShell-parancsmagokat vagy az Azure CLI-t a szerepkörök létrehozásához **a New-AzureRmRoleDefinition (AzureRM)** vagy **a New-AzRoleDefinition (AZ)** használatával.

További információt az Egyéni szerepkörök az [Azure-erőforrásokhoz](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)című cikkben talál.

Miután létrehozta ezeket az egyéni szerepköröket, szerepkör-hozzárendeléseket kell hozzáadnia a felhasználókhoz és az APP-azonosítókhoz a megfelelő erőforrásokhoz vagy erőforráscsoportokhoz:

* A "DMS-szerepkör – alkalmazásazonosító" szerepkört meg kell adni az áttelepítésekhez, valamint a storage-fiókhoz, az Azure Database Migration Service-példányhoz és az SQL Database felügyelt példányerőforrás-szintekhez.
* A "DMS-szerepkör – alkalmazásazonosító – sub" szerepkört az app-azonosítónak az előfizetés szintjén kell megadni (az erőforrás vagy erőforráscsoport megadása sikertelen lesz). Ez a követelmény ideiglenes, amíg a kódfrissítés telepítése.

## <a name="expanded-number-of-roles"></a>Kibontott szerepkörök száma

Ha az egyéni szerepkörök száma az Azure Active Directoryban nem jelent problémát, javasoljuk, hogy hozzon létre összesen három szerepkört. Továbbra is szüksége lesz a "DMS-szerepkör – alkalmazásazonosító – sub" szerepkörre, de a fenti "DMS-szerepkör – alkalmazásazonosító" szerepkör erőforrástípus szerint két különböző szerepkörre van felosztva.

**Az SQL Database felügyelt példányának APP-azonosítójának egyéni szerepköre**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Egyéni szerepkör az APP-azonosító tárolóhoz**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Szerepkör-kijelölés

Ha szerepkört szeretne hozzárendelni a felhasználókhoz/APP-azonosítóhoz, nyissa meg az Azure Portalt, hajtsa végre a következő lépéseket:

1. Keresse meg az erőforráscsoportot vagy erőforrást (kivéve az előfizetéshez megadandó szerepkört), nyissa meg a **Hozzáférés-vezérlés**t, majd görgessen az imént létrehozott egyéni szerepkörök megkereséséhez.

2. Válassza ki a megfelelő szerepkört, jelölje ki az APP-azonosítót, majd mentse a módosításokat.

  Az APP-azonosító(k) mostantól megjelennek a **Szerepkör-hozzárendelések** lapon.

## <a name="next-steps"></a>További lépések

* Tekintse át a forgatókönyvhöz vezető áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatóban.](https://datamigration.microsoft.com/)
