---
title: Az Excel csatlakoztatása egyetlen adatbázishoz
description: Megtudhatja, hogy miként csatlakoztathatja a Microsoft Excelt egyetlen adatbázishoz az Azure SQL-adatbázisban. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827135"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Az Excel csatlakoztatása egyetlen adatbázishoz az Azure SQL-adatbázisban, és jelentés létrehozása

Csatlakoztassa az Excelt egyetlen adatbázishoz az Azure SQL Database-ben, importálja az adatokat, és hozzon létre táblákat és diagramokat az adatbázis értékei alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

A kezdéshez egyetlen adatbázisra lesz szüksége. Ha nem rendelkezik ilyennel, olvassa el az [Egyetlen adatbázis létrehozása](sql-database-single-database-get-started.md) és a [Kiszolgálószintű IP-tűzfal létrehozása című témakört,](sql-database-server-level-firewall-rule.md) amelyből néhány perc alatt egyetlen adatbázis tapad le a mintaadatokkal.

Ebben a cikkben a cikkből importálhat mintaadatokat az Excelbe, de a saját adataival hasonló lépéseket követhet.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/) verziót vettük alapul.

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Az Excel csatlakoztatása SQL-adatbázishoz és adatok betöltése

1. Az Excel SQL-adatbázishoz való csatlakoztatásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. A lap tetején lévő menüsorban válassza az **Adatok** fület, válassza az **Adatok betöltése**lehetőséget, válassza az Azure-ból lehetőséget, majd az **Azure SQL-adatbázisból**lehetőséget. 

   ![Adatforrás kiválasztása: Excel csatlakoztatása SQL-adatbázishoz.](./media/sql-database-connect-excel/excel_data_source.png)

   Megnyílik az Adatkapcsolat varázsló.
3. A **Kapcsolódás adatbázis-kiszolgálóhoz** párbeszédpanelen írja be annak az SQL Database adatbázisnak a **Kiszolgálónevét**, amelyhez csatlakozni szeretne <*kiszolgálónév*>**. database.windows.net** formában. Például **msftestserver.database.windows.net**. Szükség esetén írja be az adatbázis nevét. A hitelesítő adatok ablakának megnyitásához válassza az **OK gombot.** 

   ![Csatlakozás az adatbázis-kiszolgálóhoz párbeszédpanel](media/sql-database-connect-excel/server-name.png)

4. Az **SQL Server Database** párbeszédpanelbal oldalán válassza az **Adatbázis** lehetőséget, majd írja be annak az SQL Database-kiszolgálónak a **felhasználónevét** és **jelszavát,** amelyhez csatlakozni szeretne. A **Navigátor**megnyitásához válassza a **Csatlakozás** lehetőséget. 

   ![Kiszolgálónév és hitelesítő adatok megadása](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy megszakad a kapcsolat, ha az SQL-adatbáziskiszolgáló nem engedélyezi az ügyfél IP-címről érkező forgalmat. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](sql-database-configure-firewall-settings.md) részben találja meg.

5. A **Navigátorban**jelölje ki a listából a dolgozni kívánt adatbázist, jelölje ki a dolgozni kívánt táblákat vagy nézeteket (a **vGetAllCategories lehetőséget**választottuk ), majd válassza a **Betöltés** lehetőséget az adatbázisadatainak excel-számolótáblába való áthelyezéséhez.

    ![Válasszon ki egy adatbázist és egy táblát.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása

Most, hogy létrehozta a kapcsolatot, számos különböző lehetősége van az adatok betöltésére. A következő lépések például az SQL-adatbázisban található adatok alapján hoznak létre egy kimutatásdiagramot. 

1. Kövesse az előző szakaszlépéseit, de ezúttal a **Betöltés**menü kiválasztása helyett válassza a **Betöltés a** legördülő menüben a **Betöltés** lehetőséget.
2. Ezután adja meg, hogyan szeretné megtekinteni ezeket az adatokat a munkafüzetben. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap**kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál. 

    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/sql-database-connect-excel/import-data.png)

    A munkalapon most egy üres kimutatástábla és -diagram van.
3. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.

    ![Konfigurálja az adatbázis-jelentést.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és munkalapokat szeretne csatlakoztatni az adatbázishoz, válassza az **Adatok** lapot, és a **Legutóbbi források** lehetőséget választva indítsa el a **Legutóbbi források** párbeszédpanelt. Itt válassza ki a listából létrehozott kapcsolatot, majd kattintson a **Megnyitás gombra.**
> ![Legutóbbi források párbeszédpanel](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Állandó kapcsolat létrehozása .odc fájl használatával

A kapcsolat részleteinek végleges mentéséhez hozzon létre egy .odc fájlt, és a **meglévő kapcsolatok** párbeszédpanelen kijelölhetővé tegye ezt a kapcsolatot. 

1. A lap tetején lévő menüsorban jelölje ki az **Adatok** lapot, majd a **Meglévő kapcsolatok** lehetőséget a **Meglévő kapcsolatok** párbeszédpanel elindításához. 
   1. Válassza **a Tallózás lehetőséget** az Adatforrás **kiválasztása** párbeszédpanel megnyitásához.   
   2. Jelölje ki a **+NewSqlServerConnection.odc** fájlt, majd a **Megnyitás** gombra az **Adatkapcsolat varázsló**megnyitásához.

      ![Új kapcsolat párbeszédpanel](media/sql-database-connect-excel/new-connection.png)

2. Az **Adatkapcsolat varázslóban**írja be a kiszolgáló nevét és az SQL Database hitelesítő adatait. Válassza a **Tovább lehetőséget.** 
   1. Válassza ki az adatokat tartalmazó adatbázist a legördülő menüből. 
   2. Válassza ki az önt érdeklő táblát vagy nézetet. A vGetAllCategories lehetőséget választottuk.
   3. Válassza a **Tovább lehetőséget.** 

      ![Adatkapcsolat varázsló](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Az Adatkapcsolat varázsló következő képernyőjén válassza ki a fájl helyét, a **Fájlnevet**és a **Rövid nevet.** Azt is választhatja, hogy a jelszót a fájlba menti, bár ez potenciálisan nem kívánt hozzáférést okozhat az adatoknak. Válassza a **Befejezés lehetőséget,** ha készen áll. 

    ![Adatkapcsolat mentése](media/sql-database-connect-excel/save-data-connection.png)

4. Adja meg, hogyan szeretné importálni az adatokat. Úgy döntöttünk, hogy csinál egy kimutatást. A kapcsolat tulajdonságait a Tulajdonságok lehetőséget választva is **módosíthatja.** Válassza **az OK gombot,** ha készen áll. Ha nem úgy döntött, hogy a jelszót a fájllal együtt menti, akkor a rendszer kéri a hitelesítő adatok megadását. 

    ![Adatok importálása](media/sql-database-connect-excel/import-data2.png)

5. Ellenőrizze, hogy az új kapcsolat mentve lett-e az **Adatok** lap kibontásával és a Meglévő kapcsolatok lehetőség **kiválasztásával.** 

    ![Meglévő kapcsolat](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>További lépések

* [Kapcsolódás az SQL Database adatbázishoz az SQL Server Management Studio használatával](sql-database-connect-query-ssms.md) speciális lekérdezés és elemzés céljából.
* Tudjon meg többet a [rugalmas készletek](sql-database-elastic-pool.md) előnyeiről.
* [A háttérben SQL Database adatbázishoz kapcsolódó webalkalmazás létrehozása](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
