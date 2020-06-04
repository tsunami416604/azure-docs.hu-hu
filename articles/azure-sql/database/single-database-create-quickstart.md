---
title: Önálló adatbázis létrehozása
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzon létre egyetlen adatbázist a Azure SQL Database. A Azure Portal lekérdezési szerkesztőjével kérdezheti le az adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: 1ae3159365e63159b8ed62c09bc20441ba65d8aa
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343846"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rövid útmutató: Azure SQL Database önálló adatbázis létrehozása

Ebben a rövid útmutatóban a Azure Portal, egy PowerShell-parancsfájl vagy egy Azure CLI-parancsfájl használatával hozhat létre egyetlen adatbázist a Azure SQL Database. Ezután lekérdezheti az adatbázist a Azure Portalban található **query Editor** használatával.

[Egyetlen adatbázis](single-database-overview.md) a leggyorsabb és legegyszerűbb lehetőség a Azure SQL Database számára. Egyetlen adatbázist kezel egy [kiszolgálón](logical-servers.md)belül, amely egy adott Azure-régióban található [Azure-erőforráscsoport](../../active-directory-b2c/overview.md) belsejében van. Ebben a rövid útmutatóban létrehoz egy új erőforráscsoportot és egy kiszolgálót az új adatbázishoz.

Létrehozhat egyetlen adatbázist a *kiépített* vagy *kiszolgáló* nélküli számítási rétegben. A kiépített adatbázisok előre meghatározott mennyiségű számítási erőforrást foglalnak magukban, beleértve a CPU-t és a memóriát, és a két [beszerzési modell](purchasing-models.md)egyikét használják. Ez a rövid útmutató egy kiépített adatbázist hoz létre a [virtuális mag-alapú](service-tiers-vcore.md) vásárlási modell használatával, de [DTU-alapú](service-tiers-dtu.md) modellt is választhat.

A kiszolgáló nélküli számítási réteg csak a virtuális mag-alapú vásárlási modellben érhető el, és a számítási erőforrások, például a processzor és a memória (CPU és memória) egy, előre meghatározott skáláját tartalmazza. Ha egyetlen adatbázist szeretne létrehozni a kiszolgáló nélküli számítási rétegben, tekintse meg [a kiszolgáló nélküli adatbázis létrehozása](serverless-tier-overview.md#create-a-new-database-in-the-serverless-compute-tier)című témakört.

## <a name="prerequisite"></a>Előfeltétel

- Aktív Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Az adatbázis létrehozása után a Azure Portal beépített **lekérdezési szerkesztőjével** csatlakozhat az adatbázishoz, és lekérdezheti az adatait.

1. A portálon keresse meg és válassza ki az **SQL-adatbázisok**elemet, majd válassza ki az adatbázist a listából.
1. Az adatbázis **SQL Database** lapján a bal oldali menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget.
1. Adja meg a kiszolgáló-rendszergazdai bejelentkezési adatait, majd kattintson **az OK gombra**.

   ![Bejelentkezés a lekérdezés-szerkesztőbe](./media/single-database-create-quickstart/query-editor-login.png)

1. Adja meg a következő lekérdezést a **lekérdezéstervező** ablaktáblán.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Válassza a **Futtatás**lehetőséget, majd tekintse át a lekérdezési eredményeket az **eredmények** ablaktáblán.

   ![A lekérdezési szerkesztő eredményei](./media/single-database-create-quickstart/query-editor-results.png)

1. A **Lekérdezés-szerkesztő** oldal bezárásához kattintson az **OK gombra** , amikor a rendszer a nem mentett módosítások elvetésére kéri.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, a kiszolgálót és az önálló adatbázist, és folytassa a következő lépésekkel, és megtudhatja, hogyan csatlakozhat az adatbázishoz, és hogyan kérdezheti le azokat különböző módszerekkel.

Ha elkészült ezeknek az erőforrásoknak a használatával, törölheti a létrehozott erőforráscsoportot, amely a kiszolgálót és az önálló adatbázist is törli.

### <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

A **myResourceGroup** és az összes erőforrás törlése a Azure Portal használatával:

1. A portálon keresse meg és válassza ki az **erőforráscsoportok**elemet, majd válassza a **myResourceGroup** elemet a listából.
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése**elemet.
1. **A írja be az erőforráscsoport nevét**mezőbe írja be a *myResourceGroup*nevet, majd válassza a **Törlés**lehetőséget.

### <a name="the-azure-cli"></a>[Az Azure CLI](#tab/azure-cli)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi Azure CLI-parancsot az erőforráscsoport neve alapján:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az erőforráscsoport és az összes erőforrás törléséhez futtassa az alábbi PowerShell-parancsmagot az erőforráscsoport neve alapján:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Következő lépések

Az adatbázis [összekötése és lekérdezése](connect-query-content-reference-guide.md) különböző eszközök és nyelvek használatával:
> [!div class="nextstepaction"]
> [Kapcsolódás és lekérdezés az SQL Server Management Studióval](connect-query-ssms.md)
>
> [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
 