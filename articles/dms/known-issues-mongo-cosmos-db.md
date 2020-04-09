---
title: 'Ismert problémák: Áttelepítés a MongoDB-ról az Azure CosmosDB-re'
titleSuffix: Azure Database Migration Service
description: Ismerje meg a MongoDB-ról az Azure Cosmos DB-re az Azure Database Migration Service használatával történő áttelepítések ismert problémáit és áttelepítési korlátait.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878051"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB-ról az Azure Cosmos DB MongoDB-ra vonatkozó API-jával kapcsolatos ismert problémák/áttelepítési korlátozások

A MongoDB-ról a Cosmos DB MongoDB-ra vonatkozó API-jára való áttéréssel kapcsolatos ismert problémákat és korlátozásokat a következő szakaszok ismertetik.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Az áttelepítés sikertelen a helytelen SSL-tanúsítvány használata miatt

* **Jelenség**: Ez a probléma akkor jelentkezik, ha a felhasználó nem tud csatlakozni a MongoDB forráskiszolgálóhoz. Annak ellenére, hogy az összes tűzfalport nyitva van, a felhasználó továbbra sem tud csatlakozni.

| Ok         | Megoldás: |
| ------------- | ------------- |
| Önaláírt tanúsítvány használata az Azure Database Migration Service vezethet az áttelepítés idl. A hibaüzenet ben szerepelhet: "A távoli tanúsítvány az érvényesítési eljárás nak megfelelően érvénytelen." | Használjon eredeti tanúsítványt a hitelesítésszolgáltatótól.  Az önaláírt certeket általában csak belső tesztekben használják. Ha egy hitelesítésszolgáltatói hatóságtól telepít egy eredeti tanúsítványt, probléma nélkül használhatja az SSL-t az Azure Database Migration Service szolgáltatásban (a Cosmos DB-vel létesített kapcsolatok SSL-t használnak a Mongo API-n keresztül).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Nem lehet leképezni a DMS-ben leképezésre kerülő adatbázisok listáját

* **Jelenség:** Nem lehet beszerezni az ADATBÁZIS-lista az **adatbázis-beállítás** panelen, ha az Adatok az **Azure Storage** módban a **Forrás kiválasztása** panelen.

| Ok         | Megoldás: |
| ------------- | ------------- |
| A tárfiók kapcsolati karakterlánca hiányzik a SAS-adatokból, ezért nem hitelesíthető. | Hozza létre a SAS-t a Blob container a Storage Explorer ben, és használja az URL-t a tároló SAS-adatok, mint a forrás részletes kapcsolat ihpedig.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Az adatbázis nem támogatott verziójának használata

* **Tünet**: Az áttelepítés sikertelen.

| Ok         | Megoldás: |
| ------------- | ------------- |
| A MongoDB nem támogatott verziójából próbál áttérni az Azure Cosmos DB-re. | A MongoDB új verzióinak megjelenésekor a rendszer teszteli őket az Azure Database Migration Service szolgáltatással való kompatibilitás biztosítása érdekében, és a szolgáltatás rendszeres időközönként frissül, hogy elfogadja a legújabb verzió(ka)t. Ha azonnali áttelepítésre van szükség, kerülő megoldásként exportálhatja az adatbázisokat/gyűjteményeket az Azure Storage-ba, és irányíthatja a forrást az eredményül kapott memóriaképre. Hozza létre a SAS-t a blobtárolón a Storage Explorerben, majd használja az URL-címet a tároló SAS-adataival a forrásrészletkapcsolati karakterláncként.<br><br> |

## <a name="next-steps"></a>További lépések

* Tekintse meg a [MongoDB áttelepítése az Azure Cosmos DB API-t a MongoDB online DMS használatával](tutorial-mongodb-cosmos-db-online.md).
* Tekintse meg a [MongoDB áttelepítése az Azure Cosmos DB API-t a MongoDB offline dms használatával.](tutorial-mongodb-cosmos-db.md)