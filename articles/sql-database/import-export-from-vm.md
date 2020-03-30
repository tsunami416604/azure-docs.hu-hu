---
title: SQL-adatbázis importálása vagy exportálása
description: Importáljon vagy exportáljon egy Azure SQL-adatbázist anélkül, hogy az Azure-szolgáltatások hozzáférhetne a kiszolgálóhoz.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 9f694f3f0ec740d0a4e8dc4e6bf8845c408802c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897844"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Azure SQL-adatbázis importálása vagy exportálása anélkül, hogy az Azure-szolgáltatások hozzáférhetnek a kiszolgálóhoz

Ez a cikk bemutatja, hogyan importálhat vagy exportálhat egy Azure SQL-adatbázist, ha az *Azure Services engedélyezése* ki van *állítva* az Azure SQL-kiszolgálón. A munkafolyamat egy Azure virtuális gép használatával futtatja az SqlPackage-t az importálási vagy exportálási művelet végrehajtásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-the-azure-virtual-machine"></a>Az Azure virtuális gép létrehozása

Hozzon létre egy Azure-beli virtuális gépet az **Azure-ba való üzembe helyezés** gomb kiválasztásával.

Ez a sablon lehetővé teszi egy egyszerű Windows virtuális gép telepítését néhány különböző lehetőség gel a Windows verzióhoz, a legújabb javított verzió használatával. Ez telepíti az A2-es méretű virtuális gép az erőforráscsoport helyét, és adja vissza a virtuális gép teljesen minősített tartománynevét.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

További információt a Windows virtuális gépek egyszerű telepítése című [témakörben](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)talál.


## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő lépések bemutatják, hogyan csatlakozhat a virtuális géphez távoli asztali kapcsolaton keresztül.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Kattintson a **Csatlakozás** gombra.

   A távoli asztali protokollfájl (.rdp fájl) űrlapja megjelenik a virtuális gép nyilvános IP-címével és portszámával.

   ![RDP képernyő](./media/import-export-from-vm/rdp.png)  

3. Válassza **az RDP-fájl letöltése lehetőséget.**

   > [!NOTE]
   > Az SSH használatával is csatlakozhat a virtuális géphez.

4. Zárja be a **Csatlakozás a virtuális géphez** űrlapot.
5. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz.
6. Amikor a rendszer kéri, válassza a **Csatlakozás lehetőséget.** Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

7. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd válassza az **OK gombot.**

8. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat folytatásához válassza az **Igen** vagy a **Folytatás** lehetőséget.



## <a name="install-sqlpackage"></a>SqlPackage telepítése

