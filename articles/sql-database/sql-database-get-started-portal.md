---
title: 'Azure Portal: SQL-adatbázis létrehozása | Microsoft Docs'
description: SQL Database logikai kiszolgáló, kiszolgálószintű tűzfalszabályok és adatbázisok létrehozása és lekérdezése az Azure Portal használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165007"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Azure SQL Database létrehozása az Azure Portalon

Ez a rövid útmutató végigvezeti azon, hogyan hozhat létre SQL-adatbázist az Azure-ban a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) segítségével. Az Azure SQL Database egy adatbázis-szolgáltatási ajánlat, amellyel magas rendelkezésre állású SQL Server-adatbázisokat futtathat és méretezhet a felhőben. Ez a rövid útmutató bemutatja, hogyan teheti meg az SQL Database-adatbázisok létrehozásának, majd lekérdezésének első lépéseit az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

  >[!NOTE]
  >Ez az oktatóanyag a DTU-alapú vásárlási modellt használja, de a [vCore-alapú vásárlási modell](sql-database-service-tiers-vcore.md) szintén elérhető.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers-dtu.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre.

Kövesse az alábbi lépéseket az Adventure Works LT mintaadatokat tartalmazó SQL-adatbázis létrehozásához.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Az **Új** oldalon válassza az **Adatbázisok** lehetőséget, majd az **Új** oldal **SQL Database** területén válassza a **Létrehozás** lehetőséget.

   ![adatbázis létrehozása-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Töltse ki az SQL Database űrlapját a következő információkkal az előző képen látható módon:   

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Adatbázis neve** | mySampleDatabase | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport**  | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Forrás kiválasztása** | Minta (AdventureWorksLT) | Betölti az AdventureWorksLT sémát és adatokat az új adatbázisba. |

   > [!IMPORTANT]
   > Az űrlapon a mintaadatbázist kell kiválasztania, mivel a rövid útmutató hátralévő részében ezt használjuk majd.
   >

4. A **Server** (Kiszolgáló) területen kattintson a **Configure required settings** (Kötelező beállítások konfigurálása) elemre, majd töltse ki az SQL-kiszolgáló (logikai kiszolgáló) űrlapját a következő adatokkal a következő képen látható módon:   

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. |
   | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább 8 karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
   | **Előfizetés** | Az Ön előfizetése | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

   > [!IMPORTANT]
   > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.
   >  

   ![adatbázis-kiszolgáló létrehozása](./media/sql-database-get-started-portal/create-database-server.png)

5. Miután végzett az űrlappal, kattintson a **Kiválasztás** gombra.

6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-szám és a tárterületméret megadásához. Fedezze fel a DTU-k és a tárterület mennyiségének az egyes szolgáltatásszinteken elérhető lehetséges beállításait.

   > [!IMPORTANT]
   > Az 1 TB-ot meghaladó Prémium szintű tárterület az összes régióban elérhető, kivéve a következőket: Egyesült Királyság északi régiója, USA nyugati középső régiója, Egyesült Királyság 2. déli régiója, Kelet-Kína, USA Védelmi Minisztériuma – Középső régió, Közép-Németország, USA Védelmi Minisztériuma – Keleti régió, USA-beli államigazgatás délnyugati régiója, USA-beli államigazgatás déli középső régiója, Északkelet-Németország, Észak-Kína és USA-beli államigazgatás keleti régiója. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Ehhez a rövid útmutatóhoz válassza a **Standard** szolgáltatásszintet, majd a csúszkával válassza ki a **10 DTU (S0)** egységet, illetve **1** GB tárhelyet.

   ![adatbázis létrehozása-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. A **Kiegészítő tárterület** beállítás használatához el kell fogadnia az előzetes verziójú szolgáltatás feltételeit.

   > [!IMPORTANT]
   > Az 1 TB-ot meghaladó Prémium szintű tárterület az összes régióban elérhető, kivéve a következőket: USA nyugati középső régiója, Kelet-Kína, US DoD – középső régió, US Gov Iowa, Közép-Németország, US DoD – keleti régió, US Gov délnyugati régió, Északkelet-Németország, Észak-Kína. Más régiókban a Prémium szinthez tartozó tárterület maximuma 1 TB. Lásd: [P11–P15 – Aktuális korlátozások]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. A kiszolgálószint, a DTU-szám és a tárterületméret kiválasztása után kattintson az **Alkalmaz** gombra.  

10. Most, hogy kitöltötte az SQL Database űrlapját, kattintson a **Létrehozás** gombra az adatbázis létrehozásához. Az üzembe helyezés eltarthat néhány percig.

11. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

     ![értesítés](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Az SQL-adatbázis lekérdezése

Most, miután létrehozott egy mintaadatbázist az Azure-ban, az Azure Portalon található beépített lekérdezési eszközzel ellenőrizzük, hogy tud-e csatlakozni az adatbázishoz, és le tudja-e kérdezni az adatokat.

1. Az adatbázishoz tartozó SQL Database oldalon kattintson a **Lekérdezésszerkesztő (előzetes verzió)** elemre a bal oldali menüben, majd kattintson a **Bejelentkezés** gombra.

   ![bejelentkezés](./media/sql-database-get-started-portal/query-editor-login.png)

2. Válassza az SQL Server-hitelesítést, adja meg a szükséges bejelentkezési adatokat, és kattintson az **OK** gombra a bejelentkezéshez.

3. Miután hitelesítette magát a **kiszolgáló rendszergazdájaként**, írja be a következő lekérdezést a lekérdezésszerkesztő panelén.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Kattintson a **Futtatás** gombra, majd tekintse át a lekérdezési eredményeket az **Eredmények** ablaktáblán.

   ![lekérdezésszerkesztő: eredmények](./media/sql-database-get-started-portal/query-editor-results.png)

5. Zárja be a **Lekérdezésszerkesztő** lapot, majd kattintson az **OK** gombra a nem mentett szerkesztések elvetéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné a rövid útmutató során létrehozott erőforrásokat, végezze el a következő lépéseket.


1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, létre kell hoznia egy kiszolgálószintű tűzfalszabályt a helyszíni eszközökből való csatlakozáshoz. Lásd: [Kiszolgálószintű tűzfalszabály létrehozása](sql-database-get-started-portal-firewall.md)
- Kiszolgálószintű tűzfalszabály létrehozása esetében [csatlakozhat és lekérdezhet](sql-database-connect-query.md) kedvenc eszközeinek vagy nyelveinek egyikét használva, beleértve a következőt:
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Adatbázisok az Azure CLI-vel való létrehozásáról lásd: [Azure CLI-minták](sql-database-cli-samples.md)
- Adatbázisok az Azure PowerShell-lel való létrehozásáról lásd: [Azure PowerShell-minták](sql-database-powershell-samples.md)