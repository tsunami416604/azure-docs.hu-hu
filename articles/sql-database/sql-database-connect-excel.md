---
title: Excel csatlakoztatása SQL Database adatbázishoz| Microsoft Docs
description: Útmutató a Microsoft Excel Azure SQL adatbázishoz való csatlakoztatásához a felhőben. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez.
services: sql-database
keywords: excel csatlakoztatása sql-hez, adatok importálása excelbe
author: joseidz
manager: jhubbard
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 03/10/2017
ms.author: craigg
ms.openlocfilehash: 5d2d863c0266467a60eafa70f0ef3ecaa9736ac0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Excel csatlakoztatása Azure SQL-adatbázis és a jelentés létrehozása

Excel csatlakoztatása SQL-adatbázis a felhőben, és adatokat importálhat, és létre táblázatokat és diagramokat az adatbázisban levő értékek alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

Ezekhez a műveletekhez szükség van egy Azure SQL-adatbázisra. Ha nincs ilyen, az [Első SQL-adatbázis létrehozása](sql-database-get-started-portal.md) alapján hozzon létre egy adatbázist mintaadatokkal, majd futtassa - ez csupán néhány percet vesz igénybe. Ebben a cikkben lesz importál mintaadatokat az Excelbe, ha a cikkben, de a lépésekkel hasonló a saját adataival.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/) verziót vettük alapul.

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Excel csatlakoztatása egy SQL database és az adatok betöltése
1. Az Excel SQL-adatbázishoz való csatlakoztatásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. A lap felső menüsávban válassza ki a **adatok** lapon jelölje be **adatok beolvasása**, az Azure, majd válassza ki és **az Azure SQL Database**. 
   
   ![Adatforrás kiválasztása: Excel csatlakoztatása SQL-adatbázishoz.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Megnyílik az Adatkapcsolat varázsló.
3. A **Kapcsolódás adatbázis-kiszolgálóhoz** párbeszédpanelen írja be annak az SQL Database adatbázisnak a **Kiszolgálónevét**, amelyhez csatlakozni szeretne <*kiszolgálónév*>**. database.windows.net** formában. Például **msftestserver.database.windows.net**. Szükség esetén adja meg az adatbázis nevében. Válassza ki **OK** a hitelesítő adatok ablak megnyitásához. 

   ![kiszolgáló-name.png](media/sql-database-connect-excel/server-name.png)

1. Az a **SQL Server-adatbázis** párbeszédpanelen jelölje ki **adatbázis** a bal oldali oldalán található, és adja meg a **felhasználónév** és **jelszó** a a SQL adatbázis-kiszolgáló, amelyhez csatlakozni kíván. Válassza ki **Connect** megnyitásához a **Navigator**. 

  ![Kiszolgálónév és hitelesítő adatok megadása](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy megszakad a kapcsolat, ha az SQL Database-kiszolgáló nem engedélyezi az ügyfél IP-címről érkező forgalmat. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](sql-database-configure-firewall-settings.md) részben találja meg.
   
   
5. Az a **Navigator**, jelölje be a listában a használni kívánt adatbázis válassza ki a táblák vagy nézetek dolgozni szeretne (választottuk **vGetAllCategories**), majd válassza ki **terhelés**a tárolt adatok mozgatása az SQL Azure adatbázis az excel-táblázat.
   
    ![Válasszon ki egy adatbázist és egy táblát.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása
Most, hogy létrehozta a kapcsolatot, lehetősége van több különböző, hogyan az adatok betöltésére. Például az alábbi lépéseket az SQL-adatbázis található adatok alapján kimutatásdiagram létrehozása. 

1. Kövesse az előző szakaszban, de ezúttal kiválasztása helyett a **betöltése**, jelölje be **való betöltés** a a **terhelés** legördülő listán.
2. Válassza ki, hogyan kívánja az adat a munkafüzetben lévő megtekintéséhez. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap**kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál. 
   
    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/sql-database-connect-excel/import-data.png)
   
    A munkalapon most egy üres kimutatástábla és -diagram van.
2. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.
   
    ![Konfigurálja az adatbázis-jelentést.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és munkalapokat csatlakozni az adatbázishoz, jelölje be a **adatok** lapot, és jelölje be **források** elindíthatja a **források** párbeszédpanel megnyitásához. Ott, válassza ki a listából a létrehozott kapcsolatot, és kattintson a **nyitott**.
> ![Legutóbbi kapcsolatok](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Állandó kapcsolatot .odc fájl használata
A kapcsolat adatai véglegesen mentéséhez .odc fájl létrehozása és a kapcsolat egy egyik lehetősége a **meglévő kapcsolatok** párbeszédpanel megnyitásához. 

1. A lap felső menüsávban válassza ki a **adatok** lapra, majd válassza ki **meglévő kapcsolatok** elindíthatja a **meglévő kapcsolatok** párbeszédpanel. 
    1. Válassza ki **további Tallózás** megnyitásához a **adatforrás kiválasztása** párbeszédpanel megnyitásához.   
    2. Válassza ki a **+NewSqlServerConnection.odc** fájlt, és válassza ki **nyissa meg a** megnyitásához a **Adatkapcsolat varázsló**.

    ![Új kapcsolat](media/sql-database-connect-excel/new-connection.png)

2. Az a **Adatkapcsolat varázsló**, írja be a kiszolgáló nevét és az SQL-adatbázis hitelesítő adatait. Kattintson a **Tovább** gombra. 
    1. Válassza ki a legördülő származó adatokat tartalmazó adatbázis. 
    2. Válassza ki a tábla vagy nézet kíváncsiak vagyunk. VGetAllCategories választottuk.
    3. Kattintson a **Tovább** gombra. 

    ![Adatkapcsolat varázsló](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Válassza ki a fájl helyét a **Fájlnév**, és a **rövid név** az Adatkapcsolat varázsló a következő képernyőn. Is beállíthatja a jelszó mentése a fájlban, bár ez potenciálisan felfedheti az adatok nem kívánt eléréséhez. Válassza ki **Befejezés** Ha készen áll. 

    ![Adatkapcsolat mentése](media/sql-database-connect-excel/save-data-connection.png)

4. Válassza ki, hogy az adatok importálásához. A kimutatás teendő választottuk. Jelöljön ki is módosíthatja a kapcsolat tulajdonságait **tulajdonságok**. Válassza ki **OK** Ha készen áll. Ha nem választotta a jelszó mentése a fájlhoz, majd kérni fogja a hitelesítő adatok megadását. 

    ![Adatok importálása](media/sql-database-connect-excel/import-data2.png)

5. Győződjön meg arról, hogy mentette-e az új kapcsolat kibontásával a **adatok** lapra, majd válassza **meglévő kapcsolatok**. 

    ![Meglévő kapcsolat](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>További lépések
* [Kapcsolódás az SQL Database adatbázishoz az SQL Server Management Studio használatával](sql-database-connect-query-ssms.md) speciális lekérdezés és elemzés céljából.
* Tudjon meg többet a [rugalmas készletek](sql-database-elastic-pool.md) előnyeiről.
* [A háttérben SQL Database adatbázishoz kapcsolódó webalkalmazás létrehozása](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

