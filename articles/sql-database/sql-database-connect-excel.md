---
title: "Excel csatlakoztatása SQL Database adatbázishoz| Microsoft Docs"
description: "Útmutató a Microsoft Excel Azure SQL adatbázishoz való csatlakoztatásához a felhőben. Adatok importálása Excelbe jelentésekhez és adatok áttekintéséhez."
services: sql-database
keywords: "excel csatlakoztatása sql-hez, adatok importálása excelbe"
documentationcenter: 
author: joseidz
manager: jhubbard
editor: 
ms.assetid: 906924bc-2707-48d3-bac6-397976a0409d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/05/2016
ms.author: joseidz
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3a0f9501760ba3ea97341d30cd706c971c826430


---
# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>SQL Database oktatóanyag: Excel csatlakoztatása Azure SQL adatbázishoz és jelentés létrehozása
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

Tanulja meg, hogyan csatlakoztathatja az Excelt SQL adatbázishoz a felhőben, és hogyan importálhat adatokat, hozhat létre táblázatokat és diagramokat az adatbázisban levő értékek alapján. Ebben az oktatóanyagban csatlakoztatjuk az Excelt az adatbázistáblához, elmentjük az adatokat tároló fájlt és az Excelre vonatkozó kapcsolatadatokat, és kimutatásdiagramot hozunk létre az adatbázis értékeiből.

Ezekhez a műveletekhez szükség van egy Azure SQL-adatbázisra. Ha nincs ilyen, az [Első SQL-adatbázis létrehozása](sql-database-get-started.md) alapján hozzon létre egy adatbázist mintaadatokkal, majd futtassa - ez csupán néhány percet vesz igénybe. Ebben a cikkben az említett cikkből importálunk mintaadatokat az Excelbe, de saját adatokkal is végrehajthatja a leírt lépéseket.

