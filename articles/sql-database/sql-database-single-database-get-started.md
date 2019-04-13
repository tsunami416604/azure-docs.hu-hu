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
ms.date: 04/11/2019
ms.openlocfilehash: a5fbc58feea8779ba8a7a61dfc89158e20bd2c92
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544280"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Gyors útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával

Létrehozás egy [önálló adatbázis](sql-database-single-database.md) a leggyorsabb és legegyszerűbb üzembehelyezési megoldás az Azure SQL Database-adatbázis létrehozása. Ez a rövid útmutató bemutatja, hogyan hozhat létre, és majd lekérdezheti az adatbázist az Azure portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

Ebben a rövid útmutatóban minden lépéseiért jelentkezzen be a [az Azure portal](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Önálló adatbázis létrehozása

Önálló adatbázis az egyik két számítási, memória, IO és tárolási erőforrások egy meghatározott készletével rendelkezik [vásárlási modellek](sql-database-purchase-models.md). Ha létrehoz egy adatbázist, akkor is definiálni egy [SQL Database-kiszolgáló](sql-database-servers.md) felügyelni, és helyezze belül [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) egy meghatározott régióban.

Az AdventureWorksLT mintaadatokat tartalmazó önálló adatbázis létrehozása:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza ki **adatbázisok** majd **SQL Database** megnyitásához a **SQL-adatbázis létrehozása** lap. 

   ![Önálló adatbázis létrehozása](./media/sql-database-get-started-portal/create-database-1.png)

1. Az a **alapjai** lap a **Project Details** szakaszban adja meg a következő értékeket:

   - **Előfizetés**: Legördülő lista, és válassza ki a megfelelő előfizetés, ha nem jelenik meg.
   - **Erőforráscsoport**: Válassza ki **új létrehozása**, típus `myResourceGroup`, és válassza ki **OK**.

   ![Új SQL database – az egyszerű lap](media/sql-database-get-started-portal/new-sql-database-basics.png)


1. Az a **adatbázis adatainak** szakaszban adja meg a következő értékeket: 

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
   - **Számítási és tárolási**: Válassza ki **konfigurálása adatbázis** ennél a gyorsútmutatónál válassza ki a **Standard** szolgáltatásszintet, és a csúszka segítségével válassza ki **10 Dtu (S0)** és **1** GB tárhelyet. Kattintson az **Alkalmaz** gombra. 

    ![Szint konfigurálása](media/sql-database-get-started-portal/create-database-s1.png) 


      > [!NOTE]
      > Ebben a rövid útmutatóban használja a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md), de a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) is rendelkezésre áll.
      > [!IMPORTANT]
      > Jelenleg több mint 1 TB tárterület egységára prémium szinten érhető el minden régióban, kivéve: Kelet-Kína, Észak-Kína, közép-Németország, Északkelet-Németország, USA nyugati középső Régiója, USA védelmi Minisztériuma régiók és US Government központi. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB.  További információkért lásd: [P11 – P15 – aktuális korlátozások](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

    



1. Válassza ki a **további beállítás** fülre. 
1. Az a **adatforrás** szakasz **meglévő adatainak felhasználását**válassza `Sample`. 

   ![További SQL-adatbázis-beállítások](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ügyeljen arra, hogy válassza ki a **minta (AdventureWorksLT)** adatokat, így könnyen is követheti ezzel és más, amely a tárolt adatok Azure SQL Database gyors útmutatók.

1. Alapértelmezett, és válassza ki a többi értéket hagyja **felülvizsgálat + létrehozás** a képernyő alján. 
1. Tekintse át a végső beállításokat, és válassza ki **létrehozás**. 

8. Az a **SQL Database** képernyő, válassza ki **létrehozás** üzembe helyezéséhez és az erőforráscsoport, kiszolgáló és adatbázis üzembe helyezése.


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
- Hozzon létre egy önálló adatbázis, Azure CLI-vel, tekintse meg [Azure CLI-minták](sql-database-cli-samples.md).
- Hozzon létre egy önálló adatbázis, Azure PowerShell-lel, tekintse meg [Azure PowerShell-minták](sql-database-powershell-samples.md).
