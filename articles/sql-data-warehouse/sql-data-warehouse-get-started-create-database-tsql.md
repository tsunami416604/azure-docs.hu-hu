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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# SQL Data Warehouse-adatbázis létrehozása a Transact-SQL (TSQL) használatával

> [AZURE.SELECTOR]
- [Azure portálra](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Ebből a cikkből megtudhatja, hogyan hozhat létre egy SQL Data Warehouse-adatbázis a Transact-SQL (T-SQL) használatával.

## Előfeltételek
Mielőtt elkezdi a műveletet, ellenőrizze az alábbi előfeltételek meglétét.

- **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][].
- **V12 Azure SQL Server**: Lásd: [Create an Azure SQL Database logical server with the Azure Portal][] (Azure SQL Database logikai kiszolgáló létrehozása az Azure portál használatával) vagy [Create an Azure SQL Database logical server with PowerShell][] (Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával).
- **Erőforráscsoport neve**: Használja ugyanazt az erőforráscsoportot, mint a V12 Azure SQL Server, vagy tekintse át az [erőforráscsoportok][] részt egy új erőforráscsoport létrehozásához.
- **Visual Studio SQL Server Data Tools összetevővel**: A telepítés menetét az [Installing Visual Studio and SSDT][] (A Visual Studio és az SSDT telepítése) című rész írja le.

> [AZURE.NOTE] Az új SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  A díjszabással kapcsolatos további információkért lásd: [SQL Data Warehouse díjszabása][].

## Adatbázis létrehozása a Visual Studióval

Ha új Visual Studio-felhasználó, tekintse meg ezt a cikket: [Connect to SQL Data Warehouse with Visual Studio][] (Csatlakozás a SQL Data Warehouse-hoz a Visual Studio használatával).  A kezdéshez nyissa meg az SQL Server Object Explorert a Visual Studióban, és csatlakozzon az SQL Data Warehouse-adatbázisnak helyet adó kiszolgálóra.  A csatlakozás után úgy hozhatja létre az SQL Data Warehouse-adatbázist, hogy a következő SQL parancsot futtatja a **fő** adatbázison.  A parancs létrehozza a MySqlDwDb adatbázist DW400 szolgáltatási céllal, és lehetővé teszi az adatbázisnak, hogy legfeljebb 10 TB nagyságúra nőjön.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Adatbázis létrehozása sqlcmd használatával

Az sqlcmd segédprogrammal is futtathatja ugyanezt a parancsot, ehhez a következőket kell beírnia a parancssorba.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

A **MAXSIZE** és a **SERVICE_OBJECTIVE** paraméterek az adatbázis maximális méretét határozzák meg a lemezen, illetve hogy az Adatraktár-példányhoz mennyi erőforrás lesz hozzárendelve.  A szolgáltatási cél alapvetően a processzor- és memóriakapacitás hozzárendelését jelenti, ami egyenes arányban nő a DWU méretével.  

A MAXSIZE értéke 250 GB és 240 TB köz eshet.  A szolgáltatási cél DW100 és DW2000 közé eshet.  A MAXSIZE és a SERVICE_OBJECTIVE paraméterek összes érvényes értékének megtekintéséhez válassza az MSDN-dokumentáció [CREATE DATABASE][] (ADATBÁZIS LÉTREHOZÁSA) című részét.  A MAXSIZE és a SERVICE_OBJECTIVE paraméter is módosítható az [ALTER DATABASE][] T-SQL paranccsal.  A SERVICE_OBJECTIVE paraméter módosításakor elővigyázatosnak kell lennie, mivel ekkor újraindulnak a szolgáltatások, és megszakadnak a folyamatban lévő lekérdezések.  A MAXSIZE módosítása nem indítja újra a szolgáltatásokat, mivel csupán egyszerű metaadat-műveletről van szó.

## További lépések
Miután az SQL Data Warehouse befejezte a kiépítést, [mintaadatokat tölthet be][], vagy további részleteket tudhat meg a [fejlesztés][], [betöltés][] vagy [áttelepítés][] mikéntjéről.

<!--Article references-->
[SQL Data Warehouse létrehozása az Azure portálról]: ./sql-data-warehouse-get-started-provision.md
[Connect to SQL Data Warehouse with Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[áttelepítés]: ./sql-data-warehouse-overview-migrate.md
[fejlesztés]: ./sql-data-warehouse-overview-develop.md
[betöltés]: ./sql-data-warehouse-overview-load.md
[mintaadatokat tölthet be]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell (Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával)]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok]: ../azure-portal/resource-group-portal.md
[Installing Visual Studio and SSDT]: ./sql-data-warehouse-install-visual-studio.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-fiók létrehozása]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Havi Azure-kredit a Visual Studio-előfizetőknek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


