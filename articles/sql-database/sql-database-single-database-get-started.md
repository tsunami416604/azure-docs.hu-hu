---
title: Egyetlen adatbázis létrehozása
description: Hozzon létre egy Azure SQL Database egyetlen adatbázist az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül. Az azure-portállekérdezési lekérdezése a Lekérdezésszerkesztővel.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240519"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rövid útmutató: Azure SQL Database egyetlen adatbázis létrehozása

Ebben a rövid útmutatóban az Azure Portalon, egy PowerShell-parancsfájlt vagy egy Azure CLI-parancsfájlt használ egy Azure SQL Database egyetlen adatbázis létrehozásához. Ezután lekérdezheti az adatbázist az Azure Portal **lekérdezésszerkesztőjével.** 

[Egyetlen adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembe helyezési lehetőség az Azure SQL Database számára. Egyetlen adatbázist kezel het egy [SQL Database-kiszolgálón](sql-database-servers.md)belül, amely egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül van egy adott Azure-régióban. Ebben a rövid útmutatóban új erőforráscsoportot és SQL-kiszolgálót hoz létre az új adatbázishoz.

Létrehozhat egyetlen adatbázist a *kiépített* vagy *kiszolgáló nélküli* számítási rétegben. A kiépített adatbázis előre lefoglalt egy fix mennyiségű számítási erőforrások, beleértve a processzor és a memória, és használja a két [beszerzési modellek.](sql-database-purchase-models.md) Ez a rövid útmutató létrehoz egy kiépített adatbázist a [virtuálismag-alapú](sql-database-service-tiers-vcore.md) vásárlási modell használatával, de [dtu-alapú](sql-database-service-tiers-DTU.md) modellt is választhat. 

A kiszolgáló nélküli számítási szint csak a virtuálismag-alapú vásárlási modellben érhető el, és a számítási erőforrások automatikusskálázott tartományával rendelkezik, beleértve a processzort és a memóriát. Ha egyetlen adatbázist szeretne létrehozni a kiszolgáló nélküli számítási rétegben, olvassa el a [Kiszolgáló nélküli adatbázis létrehozása](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)című témakört.

## <a name="prerequisite"></a>Előfeltétel

- Aktív Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Egyetlen adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Adatbázis lekérdezése

Az adatbázis létrehozása után az Azure Portal beépített **lekérdezésszerkesztőjével** csatlakozhat az adatbázishoz, és lekérdezheti az adatokat.

1. A portálon keresse meg és jelölje ki az **SQL-adatbázisokat,** majd válassza ki az adatbázist a listából.
1. Az adatbázis **SQL Database** lapján válassza a bal oldali menü **Lekérdezésszerkesztő (előnézet)** parancsát.
1. Adja meg a kiszolgáló rendszergazdájának bejelentkezési adatait, és válassza **az OK gombot.**
   
   ![Bejelentkezés a Lekérdezésszerkesztőbe](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Írja be a következő lekérdezést a **Lekérdezésszerkesztő** ablaktáblába.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Válassza **a Futtatás**lehetőséget, majd tekintse át a lekérdezés eredményeit az **Eredmények** ablaktáblán.

   ![Lekérdezésszerkesztő eredményei](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Zárja be a **Lekérdezésszerkesztő** lapot, és a nem mentett módosítások elvetése kor válassza az **OK** gombot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, a kiszolgálót és az egyetlen adatbázist a következő lépésekhez, és ismerje meg, hogyan csatlakozhat és kérdezheti le az adatbázist különböző módszerekkel.

Ha befejezte ezeknek az erőforrásoknak a használatát, törölheti a létrehozott erőforráscsoportot, amely a kiszolgálót és az adatbázison belüli egyetlen adatbázist is törli.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A **myResourceGroup** és az összes erőforrás törlése az Azure Portalhasználatával:

1. A portálon keressen rá **az Erőforráscsoportokra,** és válassza ki a lehetőséget, majd válassza a **myResourceGroup** elemet a listából.
1. Az erőforráscsoport lapon válassza az **Erőforráscsoport törlése**lehetőséget.
1. Az **Erőforráscsoport nevének megadása**csoportban írja be a *myResourceGroup*parancsot, majd válassza a **Törlés**lehetőséget.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa a következő Azure CLI parancsot az erőforráscsoport nevének használatával:

```azurecli-interactive
az group delete --name <your resource group>
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa a következő PowerShell-parancsmavet az erőforráscsoport nevének használatával:

 ```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---
## <a name="next-steps"></a>További lépések

Az adatbázis [csatlakoztatása és lekérdezése](sql-database-connect-query.md) különböző eszközökkel és nyelvekkel:
> [!div class="nextstepaction"]
> [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
> 
> [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
