---
title: 'Az Azure Portalon: Hozzon létre egy önálló adatbázis – Azure SQL Database |} A Microsoft Docs'
description: Adatbázis létrehozása és lekérdezése egy egyetlen Azure SQL Database az Azure portal használatával.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 3a0f3154142dc02a9ab48bc288c543b02fef46ea
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389073"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Gyors útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával

Létrehozás egy [önálló adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembehelyezési megoldás az Azure SQL Database-adatbázis létrehozása. Ez a rövid útmutató bemutatja, hogyan hozhat létre, és majd lekérdezheti az adatbázist az Azure portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben a rövid útmutatóban minden lépéseiért jelentkezzen be a [az Azure portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Önálló adatbázis vagy hozható létre a kiépített vagy kiszolgáló nélküli számítási kapacitás (előzetes verzió).

- A kiépített számítási kapacitás egy önálló adatbázis rendelkezik a meghatározott számú előre lefoglalt számítási erőforrások és a egy-két memória- és tárolási erőforrásokat készletét [vásárlási modellek](sql-database-purchase-models.md).
- A kiszolgáló nélküli számítási kapacitás egy önálló adatbázis rendelkezik a számítási erőforrásokat, amelyek a rendszer automatikusan méretezett számos és a egy megadott méretű memória magonként és a tárolási erőforrások egy megadott mennyiséget, és csak a [Virtuálismag-alapú vásárlási modell ](sql-database-service-tiers-vcore.md).

Ha létrehoz egy adatbázist, akkor is definiálni egy [SQL Database-kiszolgáló](sql-database-servers.md) felügyelni, és helyezze belül [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy meghatározott régióban.

> [!NOTE]
> Ebben a rövid útmutatóban használt a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) és a [kiszolgáló nélküli](sql-database-serverless.md) compute-kapacitás, de a [DTU-alapú vásárlási modell](sql-database-service-tiers-DTU.md) is rendelkezésre áll.

Az AdventureWorksLT mintaadatokat tartalmazó önálló adatbázis létrehozása:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza ki **adatbázisok** majd **SQL Database** megnyitásához a **SQL-adatbázis létrehozása** lap.

   ![Önálló adatbázis létrehozása](./media/sql-database-get-started-portal/create-database-1.png)

3. Az a **alapjai** lap a **Project Details** szakaszban adja meg a következő értékeket:

   - **Előfizetés**: Legördülő lista, és válassza ki a megfelelő előfizetés, ha nem jelenik meg.
   - **Erőforráscsoport**: Válassza ki **új létrehozása**, típus `myResourceGroup`, és válassza ki **OK**.

     ![Új SQL database – az egyszerű lap](media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Az a **adatbázis adatainak** szakaszban adja meg a következő értékeket:

   - **Adatbázis neve**: Írja be a `mySampleDatabase` (igen) kifejezést.
   - **Kiszolgáló**: Válassza ki **új létrehozása** , és adja meg a következő értékeket, majd **kiválasztása**.
       - **Kiszolgálónév**: Típus `mysqlserver`; egyedi-e számok együtt.
       - **Kiszolgáló-rendszergazdai bejelentkezés**: Gépelje be: `azureuser`.
       - **Jelszó**: Írjon be egy összetett jelszót, amely megfelel a jelszókövetelmények.
       - **Hely**: Például a legördülő listából válassza ki a helyet `West US 2`.

         ![Új kiszolgáló](media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Jegyezze fel a kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát, így bejelentkezhet a kiszolgáló és az adatbázisok ennél és a többi rövid útmutató. Ha elfelejti a bejelentkezéshez vagy a jelszavát, kérje le a bejelentkezési nevét, vagy alaphelyzetbe állíthatja a jelszót a a **az SQL server** lapot. Megnyitásához a **az SQL server** lapra, jelölje be a kiszolgáló nevét, az adatbázis **áttekintése** lap az adatbázis létrehozása után.

        ![SQL-adatbázis részletei](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Rugalmas SQL-készlet használandó**: Válassza ki a **nem** lehetőséget.
   - **Számítási és tárolási**: Válassza ki **konfigurálása adatbázis** ebben a rövid útmutatóban válassza **Virtuálismag-alapú vásárlási lehetőségek**

     ![Virtuálismag-alapú vásárlási lehetőségek](media/sql-database-get-started-portal/create-database-vcore.png)

   - Válassza ki **kiszolgáló nélküli**.

     ![kiszolgáló nélküli számítási kapacitás](media/sql-database-get-started-portal/create-database-serverless.png)

   - Tekintse át a beállításokat a **virtuális magok maximális**, **minimális virtuális magok**, **automatikus szüneteltetési késleltetés**, és **adatok maximális mérete**. Ezek igény szerint módosíthatja.
   - Fogadja el az előzetes verzióra vonatkozó, majd kattintson a **OK**.
   - Kattintson az **Alkalmaz** gombra.

5. Válassza ki a **további beállítás** fülre. 
6. Az a **adatforrás** szakasz **meglévő adatainak felhasználását**válassza `Sample`. 

   ![További SQL-adatbázis-beállítások](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ügyeljen arra, hogy válassza ki a **minta (AdventureWorksLT)** adatokat, így könnyen is követheti ezzel és más, amely a tárolt adatok Azure SQL Database gyors útmutatók.

7. Alapértelmezett, és válassza ki a többi értéket hagyja **felülvizsgálat + létrehozás** a képernyő alján.
8. Tekintse át a végső beállításokat, és válassza ki **létrehozás**.

9. Az a **SQL Database** képernyő, válassza ki **létrehozás** üzembe helyezéséhez és az erőforráscsoport, kiszolgáló és adatbázis üzembe helyezése.

## <a name="query-the-database"></a>Az adatbázis lekérdezése

Most, hogy létrehozta az adatbázist, használja a beépített lekérdezési eszközzel az Azure Portalon kapcsolódni az adatbázishoz, és lekérdezheti az adatokat.

1. Az a **SQL Database** az adatbázisra, válassza a lap **Lekérdezésszerkesztő (előzetes verzió)** a bal oldali menüben.

   ![Jelentkezzen be a szerkesztő lekérdezése](./media/sql-database-get-started-portal/query-editor-login.png)

2. Adja meg a bejelentkezési adatait, és válassza ki **OK**.
3. Adja meg a következő lekérdezést a **Lekérdezésszerkesztő** ablaktáblán.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Válassza ki **futtatása**, majd tekintse át a lekérdezési eredményeket az **eredmények** ablaktáblán.

   ![Lekérdezésszerkesztő: eredmények](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zárja be a **Lekérdezésszerkesztő** lapon, és válassza ki **OK** amikor a rendszer kéri, hogy a nem mentett szerkesztések elvetéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg az erőforráscsoportot, adatbázis-kiszolgáló és önálló adatbázist, ha szeretne belépni a [további lépések](#next-steps). A következő lépések bemutatják, hogyan történő csatlakozásról és lekérdezésről különböző módszereket használ az adatbázis.

Ha elkészült, használja ezeket az erőforrásokat, törölheti őket a következő:

1. Az Azure Portal bal oldali menüből válassza ki a **erőforráscsoportok**, majd válassza ki **myResourceGroup**.
2. Az erőforráscsoport lapján, válassza ki a **erőforráscsoport törlése**.
3. Adja meg *myResourceGroup* a mezőt, és válassza ki a **törlése**.

## <a name="next-steps"></a>További lépések

- Hozzon létre egy kiszolgálószintű tűzfalszabályt, amely az egyetlen adatbázis csatlakozhat a helyszíni vagy távoli eszközök. További információkért lásd: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-server-level-firewall-rule.md).
- Miután létrehozott egy kiszolgálószintű tűzfalszabályt [csatlakozásról és lekérdezésről](sql-database-connect-query.md) számos különböző eszközöket és nyelveket használ az adatbázis.
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Önálló adatbázis létrehozása a kiépített számítási rétegben, Azure CLI-vel: [Azure CLI-minták](sql-database-cli-samples.md).
- Egy önálló adatbázis létrehozása az Azure PowerShell-lel kiépített számítási szinten: [Azure PowerShell-minták](sql-database-powershell-samples.md).
- Egy önálló adatbázis létrehozása az Azure Powershell használatával kiszolgáló nélküli számítási szinten: [kiszolgáló nélküli adatbázis létrehozása PowerShell használatával](sql-database-serverless.md#create-new-serverless-database-using-powershell)
