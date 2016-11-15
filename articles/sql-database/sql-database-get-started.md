---
title: "Oktatóanyag az SQL Database használatához: SQL-adatbázis létrehozása | Microsoft Docs"
description: "Megtudhatja, hogyan kell SQL Database-alapú logikai kiszolgálót és a hozzá tartozó tűzfalszabályt beállítani, valamint SQL Database-adatbázist létrehozni és mintaadatokat betölteni. Megtanulhatja az ügyféleszközökhöz való kapcsolódás, továbbá a felhasználók és az adatbázishoz tartozó tűzfalszabály konfigurálásának módját is."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 914d5f7e6b8e9165fc8ba9021e1030b865ba5fb2


---
# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Oktatóanyag az SQL Database használatához: SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-get-started.md)
> * [C#](sql-database-get-started-csharp.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan hozhatja létre a következőket az Azure Portal használatával:

* Azure SQL Database-adatbázis létrehozása mintaadatokkal.
* Kiszolgálószintű tűzfalszabály egyetlen IP-cím vagy IP-címek tartománya számára.

Ugyanezen feladatokat [C#](sql-database-get-started-csharp.md) nyelven vagy a [PowerShell](sql-database-get-started-powershell.md) segítségével is végrehajthatja.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Az első SQL Database-adatbázis létrehozása
1. Ha jelenleg nem csatlakozik, jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Kattintson az **Új**, majd az **Adatok + tárolás** elemre, és keresse meg az **SQL Database** elemet.
   
    ![Új SQL Database-adatbázis – 1](./media/sql-database-get-started/sql-database-new-database-1.png)
3. Kattintson az **SQL Database** elemre az SQL Database panel megnyitásához. A panel tartalma az előfizetések és a meglévő objektumok (pl. meglévő kiszolgálók) számától függően eltérő lehet.
   
    ![Új SQL Database-adatbázis – 2](./media/sql-database-get-started/sql-database-new-database-2.png)
4. Az **Adatbázis neve** szövegbeviteli mezőben adjon nevet az első adatbázisának – pl. „my-database” (saját adatbázis). Zöld pipa jelzi, hogy érvényes nevet adott meg.
   
    ![Új SQL Database-adatbázis – 3](./media/sql-database-get-started/sql-database-new-database-3.png)
5. Ha több előfizetéssel rendelkezik, válasszon egyet.
6. Az **Erőforráscsoport** területen kattintson az **Új létrehozása** elemre, majd adjon nevet az első erőforráscsoportjának – pl. „my-resource-group” (saját erőforráscsoport). Zöld pipa jelzi, hogy érvényes nevet adott meg.
   
    ![Új SQL Database-adatbázis – 4](./media/sql-database-get-started/sql-database-new-database-4.png)
7. A **Forrás kiválasztása** területen kattintson a **Minta** elemre, majd a **Minta kiválasztása** területen kattintson az **AdventureWorksLT [V12]** elemre.
   
    ![Új SQL Database-adatbázis – 5](./media/sql-database-get-started/sql-database-new-database-5.png)
8. A **Kiszolgáló** területen kattintson a **Kötelező beállítások konfigurálása** elemre.
   
    ![Új SQL Database-adatbázis – 6](./media/sql-database-get-started/sql-database-new-database-6.png)
9. A Kiszolgáló panelen kattintson az **Új kiszolgáló létrehozása** elemre. Az Azure SQL Database-adatbázisok egy kiszolgálóobjektumban jönnek létre, amely lehet új vagy meglévő kiszolgáló.
   
    ![Új SQL Database-adatbázis – 7](./media/sql-database-get-started/sql-database-new-database-7.png)
10. Tekintse át az **Új kiszolgáló** panelt, hogy megismerje, milyen adatokat kell megadnia az új kiszolgálóhoz.
    
    ![Új SQL Database-adatbázis – 8](./media/sql-database-get-started/sql-database-new-database-8.png)
11. A **Kiszolgáló neve** szövegbeviteli mezőben adjon nevet az első kiszolgálójának – pl. „my-new-server-object” (új saját kiszolgálóobjektum). Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![Új SQL Database-adatbázis – 9](./media/sql-database-get-started/sql-database-new-database-9.png)
12. A **Kiszolgáló rendszergazdájának felhasználóneve** területen adjon meg egy felhasználónevet a kiszolgálóhoz tartozó rendszergazdai bejelentkezéshez, pl. „my-admin-account” (saját rendszergazdai fiók). Az ilyen bejelentkezést kiszolgálószintű bejelentkezésnek nevezik. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![Új SQL Database-adatbázis – 10](./media/sql-database-get-started/sql-database-new-database-10.png)
13. A **Jelszó** és a **Jelszó megerősítése** területen adjon meg egy jelszót a kiszolgálószintű bejelentkezési fiókhoz – pl."p@ssw0rd1".Zöld pipa jelzi, hogy érvényes jelszót adott meg.
    
    ![Új SQL Database-adatbázis – 11](./media/sql-database-get-started/sql-database-new-database-11.png)
14. A **Hely** területen válasszon a tartózkodási helyének megfelelő adatközpontot – pl. „Kelet-Ausztrália”.
    
    ![Új SQL Database-adatbázis – 12](./media/sql-database-get-started/sql-database-new-database-12.png)
15. Vegye figyelembe, hogy a **12-es verziójú kiszolgáló létrehozása (legújabb frissítés) területen csak az Azure SQL-kiszolgáló aktuális verziójának létrehozására van lehetősége.
    
    ![Új SQL Database-adatbázis – 13](./media/sql-database-get-started/sql-database-new-database-13.png)
16. Vegye figyelembe, hogy alapértelmezés szerint az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzet be van jelölve, és itt nem módosítható. Ez egy speciális beállítás. Ezt a beállítást a kiszolgálóobjektumhoz tartozó kiszolgálói tűzfal beállításaiban módosíthatja, bár ez a legtöbb esetben nem szükséges.
    
    ![Új SQL Database-adatbázis – 14](./media/sql-database-get-started/sql-database-new-database-14.png)
17. Az Új kiszolgáló panelen tekintse át választásait, majd kattintson a **Kiválasztás** elemre az új kiszolgáló kiválasztásához az új adatbázis számára.
    
    ![Új SQL Database-adatbázis – 15](./media/sql-database-get-started/sql-database-new-database-15.png)
18. Az SQL Database panel **Tarifacsomag** területén kattintson az**S2 Standard**, majd a **Basic** elemre a legkevésbé költséges tarifacsomag kiválasztásához az első adatbázisa számára. Később bármikor módosíthatja a tarifacsomagot.
    
    ![Új SQL Database-adatbázis – 16](./media/sql-database-get-started/sql-database-new-database-16.png)
19. Az SQL Database panelen tekintse át választásait, majd kattintson a **Létrehozás** elemre az első kiszolgálója és adatbázisa létrehozásához. Megtörténik a megadott értékek hitelesítése, és megkezdődik az üzembe helyezés.
    
    ![Új SQL Database-adatbázis – 17](./media/sql-database-get-started/sql-database-new-database-17.png)
20. A portál eszköztárán kattintson az **Értesítések** elemre az üzembe helyezés állapotának ellenőrzéséhez.
    
    ![Új SQL Database-adatbázis – 18](./media/sql-database-get-started/sql-database-new-database-18.png)

> [!IMPORTANT]
> Ha az üzembe helyezés befejeződött, a rendszer létrehozza az új Azure SQL-kiszolgálót és -adatbázist az Azure-ban. Addig nem csatlakozhat az új kiszolgálóhoz vagy adatbázishoz az SQL Server-eszközökkel, amíg létre nem hoz egy tűzfalszabályt a kiszolgálóhoz, amely megnyitja az SQL Database-tűzfalat az Azure-on kívülről származó kapcsolatok esetén.
> 
> 

[!INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Következő lépések
Miután áttanulmányozta az SQL Database oktatóanyagát, és létrehozott egy adatbázist mintaadatokkal, feltárhatja az adatokat a megszokott eszközökkel.

* Ha jártas a Transact-SQL és az SQL Server Management Studio (SSMS) használatában, olvassa el a [Connect and query a SQL database with SSMS](sql-database-connect-query-ssms.md) (SQL-adatbázishoz való kapcsolódás és lekérdezés az SSMS használatával) című cikket.
* Ha munkája során az Excelt használja, olvassa el a [Connect to a SQL database in Azure with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Azure-ban az Excellel) című cikket.
* Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez) című cikk alapján.
* Ha át szeretné helyezni helyszíni SQL Server-adatbázisait az Azure-ba, olvassa el a [Migrating a database to SQL Database](sql-database-cloud-migrate.md) (Adatbázis áttelepítése az SQL Database-be) című cikket.
* Ha a BCP parancssori eszköz használatával szeretne adatokat betölteni egy CSV-fájlból egy új táblába, olvassa el a [Loading data into SQL Database from a CSV file using BCP](sql-database-load-from-csv-with-bcp.md) (Adatok betöltése az SQL Database-be CSV-fájlból a BCP használatával) című cikket.
* Ha további információkat szeretne megtudni az Azure SQL Database biztonságával kapcsolatban, olvassa el a [Getting started with security](sql-database-get-started-security.md) (Ismerkedés az adatbázis-biztonsággal) című cikket.

## <a name="additional-resources"></a>További források
[Mi az SQL Database?](sql-database-technical-overview.md)




<!--HONumber=Nov16_HO2-->


