<properties
   pageTitle="SQL Data Warehouse létrehozása a TSQL használatával | Microsoft Azure"
   description="Tudnivalók Azure SQL Data Warehouse a TSQL használatával történő létrehozásáról"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# SQL Data Warehouse-adatbázis létrehozása a Transact-SQL (TSQL) használatával

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Ebből a cikkből megtudhatja, hogyan hozhat létre egy SQL Data Warehouse-adatbázist a T-SQL használatával.

## Előfeltételek

A kezdéshez a következők szükségesek: 

- **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][].
- **Azure SQL-kiszolgáló**: További részletes információ: [Create an Azure SQL Database logical server with the Azure Portal][] vagy [Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával][].
- **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, mint az Azure SQL-kiszolgáló, vagy tekintse át az [erőforráscsoportok létrehozásával foglalkozó][] cikket.
- **T-SQL-végrehajtási környezet**: a T-SQL-ek végrehajtásához [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][] vagy [SSMS][] alkalmazáskörnyezetek használhatók.

> [AZURE.NOTE] A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  A díjszabással kapcsolatos további információkért lásd: [SQL Data Warehouse díjszabása][].

## Adatbázis létrehozása a Visual Studióval

Ha új Visual Studio-felhasználó, tekintse meg ezt a cikket: [Az Azure SQL Data Warehouse lekérdezése (Visual Studio)][].  A kezdéshez nyissa meg az SQL Server Object Explorert a Visual Studióban, és csatlakozzon az SQL Data Warehouse-adatbázisnak helyet adó kiszolgálóra.  A csatlakozás után úgy hozhatja létre az SQL Data Warehouse-adatbázist, hogy a következő SQL parancsot futtatja a **fő** adatbázison.  A parancs létrehozza a MySqlDwDb adatbázist DW400 szolgáltatási céllal, és lehetővé teszi az adatbázisnak, hogy legfeljebb 10 TB nagyságúra nőjön.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Adatbázis létrehozása sqlcmd használatával

Az sqlcmd segédprogrammal is futtathatja ugyanezt a parancsot. Ehhez a következőket kell beírnia a parancssorba.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Ha nincs megadva, az alapértelmezett rendezés: COLLATE SQL_Latin1_General_CP1_CI_AS.  A `MAXSIZE` értéke 250 GB és 240 TB között lehet.  A `SERVICE_OBJECTIVE` értéke DW100 és DW2000 [DWU][] között lehet.  Az összes érvényes értéket megtekintheti az MSDN-dokumentáció [CREATE DATABASE][] (ADATBÁZIS LÉTREHOZÁSA) című részében.  A MAXSIZE és a SERVICE_OBJECTIVE paraméter is módosítható az [ALTER DATABASE][] T-SQL paranccsal.  Az adott adatbázis rendezése annak létrehozását követően nem módosítható.   A SERVICE_OBJECTIVE paraméter módosításakor elővigyázatosnak kell lennie, mivel a DWU módosításakor újraindulnak a szolgáltatások, és megszakadnak a folyamatban lévő lekérdezések.  A MAXSIZE módosítása nem indítja újra a szolgáltatásokat, mivel csupán egyszerű metaadat-műveletről van szó.

## Következő lépések

Miután az SQL Data Warehouse befejezte a kiépítést, [mintaadatokat tölthet be][], vagy további részleteket tudhat meg a [fejlesztés][], [betöltés][] vagy [áttelepítés][] mikéntjéről.

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[SQL Data Warehouse létrehozása az Azure Portalról]: sql-data-warehouse-get-started-provision.md
[Az Azure SQL Data Warehouse lekérdezése (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[áttelepítés]: sql-data-warehouse-overview-migrate.md
[fejlesztés]: sql-data-warehouse-overview-develop.md
[betöltés]: sql-data-warehouse-overview-load.md
[mintaadatokat tölthet be]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok létrehozásával foglalkozó]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-fiók létrehozása]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Havi Azure-kredit a Visual Studio-előfizetőknek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=sep16_HO1-->


