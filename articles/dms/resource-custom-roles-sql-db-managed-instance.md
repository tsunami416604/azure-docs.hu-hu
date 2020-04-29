---
title: 'Egyéni szerepkörök: online SQL Server az SQL felügyelt példányainak áttelepítéséhez'
titleSuffix: Azure Database Migration Service
description: Megtudhatja, hogyan használhatja a SQL Server egyéni szerepköreit a felügyelt példányok online áttelepítésének Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78254942"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>A felügyelt példányok online áttelepítésének SQL Database SQL Server egyéni szerepkörei

Azure Database Migration Service egy alkalmazás-azonosítót használ az Azure-szolgáltatásokkal való kommunikációhoz. Az alkalmazás-AZONOSÍTÓhoz a közreműködő szerepkörnek kell lennie az előfizetés szintjén (amely sok vállalati biztonsági osztályt nem engedélyez), vagy olyan egyéni szerepköröket hoz létre, amelyek az Azure Database migrations szolgáltatás által igényelt konkrét engedélyeket biztosítanak. Mivel a Azure Active Directoryban az egyéni szerepkörök 2 000 száma korlátozva van, előfordulhat, hogy az alkalmazás-azonosító által kifejezetten egy vagy két egyéni szerepkörhöz szükséges engedélyeket szeretné egyesíteni, majd az alkalmazás AZONOSÍTÓját az adott objektumokon vagy erőforráscsoportok (vagy az előfizetés szintjén) adja meg az egyéni szerepkörnek. Ha az egyéni szerepkörök száma nem aggodalomra ad okot, az egyéni szerepköröket az erőforrástípus alapján is feloszthatja, hogy az alább leírtak szerint három egyéni szerepkört hozzon létre.

A szerepkör-definíció JSON-karakterláncának AssignableScopes szakasza segítségével szabályozhatja, hogy az engedélyek hol jelenjenek meg a portál **szerepkör-hozzárendelés hozzáadása** felületén. Valószínűleg meg szeretné határozni a szerepkört az erőforráscsoport vagy az erőforrás szintjén, hogy elkerülje a felhasználói felület túlzsúfoltságát a további szerepkörökkel. Ne feledje, hogy ez nem hajtja végre a tényleges szerepkör-hozzárendelést.

## <a name="minimum-number-of-roles"></a>Szerepkörök minimális száma

Javasoljuk, hogy legalább két egyéni szerepkört hozzon létre az alkalmazás-AZONOSÍTÓhoz, egyet az erőforrás szintjén és a másikat az előfizetés szintjén.

> [!NOTE]
> Az utolsó egyéni szerepkörre vonatkozó követelményt végül el lehet távolítani, mivel az új SQL Database felügyelt példány kódja üzembe helyezése az Azure-ban történik.

**Egyéni szerepkör az alkalmazás-azonosítóhoz**. Ez a szerepkör az *erőforrás* -vagy *erőforráscsoport* szintjén Azure Database Migration Service áttelepítéshez szükséges (az alkalmazás-azonosítóval kapcsolatos további tudnivalókért tekintse meg a következő cikket: az [Azure ad-alkalmazás és az erőforrások elérésére képes egyszerű szolgáltatásnév létrehozása a portál használatával](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

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

**Egyéni szerepkör az alkalmazás-azonosítóhoz – előfizetés**. Ez a szerepkör az *előfizetés* szintjén Azure Database Migration Service áttelepítéshez szükséges.

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

A fenti JSON-t három szövegfájlban kell tárolni, és használhatja a AzureRM, az AZ PowerShell-parancsmagokat vagy az Azure CLI-t a szerepkörök létrehozásához a **New-AzureRmRoleDefinition (AzureRM)** vagy a **New-AzRoleDefinition (az)** használatával.

További információt az [Azure-erőforrások egyéni szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)című cikkben talál.

Miután létrehozta ezeket az egyéni szerepköröket, szerepkör-hozzárendeléseket kell hozzáadnia a felhasználókhoz és az alkalmazás-AZONOSÍTÓhoz a megfelelő erőforrásokhoz vagy erőforráscsoportokhöz:

* A "DMS role-app ID" szerepkört meg kell adni az áttelepítéshez használt alkalmazás-AZONOSÍTÓhoz, valamint a Storage-fiókhoz, Azure Database Migration Service-példányhoz és SQL Database felügyelt példány erőforrás-szintjeihez.
* A "DMS role-app ID-Sub" szerepkört az előfizetés szintjén kell megadni az alkalmazás-AZONOSÍTÓhoz (az erőforrás vagy az erőforráscsoport megadása sikertelen lesz). Ez a követelmény ideiglenes, amíg nincs telepítve a kód frissítése.

## <a name="expanded-number-of-roles"></a>Szerepkörök számának kibontása

Ha a Azure Active Directoryban szereplő egyéni szerepkörök száma nem jelent problémát, javasoljuk, hogy hozzon létre összesen három szerepkört. Továbbra is szüksége lesz a "DMS szerepkör-alkalmazás-azonosító – Sub" szerepkörre, de a fenti "DMS szerepkör-alkalmazás-azonosító" szerepkör az erőforrástípus két különböző szerepkörre van bontva.

**Egyéni szerepkör SQL Database felügyelt példányhoz tartozó alkalmazás-AZONOSÍTÓhoz**

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

**Egyéni szerepkör a Storage alkalmazás-AZONOSÍTÓhoz**

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

Ha szerepkört szeretne hozzárendelni a felhasználókhoz/alkalmazás-AZONOSÍTÓhoz, nyissa meg a Azure Portal, hajtsa végre a következő lépéseket:

1. Keresse meg az erőforráscsoportot vagy az erőforrást (kivéve az előfizetésre vonatkozó szerepkört), lépjen a **Access Control**elemre, és görgessen az imént létrehozott egyéni szerepkörök megkereséséhez.

2. Válassza ki a megfelelő szerepkört, válassza ki az alkalmazás AZONOSÍTÓját, majd mentse a módosításokat.

  Az alkalmazás-azonosító (k) mostantól megjelennek a **szerepkör-hozzárendelések** lapon.

## <a name="next-steps"></a>További lépések

* Tekintse át a forgatókönyv áttelepítési útmutatóját a Microsoft [Database áttelepítési útmutatójában](https://datamigration.microsoft.com/).
