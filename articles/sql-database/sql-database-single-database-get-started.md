---
title: 'Önálló adatbázis létrehozása – Azure SQL Database '
description: Egyetlen adatbázis létrehozása és lekérdezése Azure SQL Database a Azure Portal, a PowerShell és az Azure CLI használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 09/09/2019
ms.openlocfilehash: bf5a39fe133dc1adb46d4968ab7aa13187576f33
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687220"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Gyors útmutató: önálló adatbázis létrehozása Azure SQL Database a Azure Portal, a PowerShell és az Azure CLI használatával

[Egyetlen adatbázis](sql-database-single-database.md) létrehozása a leggyorsabb és legegyszerűbb üzembe helyezési lehetőség az adatbázisok Azure SQL Database-ben való létrehozásához. Ez a rövid útmutató bemutatja, hogyan hozhat létre és kérdez le egyetlen adatbázist a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/). 

Az ebben a rövid útmutatóban szereplő összes lépésnél jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Egyetlen adatbázis hozható létre a kiépített vagy kiszolgáló nélküli számítási rétegben.

- A kiépített számítási réteg egyetlen adatbázisa előre lefoglalt számítási erőforrások (például CPU és memória) rögzített mennyiségét használja a két [beszerzési modell](sql-database-purchase-models.md)egyikével.
- A kiszolgáló nélküli számítási réteg egyetlen adatbázisa számos számítási erőforrást tartalmaz, beleértve a CPU-t és a memóriát, amely automatikusan skálázható, és csak a [virtuális mag-alapú vásárlási modellekben](sql-database-service-tiers-vcore.md)érhető el.

Egyetlen adatbázis létrehozásakor egy [SQL Database-kiszolgálót](sql-database-servers.md) is meg kell adnia a kezeléséhez, és egy adott régióban lévő [Azure-erőforráscsoporthoz](../azure-resource-manager/resource-group-overview.md) helyezheti azt.

> [!NOTE]
> Ez a rövid útmutató a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)használja, de a [DTU-alapú vásárlási modell](sql-database-service-tiers-DTU.md) is elérhető.

AdventureWorksLT-mintaadatok tartalmazó önálló adatbázis létrehozása:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Most, hogy létrehozta az adatbázist, használja a Azure Portal beépített lekérdezési eszközét az adatbázishoz való kapcsolódáshoz és az adatlekérdezéshez.

1. Az adatbázis **SQL Database** lapján a bal oldali menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.

   ![Bejelentkezés a lekérdezés-szerkesztőbe](./media/sql-database-get-started-portal/query-editor-login.png)

2. Adja meg a bejelentkezési adatait, majd kattintson **az OK gombra**.
3. Adja meg a következő lekérdezést a **lekérdezéstervező** ablaktáblán.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Válassza a **Futtatás**lehetőséget, majd tekintse át a lekérdezési eredményeket az **eredmények** ablaktáblán.

   ![A lekérdezési szerkesztő eredményei](./media/sql-database-get-started-portal/query-editor-results.png)

5. A **Lekérdezés-szerkesztő** oldal bezárásához kattintson az **OK gombra** , amikor a rendszer a nem mentett módosítások elvetésére kéri.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg ezt az erőforráscsoportot, az adatbázis-kiszolgálót és az önálló adatbázist, ha a [következő lépésekre](#next-steps)szeretne lépni. A következő lépések bemutatják, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Ha elkészült ezeknek az erőforrásoknak a használatával, a következőképpen törölheti őket:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok**, majd a **myResourceGroup**lehetőséget.
2. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése**elemet.
3. Adja meg a *myResourceGroup* a mezőben, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálói szintű tűzfalszabály, amely a helyszíni vagy távoli eszközökről csatlakozik az önálló adatbázishoz. További információ: [kiszolgálói szintű tűzfalszabály létrehozása](sql-database-server-level-firewall-rule.md).
- A kiszolgálói szintű tűzfalszabály létrehozása után több különböző eszköz és nyelv használatával kapcsolódhat az adatbázishoz, [és lekérdezheti](sql-database-connect-query.md) azt.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Ha egyetlen adatbázist szeretne létrehozni a kiépített számítási rétegben az Azure CLI használatával, tekintse meg az [Azure CLI-minták](sql-database-cli-samples.md)című részt.
- Ha Azure PowerShell használatával szeretne létrehozni egyetlen adatbázist a kiépített számítási szinten, tekintse meg [Azure PowerShell mintákat](sql-database-powershell-samples.md).
- Ha egyetlen adatbázist szeretne létrehozni a kiszolgáló nélküli számítási rétegben az Azure PowerShell használatával, tekintse meg a [kiszolgáló nélküli adatbázis létrehozása](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)című témakört.
