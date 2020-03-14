---
title: Önálló adatbázis létrehozása
description: Hozzon létre egy Azure SQL Database önálló adatbázist a Azure Portal, a PowerShell vagy az Azure CLI használatával. A Azure Portal lekérdezési szerkesztőjével kérdezheti le az adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 03/10/2020
ms.openlocfilehash: 638adaac699bb7aa2774f5cbd37dc8394a2baee3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240519"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rövid útmutató: Azure SQL Database önálló adatbázis létrehozása

Ebben a rövid útmutatóban a Azure Portal, egy PowerShell-szkriptet vagy egy Azure CLI-szkriptet használ egy Azure SQL Database önálló adatbázis létrehozásához. Ezután lekérdezheti az adatbázist a Azure Portalban található **query Editor** használatával. 

[Egyetlen adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb központi telepítési lehetőség a Azure SQL Database számára. Egyetlen adatbázist felügyel egy [SQL Database-kiszolgálón](sql-database-servers.md)belül, amely egy adott Azure-régióban lévő [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) belsejében található. Ebben a rövid útmutatóban létrehoz egy új erőforráscsoportot és egy SQL Servert az új adatbázishoz.

Létrehozhat egyetlen adatbázist a *kiépített* vagy *kiszolgáló* nélküli számítási rétegben. A kiépített adatbázisok előre meghatározott mennyiségű számítási erőforrást foglalnak magukban, beleértve a CPU-t és a memóriát, és a két [beszerzési modell](sql-database-purchase-models.md)egyikét használják. Ez a rövid útmutató egy kiépített adatbázist hoz létre a [virtuális mag-alapú](sql-database-service-tiers-vcore.md) vásárlási modell használatával, de [DTU-alapú](sql-database-service-tiers-DTU.md) modellt is választhat. 

A kiszolgáló nélküli számítási réteg csak a virtuális mag-alapú vásárlási modellben érhető el, és a számítási erőforrások, például a processzor és a memória automatikusan méretezhető skáláját tartalmazza. Ha egyetlen adatbázist szeretne létrehozni a kiszolgáló nélküli számítási rétegben, tekintse meg [a kiszolgáló nélküli adatbázis létrehozása](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)című témakört.

## <a name="prerequisite"></a>Előfeltételek

- Aktív Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis létrehozása után a Azure Portal beépített **lekérdezési szerkesztőjével** csatlakozhat az adatbázishoz, és lekérdezheti az adatait.

1. A portálon keresse meg és válassza ki az **SQL-adatbázisok**elemet, majd válassza ki az adatbázist a listából.
1. Az adatbázis **SQL Database** lapján a bal oldali menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.
1. Adja meg a kiszolgáló-rendszergazdai bejelentkezési adatait, majd kattintson **az OK gombra**.
   
   ![Bejelentkezés a lekérdezés-szerkesztőbe](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Adja meg a következő lekérdezést a **lekérdezéstervező** ablaktáblán.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Válassza a **Futtatás**lehetőséget, majd tekintse át a lekérdezési eredményeket az **eredmények** ablaktáblán.

   ![A lekérdezési szerkesztő eredményei](./media/sql-database-single-database-get-started/query-editor-results.png)

1. A **Lekérdezés-szerkesztő** oldal bezárásához kattintson az **OK gombra** , amikor a rendszer a nem mentett módosítások elvetésére kéri.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, a kiszolgálót és az önálló adatbázist, és folytassa a következő lépésekkel, és megtudhatja, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Ha elkészült ezeknek az erőforrásoknak a használatával, törölheti a létrehozott erőforráscsoportot, amely a kiszolgálót és az önálló adatbázist is törli.

# <a name="portal"></a>[Portal](#tab/azure-portal)

A **myResourceGroup** és az összes erőforrás törlése a Azure Portal használatával:

1. A portálon keresse meg és válassza ki az **erőforráscsoportok**elemet, majd válassza a **myResourceGroup** elemet a listából.
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése**elemet.
1. **A írja be az erőforráscsoport nevét**mezőbe írja be a *myResourceGroup*nevet, majd válassza a **Törlés**lehetőséget.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi Azure CLI-parancsot az erőforráscsoport neve alapján:

```azurecli-interactive
az group delete --name <your resource group>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi PowerShell-parancsmagot az erőforráscsoport neve alapján:

 ```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---
## <a name="next-steps"></a>Következő lépések

Az adatbázis [összekötése és lekérdezése](sql-database-connect-query.md) különböző eszközök és nyelvek használatával:
> [!div class="nextstepaction"]
> [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
> 
> [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