Az Excelnek is telepítve kell lennie. Ebben a cikkben a [Microsoft Excel 2016](https://products.office.com/en-US/) verziót vettük alapul.

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Excel csatlakoztatása SQL-adatbázishoz és ODC-fájl létrehozása
1. Az Excel SQL-adatbázishoz való csatlakoztatásához nyissa meg az Excelt, majd hozzon létre egy új munkafüzetet, vagy nyisson meg egy meglévő Excel-munkafüzetet.
2. Az oldal tetején található menüsávon kattintson az **Adatok**, majd az**Egyéb forrásokból származó**, majd az**SQL szerverről származó** lehetőségre.
   
   ![Adatforrás kiválasztása: Excel csatlakoztatása SQL-adatbázishoz.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Megnyílik az Adatkapcsolat varázsló.
3. A **Kapcsolódás adatbázis-kiszolgálóhoz** párbeszédpanelen írja be annak az SQL Database adatbázisnak a **Kiszolgálónevét**, amelyhez csatlakozni szeretne <*kiszolgálónév*>**. database.windows.net** formában. Például: **adworkserver.database.windows.net**.
4. A **Hitelesítő adatok** alatt kattintson a **Következő felhasználónév és jelszó használata** lehetőségre, adja meg azt a **Felhasználónevet** és **Jelszót**, amelyet az SQL Database-kiszolgáló létrehozásakor beállított, majd kattintson a **Tovább** gombra.
   
   ![Kiszolgálónév és hitelesítő adatok megadása](./media/sql-database-connect-excel/connect-to-server.png)
   
   > [!TIP]
   > A hálózati környezettől függően előfordulhat, hogy nem tud csatlakozni, vagy megszakad a kapcsolat, ha az SQL Database-kiszolgáló nem engedélyezi az ügyfél IP-címről érkező forgalmat. Lépjen az [Azure Portalhoz](https://portal.azure.com/), kattintson az SQL Server-példányok lehetőségre, majd a saját kiszolgálójára, ezután a beállítások alatt a tűzfalra, és adja hozzá ügyfél IP-címét. A részleteket a [Tűzfal beállításainak konfigurálása](sql-database-configure-firewall-settings.md) részben találja meg.
   > 
   > 
5. Az **Adatbázis és tábla kijelölése** párbeszédpanelen válassza a listából azt az adatbázist, amellyel dolgozni szeretne, majd kattintson azokra a táblákra vagy nézetekre, amelyekkel dolgozni szeretne (ebben a példában a **vGetAllCategories**), majd kattintson a **Tovább** gombra.
   
    ![Válasszon ki egy adatbázist és egy táblát.](./media/sql-database-connect-excel/select-database-and-table.png)
   
    Megnyílik az **Adatkapcsolatfájl mentése és befejezése** párbeszédpanel, ahol meg kell adnia az Excel által alkalmazott Office adatbázis-kapcsolat fájlra (*.odc) vonatkozó információkat. Meghagyhatja az alapértelmezett beállításokat, vagy testre szabhatja a beállításokat.
6. Meghagyhatja az alapértelmezett beállításokat, de jegyezze meg a **Fájlnevet**. A **Leírás**, a **Rövid név** és a **Kulcsszavak** segítenek Önnek és a többi felhasználónak abban, hogy mihez csatlakozik és a kapcsolat megtalálásában. Kattintson az **Adatfrissítéshez kísérelje meg mindig ezt a fájlt használni ** lehetőségre, ha a kapcsolatadatokat menteni szeretné az ODC-fájlba, hogy az adatok a kapcsolódáskor frissüljenek, majd kattintson a **Befejezés** gombra.
   
    ![ODC-fájl mentése](./media/sql-database-connect-excel/save-odc-file.png)
   
    Megjelenik az **Adatimportálás** párbeszédpanel.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Adatok importálása Excelbe és kimutatásdiagram létrehozása
Most, hogy létrehozta a kapcsolatot és az adatokat, illetve kapcsolatadatokat tartalmazó fájlt, az adatok importálása következik.

1. Az **Adatok importálása** párbeszédpanelen kattintson az adatok munkalapon történő megjelenítéséhez használni kívánt beállításra, majd kattintson az **OK** gombra. Válassza a **Kimutatásdiagram** lehetőséget. Az **Új munkalap**kiválasztásával új munkalapot is létrehozhat, vagy választhatja az **Adatok hozzáadása adatmodellhez** beállítást is. Az adatmodellekről további információkat az [Adatmodell létrehozása Excelben](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B) részben talál. Kattintson a **Tulajdonságok** lehetőségre az előző lépésben létrehozott ODC-fájllal kapcsolatos információkért és az adatfrissítés beállításainak kiválasztásáért.
   
    ![Az Excelben szereplő adatok formátumának kiválasztása](./media/sql-database-connect-excel/import-data.png)
   
    A munkalapon most egy üres kimutatástábla és -diagram van.
2. A **Kimutatástábla mezői** alatt jelölje ki az összes megtekinteni kívánt mező jelölőnégyzetét.
   
    ![Konfigurálja az adatbázis-jelentést.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Ha más Excel-munkafüzeteket és munkalapokat szeretne csatlakoztatni az adatbázishoz, kattintson az **Adatok**, majd a **Kapcsolatok** és a **Hozzáadás** gombra, válassza a listából a létrehozott kapcsolatot, és kattintson **Megnyitás** gombra.
> ![Kapcsolat megnyitása másik munkafüzetből](./media/sql-database-connect-excel/open-from-another-workbook.png)
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Kapcsolódás az SQL Database adatbázishoz az SQL Server Management Studio használatával](sql-database-connect-query-ssms.md) speciális lekérdezés és elemzés céljából.
* Tudjon meg többet a [rugalmas készletek](sql-database-elastic-pool.md) előnyeiről.
* [A háttérben SQL Database adatbázishoz kapcsolódó webalkalmazás létrehozása](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).




<!--HONumber=Nov16_HO2-->


