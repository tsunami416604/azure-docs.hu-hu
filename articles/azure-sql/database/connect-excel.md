---
title: Kapcsolódás Excellel
description: Ismerje meg, hogyan csatlakoztatható a Microsoft Excel egy adatbázishoz a Azure SQL Database-ben. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: 332620699e30facb2fbbfee90e3328b8dcf1e179
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195497"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-and-create-a-report"></a>Az Excel összekötése egy Azure SQL Database adatbázissal és jelentés létrehozása

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Excelt összekapcsolhatja egy Azure SQL Database adatbázisával, majd importálhatja az adatok importálását, és táblákat és diagramokat hozhat létre az adatbázisban lévő értékek alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

Az első lépések előtt létre kell hoznia egy adatbázist a SQL Databaseban. Ha még nem rendelkezik ilyennel, tekintse meg az [adatbázis létrehozása a Azure SQL Database-ben](single-database-create-quickstart.md) és a [kiszolgálói szintű IP-tűzfal létrehozása](firewall-create-server-level-portal-quickstart.md) című témakört, amely néhány percen belül beolvashatja az adatbázist a mintaadatok használatával.

Ebben a cikkben a mintaadatok importálása az Excel programba című cikkből származnak, de a saját adataival hasonló lépéseket is követheti.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/) verziót vettük alapul.

## <a name="connect-excel-and-load-data"></a>Az Excel és az adatterhelés összekötése

1. Az Excel SQL Database-adatbázishoz való összekapcsolásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. A lap tetején lévő menüsorban válassza az **adatok** fület, válassza az **adatok lekérése**lehetőséget, válassza ki az Azure-ból lehetőséget, majd válasszon **a következőből: Azure SQL Database**.

   ![Adatforrás kiválasztása: az Excel csatolása SQL Databasehoz.](./media/connect-excel/excel_data_source.png)

3. A **SQL Server adatbázis** párbeszédpanelen írja be a **kiszolgáló nevét** , amelyhez csatlakozni szeretne a következő formában: <*servername* > **. database.Windows.net**. Például: **msftestserver.database.Windows.net**. Igény szerint megadhatja az adatbázis nevét. A hitelesítő adatok ablak megnyitásához kattintson **az OK gombra** .

   ![Kapcsolódás az adatbázis-kiszolgálóhoz párbeszédpanel](./media/connect-excel/server-name.png)

4. A **SQL Server adatbázis** párbeszédpanelen válassza a bal oldalon **található adatbázis** lehetőséget, majd írja be annak a kiszolgálónak a **felhasználónevét** és **jelszavát** , amelyhez csatlakozni szeretne. A **navigátor**megnyitásához válassza a **Kapcsolódás** lehetőséget.

   ![Kiszolgálónév és hitelesítő adatok megadása](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy elveszti a kapcsolatot, ha a kiszolgáló nem engedélyezi a forgalmat az ügyfél IP-címéről. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](firewall-configure.md) részben találja meg.

5. **A**kezelőben válassza ki a listából a használni kívánt adatbázist, válassza ki a használni kívánt táblákat vagy nézeteket ( **vGetAllCategories**), majd válassza a Load ( **Betöltés** ) lehetőséget az adatok adatbázisból az Excel-számolótáblába való áthelyezéséhez.

    ![Válasszon ki egy adatbázist és egy táblát.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása

Most, hogy létrehozta a kapcsolódást, számos különböző lehetőség áll rendelkezésére az adattöltéshez. Az alábbi lépések például egy kimutatást hoznak létre az adatbázisban található, SQL Databaseban található adatai alapján.

1. Kövesse az előző szakaszban leírt lépéseket, de ezúttal, a **Betöltés helyett**válassza a **Betöltés** lehetőséget a **Betöltés legördülő listából** .
2. Ezután válassza ki, hogyan szeretné megtekinteni ezeket az adatfájlokat a munkafüzetben. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap**kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál.

    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/connect-excel/import-data.png)

    A munkalapon most egy üres kimutatástábla és -diagram van.
3. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.

    ![Konfigurálja az adatbázis-jelentést.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és-munkalapokat szeretne összekötni az adatbázissal, **válassza az** adatlapot, és válassza a **legutóbbi** források lehetőséget a **legutóbbi források** párbeszédpanel elindításához. Válassza ki a listából a létrehozott kapcsolatokat, majd kattintson a **Megnyitás**gombra.
> ![Legutóbbi források párbeszédpanel](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Állandó kapcsolatok létrehozása. odc-fájl használatával

Ha véglegesen szeretné menteni a kapcsolat részleteit, létrehozhat egy. odc-fájlt, és ezt a kapcsolatot kiválasztható lehetőséggel teheti meg a **meglévő kapcsolatok** párbeszédpanelen.

1. A lap tetején lévő menüsorban válassza az **adatlapot, majd válassza a** **meglévő kapcsolatok** lehetőséget a **létező kapcsolatok** párbeszédpanel elindításához.
   1. Az **adatforrás kiválasztása** párbeszédpanel megnyitásához válassza a **Tallózás** lehetőséget.
   2. Válassza ki a **+ NewSqlServerConnection. odc** fájlt, majd kattintson a **Megnyitás** elemre az **adatelérési varázsló**megnyitásához.

      ![Új kapcsolatok párbeszédpanel](./media/connect-excel/new-connection.png)

2. Az **adatkapcsolatok varázslóban**adja meg a kiszolgáló nevét és a SQL Database hitelesítő adatait. Kattintson a **Tovább** gombra.
   1. Válassza ki azt az adatbázist, amely az adatait tartalmazza a legördülő listából.
   2. Válassza ki azt a táblát vagy nézetet, amelyre kíváncsi. VGetAllCategories választottunk.
   3. Kattintson a **Tovább** gombra.

      ![Adatkapcsolatok varázsló](./media/connect-excel/data-connection-wizard.png)

3. Válassza ki a fájl helyét, a **fájlnevet**és a **felhasználóbarát nevet** az adatelérési varázsló következő képernyőjén. Azt is megteheti, hogy elmenti a jelszót a fájlban, de ez potenciálisan elérhetővé teheti az adatait a nemkívánatos hozzáférés érdekében. Ha elkészült, válassza a **Befejezés** lehetőséget.

    ![Adatkapcsolatok mentése](./media/connect-excel/save-data-connection.png)

4. Válassza ki, hogyan szeretné importálni az adatait. Úgy döntöttünk, hogy kimutatást teszünk. A kapcsolatok tulajdonságait a **Tulajdonságok**lehetőség kiválasztásával is módosíthatja. Ha elkészült, kattintson **az OK gombra** . Ha nem választotta a jelszó mentését a fájllal, a rendszer kérni fogja a hitelesítő adatok megadását.

    ![Adatok importálása](./media/connect-excel/import-data2.png)

5. Ellenőrizze, **hogy az új** kapcsolat mentve lett-e, és válassza ki az adatlapot, majd a **meglévő kapcsolatok**elemet.

    ![Létező kapcsolatok](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>További lépések

* [Kapcsolódás az SQL Database adatbázishoz az SQL Server Management Studio használatával](connect-query-ssms.md) speciális lekérdezés és elemzés céljából.
* Tudjon meg többet a [rugalmas készletek](elastic-pool-overview.md) előnyeiről.
* [A háttérben SQL Database adatbázishoz kapcsolódó webalkalmazás létrehozása](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