[Töltse le és telepítse az SqlPackage legújabb verzióját.](https://docs.microsoft.com/sql/tools/sqlpackage-download)




További információt az [SqlPackage.exe című fájlban talál.](https://docs.microsoft.com/sql/tools/sqlpackage)

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Tűzfalszabály létrehozása a virtuális gép adatbázishoz való hozzáférésének engedélyezéséhez

Adja hozzá a virtuális gép nyilvános IP-címét az SQL Database server tűzfalához.

A következő lépések hozzon létre egy kiszolgálószintű IP-tűzfal szabályt a virtuális gép nyilvános IP-címéhez, és lehetővé teszi a kapcsolatot a virtuális gépről.

1. Válassza az **SQL-adatbázisok lehetőséget** a bal oldali menüből, majd válassza ki az **adatbázist** az SQL adatbázisok lapon. Megnyílik az adatbázis áttekintő lapja, amely a teljesen minősített kiszolgálónevét (például **servername.database.windows.net)** jeleníti meg, és további konfigurációs lehetőségeket biztosít.

2. Másolja a teljesen minősített kiszolgálónevet a kiszolgálóhoz és annak adatbázisaihoz való csatlakozáshoz használva.

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png)

3. Az eszköztáron válassza a **Kiszolgálótűzfal beállítása** lehetőséget. Megnyílik az adatbázis-kiszolgáló **tűzfalbeállításai** lap.

   ![kiszolgálószintű IP-tűzfal szabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Válassza az **ügyfél IP hozzáadása** lehetőséget az eszköztáron, ha hozzá szeretné adni a virtuális gép nyilvános IP-címét egy új kiszolgálószintű IP-tűzfalszabályhoz. A kiszolgálószintű IP-tűzfalszabály egyetlen IP-címhez vagy IP-címtartományhoz is megnyithatja az 1433-as portot.

5. Kattintson a **Mentés** gombra. A rendszer kiszolgálószintű IP-tűzfalszabályt hoz létre a virtuális gép 1433-as nyilvános IP-címhez az SQL Database-kiszolgálón.

6. Zárja be a **Tűzfal beállításai** lapot.



## <a name="export-a-database-using-sqlpackage"></a>Adatbázis exportálása sqlpackage használatával

Sql-adatbázis exportálása az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal: [Paraméterek és tulajdonságok exportálása](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Az SqlPackage segédprogram az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és az SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióival, illetve az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)legújabb verziójával is elérhető.

Javasoljuk, hogy az SqlPackage segédprogram ot használja a méretezéshez és a teljesítményhez a legtöbb éles környezetben. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ez a példa bemutatja, hogyan exportálhat adatbázist az SqlPackage.exe használatával az Active Directory univerzális hitelesítéssel. Cserélje le a környezetére jellemző értékekre.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Adatbázis importálása az SqlPackage használatával

Ha SQL Server adatbázist szeretne importálni az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal, olvassa el az importálási paraméterek és tulajdonságok című [témakört.](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties) Az SqlPackage rendelkezik a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és SQL Server Data Tools [rendszerrel.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) Letöltheti az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download)legújabb verzióját is.

A méretezés és a teljesítmény érdekében azt javasoljuk, hogy az SqlPackage a legtöbb éles környezetben, nem pedig az Azure Portal használata. A fájlok áttelepítésével `BACPAC` kapcsolatos SQL Server Customer Advisory Team-blogról az SQL Server [rendszerről az Azure SQL Database-re bacpacfájlok használatával című](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)témakörben talál.

A következő SqlPackage parancs importálja az **AdventureWorks2017** adatbázist a helyi tárolóból egy Azure SQL Database-kiszolgálóra. Létrehoz egy új adatbázist **myMigratedDatabase** egy **prémium szolgáltatási** szint és egy **P6** szolgáltatási cél. Módosítsa ezeket az értékeket a környezetének megfelelően.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha egyetlen adatbázist egy vállalati tűzfal mögül kezel egy SQL Database-kiszolgálóhoz, a tűzfalnak meg kell nyitnia az 1433-as portot. Felügyelt példányhoz való csatlakozáshoz [pont-hely kapcsolattal](sql-database-managed-instance-configure-p2s.md) vagy gyorsútvonal-kapcsolattal kell rendelkeznie.

Ez a példa bemutatja, hogyan importálhat adatbázist az SqlPackage használatával az Active Directory univerzális hitelesítéssel.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az exportálási sebesség számos tényező (például adatforma) miatt változik, így lehetetlen megjósolni, hogy milyen sebességre kell számítani. Az SqlPackage jelentős időt vehet igénybe, különösen a nagy adatbázisok esetében.

A legjobb teljesítmény érdekében próbálkozzon a következő stratégiákkal:

1. Győződjön meg arról, hogy nincs más számítási feladat fut az adatbázisban. Hozzon létre egy másolatot exportálás előtt lehet a legjobb megoldás annak biztosítására, hogy más számítási feladatok nem futnak.
2. Növelje az adatbázis-szolgáltatási szint célkitűzését (SLO) az exportálási munkaterhelés jobb kezelése érdekében (elsősorban az I/O olvasása). Ha az adatbázis jelenleg GP_Gen5_4, talán egy üzleti legkritikusabb szint segítene az olvasási számítási feladatok.
3. Győződjön meg arról, hogy vannak fürtözött indexek, különösen a nagy táblák. 
4. A virtuális gépeknek (VM-eknek) ugyanabban a régióban kell lenniük, mint az adatbázisnak a hálózati korlátok elkerülése érdekében.
5. Virtuális gépek kell ssd megfelelő méretű ideiglenes összetevők létrehozása előtt blob storage.Vm kell SSD megfelelő méretű ideiglenes összetevők létrehozása előtt blob storage.
6. A virtuális gépeknek megfelelő mag- és memóriakonfigurációval kell rendelkezniük az adott adatbázishoz.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Tárolja az importált vagy exportált . BACPAC-fájl

A. A BACPAC-fájl tárolható az [Azure Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)vagy az [Azure Files alkalmazásban.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 

A legjobb teljesítmény elérése érdekében használja az Azure Files. Az SqlPackage a fájlrendszerrel működik, így közvetlenül hozzáférhet az Azure Files szolgáltatáshoz.

A költségek csökkentése érdekében használja az Azure Blobs, amely olcsóbb, mint egy prémium szintű Azure-fájlmegosztás. Ehhez azonban át kell másolnia a [. BACPAC-fájl](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) a blob és a helyi fájlrendszer között az importálási vagy exportálási művelet előtt. Ennek eredményeképpen a folyamat hosszabb időt vesz igénybe.

A feltöltéshez vagy a letöltéshez. BACPAC-fájlok, lásd: [Adatok átvitele az AzCopy és Blob tárolóval,](../storage/common/storage-use-azcopy-blobs.md)valamint [Adatok átvitele az AzCopy segítségével és a fájltárolással.](../storage/common/storage-use-azcopy-files.md)

A környezettől függően szükség lehet [az Azure Storage tűzfalainak és virtuális hálózatainak konfigurálására.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>További lépések

- Az importált SQL-adatbázishoz való csatlakozásról és az adatok lekérdezéséről a [Rövid útmutató: Azure SQL Database: Az SQL Server Management Studio használata az adatok csatlakoztatásához és lekérdezéséhez](sql-database-connect-query-ssms.md)című témakörben olvashat.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server-adatbázis-áttelepítési folyamatról, beleértve a teljesítményjavaslatokat is, az [SQL Server adatbázis-áttelepítése az Azure SQL Database rendszerbe](sql-database-single-database-migrate.md)című témakörben található.
- A tárolási kulcsok és a megosztott hozzáférési aláírások biztonságos kezeléséről és megosztásáról az [Azure Storage biztonsági útmutatójában olvashat.](https://docs.microsoft.com/azure/storage/common/storage-security-guide)
