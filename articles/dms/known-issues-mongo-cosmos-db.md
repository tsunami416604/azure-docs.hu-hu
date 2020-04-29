---
title: 'Ismert problémák: áttelepítés a MongoDB-ből az Azure CosmosDB-be'
titleSuffix: Azure Database Migration Service
description: Ismerje meg az ismert problémákat és az áttelepítési korlátozásokat a MongoDB-ről Azure Cosmos DB-re való áttelepítéssel a Azure Database Migration Service használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878051"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Ismert problémák/áttelepítési korlátozások a MongoDB-ből Azure Cosmos DB API-MongoDB való áttelepítéssel

A MongoDB-ről Cosmos DB API-MongoDB való áttelepítéssel kapcsolatos ismert problémák és korlátozások a következő szakaszokban olvashatók.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Az áttelepítés nem sikerül a helytelen SSL-tanúsítvány használata miatt

* **Tünet**: Ez a probléma akkor jelentkezik, ha egy felhasználó nem tud csatlakozni a MongoDB-kiszolgálóhoz. Annak ellenére, hogy az összes tűzfal-port nyitva van, a felhasználó továbbra sem tud kapcsolatot létesíteni.

| Ok         | Megoldás: |
| ------------- | ------------- |
| Ha a Azure Database Migration Service önaláírt tanúsítványt használ, az nem megfelelő SSL-tanúsítvány miatt meghiúsulhat az áttelepítés. A hibaüzenet tartalmazhat "a távoli tanúsítvány érvénytelen az érvényesítési eljárásnak megfelelően." | Használjon valódi tanúsítványt a CA-tól.  Az önaláírt tanúsítványok általában csak belső tesztekben használatosak. Ha egy HITELESÍTÉSSZOLGÁLTATÓ-szolgáltatótól származó valódi tanúsítványt telepít, akkor a Azure Database Migration Service probléma nélkül használhatja az SSL-t (a kapcsolatok Cosmos DB SSL használatával Mongo API-val).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Nem lehet lekérni a DMS-ben leképezni kívánt adatbázisok listáját

* **Tünet**: nem sikerült beolvasni az adatbázis- **beállítás** paneljét, ha az **Azure Storage-módból származó adatok** használatával használja a **forrás kiválasztása** panelt.

| Ok         | Megoldás: |
| ------------- | ------------- |
| A Storage-fiókhoz tartozó kapcsolási karakterláncból hiányzik az SAS-információ, ezért nem hitelesíthető. | Hozza létre az SAS-t a blob-tárolóban Storage Explorerban, és használja az URL-címet a tároló sas-adataival a forrás részletes kapcsolatok karakterlánca.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Az adatbázis nem támogatott verziójának használata

* **Tünet**: az áttelepítés meghiúsul.

| Ok         | Megoldás: |
| ------------- | ------------- |
| A MongoDB nem támogatott verziójáról próbál áttérni Azure Cosmos DBra. | Mivel a MongoDB új verziói jelennek meg, a rendszer ellenőrzi, hogy kompatibilis-e a Azure Database Migration Serviceokkal, és a szolgáltatás rendszeres időközönként frissül-e a legújabb verzió (k) elfogadásához. Ha azonnali Migrálás szükséges, megkerülő megoldásként exportálhatja az adatbázisokat/gyűjteményeket az Azure Storage-ba, majd a forrást az eredményül kapott memóriaképre irányíthatja. Hozza létre az SAS-t a blob-tárolóban Storage Explorerban, majd használja az URL-címet a tároló SAS-adataival a forrás részletes kapcsolatainak karakterlánca alapján.<br><br> |

## <a name="next-steps"></a>További lépések

* Tekintse meg az oktatóanyag [áttelepíthető MongoDB Azure Cosmos db API-ját a MongoDB online-hoz a DMS használatával](tutorial-mongodb-cosmos-db-online.md).
* Tekintse át az oktatóanyagot a [MongoDB Azure Cosmos db API-MongoDB való átállításához a DMS használatával](tutorial-mongodb-cosmos-db.md).