<properties
    pageTitle="Az SQL Server használata az Azure Virtual Machines szolgáltatásban – áttekintés | Microsoft Azure"
    description="Ismerje meg, hogyan futtathat teljes SQL Server-kiadásokat Azure virtuális gépeken. Közvetlen hivatkozásokat talál az SQL Serverhez készült összes virtuálisgép-rendszerképhez és a kapcsolódó tartalmakhoz."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="jroth"/>


# Az SQL Server használata az Azure Virtual Machines szolgáltatásban – áttekintés

Ez a témakör az SQL Server Azure virtuális gépeken történő futtatásának lehetőségeit írja le, [hivatkozásokat tartalmaz a portálon elérhető rendszerképekhez](#option-1-deploy-a-sql-vm-per-minute-licensing), valamint tartalmazza a [gyakori feladatok](#manage-your-sql-vm) áttekintését.

>[AZURE.NOTE] Ha már ismeri az SQL Servert, és csak azt szeretné megtudni, hogyan helyezhet üzembe egy SQL Servert futtató virtuális gépet, tekintse meg az [SQL Server rendszerű virtuális gép létrehozása az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md) című cikket.

## Áttekintés
Tegyük fel, hogy Ön egy adatbázis-rendszergazda, aki szeretné áthelyezni a helyszíni SQL Server számítási feladatait a felhőbe. Esetleg egy fejlesztő, aki az SQL Server relációsadatbázis-kezelési képességeit kívánja felhasználni az Azure-alkalmazásában. Milyen előnyökkel jár az SQL Server számítási feladatok Azure virtuális gépeken való futtatása? A következő áttekintő videó felvázolja az előnyöket, és műszaki áttekintést nyújt.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Az előnyök értékelése

Mielőtt belekezdene, először mérje fel, milyen előnyökkel jár az SQL Server Azure virtuális gépeken való használata.

Ha egyéb számítási feladatokat, például egy vállalati alkalmazást telepít át az Azure-ba, a jobb teljesítmény érdekében érdemes minden kapcsolódó SQL Server-adatbázist is áttelepíteni. De az SQL Server Azure virtuális gépeken való üzemeltetésének egyéb előnyei is vannak. Automatikusan hozzáférést kap például több különböző adatközponthoz is a globális jelenlét érdekében, illetve vészhelyreállítás esetére. A lehetséges forgatókönyvek és előnyök teljes listáját lásd az [SQL Server on Azure VMs product page](https://azure.microsoft.com/services/virtual-machines/sql-server/) SQL Server Azure virtuális gépeken termékoldalt.

> [AZURE.NOTE] Amikor az SQL Server Azure virtuális gépeken való használatát mérlegeli, vegye számításba az Azure-on elérhető egyéb olyan tárolási és SQL-lehetőségeket, mint például az [SQL Database](../sql-database/sql-database-technical-overview.md), az [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) és az [SQL Server Stretch Database](../sql     -server-stretch-database/sql-server-stretch-database-overview.md). Részletes összehasonlításért lásd: [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) (Felhőalapú SQL Server-verzió választása: Azure SQL- (platformszolgáltatási) adatbázis vagy SQL Server Azure virtuális gépeken (IaaS)).

Ha úgy döntött, hogy az Azure virtuális gépeken kívánja futtatni az SQL Servert, el kell döntenie, hogy egy virtuálisgép-rendszerképet használ-e, amely magában foglalja az SQL Server licencelési költségeit. A másik lehetőség egy saját licenc használata (BYOL), amely esetben csak a virtuális gépért kell fizetnie. A következő két szakasz ezeket a lehetőségeket ismerteti.

## 1. lehetőség: SQL virtuális gép üzembe helyezése (percalapú licencelés)
A következő táblázat a virtuálisgép-katalógus elérhető SQL Server-rendszerképeit foglalja össze. Kattintson bármelyik hivatkozásra egy új SQL virtuális gép létrehozásának megkezdéséhez az Ön által választott verzióval, kiadással és operációs rendszerrel. Minden rendszerkép magában foglalja az [SQL Server licencelési költségeit](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Részletes útmutatásért lásd a [Provision a SQL Server virtual machine in the Azure Portal](virtual-machines-windows-portal-sql-server-provision.md) (SQL Server rendszerű virtuális gép létrehozása az Azure Portalon) című oktatóanyagot. Emellett tekintse át az [Performance best practices for SQL Server VMs](virtual-machines-windows-sql-performance.md) (Az SQL Server virtuális gépek teljesítményével kapcsolatos ajánlott eljárások) című cikket, amely leírja, hogyan válassza ki a kiépítés során a megfelelő gépméretet és egyéb lehetőségeket.

|Verzió|Operációs rendszer|Kiadás|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## 2. lehetőség: SQL virtuális gép üzembe helyezése (BYOL)
A másik lehetőség egy saját licenc használata (BYOL). Ebben az esetben csak a virtuális gépért kell fizetnie, az SQL Server licencelésével kapcsolatos egyéb költségek nélkül. A saját licenc használatához használja az SQL Server verzióinak, kiadásainak és operációs rendszereinek alábbi mátrixát. A portálban a rendszerképnevek **{BYOL}** előtagot kapnak.

> [AZURE.IMPORTANT] A BYOL VM-rendszerképek használatához nagyvállalati szerződéssel kell rendelkeznie: [Az Azure frissítési garancián keresztüli licenchordozhatósági programja](https://azure.microsoft.com/pricing/license-mobility/). Továbbá szüksége van egy érvényes licencre is az SQL Server kívánt verziójához/kiadásához. [Meg kell adnia a Microsoftnak a szükséges BYOL-információkat](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) a virtuális gép kiépítésétől számított **10** napon belül.

A [kiépítési útmutatóban](virtual-machines-windows-portal-sql-server-provision.md) foglaltak itt is érvényesek, de a következő **BYOL** rendszerkép-lehetőségek egyikét kell használnia. Emellett tekintse át az [Performance best practices for SQL Server VMs](virtual-machines-windows-sql-performance.md) (Az SQL Server virtuális gépek teljesítményével kapcsolatos ajánlott eljárások) című cikket, amely leírja, hogyan válassza ki a kiépítés során a megfelelő gépméretet és egyéb lehetőségeket.

|Verzió|Operációs rendszer|Kiadás|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Az SQL virtuális gép felügyelete
Az SQL Server virtuális gép kiépítése után több választható felügyeleti feladatot is végrehajthat. Bizonyos szempontokból az SQL Server konfigurálását és felügyeletét pontosan ugyanúgy kell elvégeznie, ahogy helyszíni környezetben tenné. Néhány feladat azonban csak az Azure-ra érvényes. A következő szakaszok kiemelnek néhány ilyen területet, és további információkra mutató hivatkozásokat is tartalmaznak.

### Adatok áttelepítése

Ha van meglévő adatbázisa, érdemes áthelyeznie az újonnan kiépített SQL virtuális gépre. Az áttelepítési lehetőségekért és útmutatásért lásd: [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md) (Adatbázis áttelepítése egy Azure virtuális gépen SQL Serverre).

### Magas rendelkezésre állás konfigurálása

Ha magas rendelkezésre állásra van szüksége, fontolja meg az SQL Server rendelkezésre állási csoportok konfigurálását. Ehhez több Azure virtuális gépre van szükség egy virtuális hálózaton. Az Azure portálon találhat egy sablont, amely beállítja ezt a konfigurációt. További információ: [Configure an AlwaysOn availability group in Azure Resource Manager virtual machines](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) (AlwaysOn rendelkezésre állási csoport konfigurálása Azure Resource Manager virtuális gépeken). Ha saját kezűleg szeretné konfigurálni a rendelkezésre állási csoportot és a kapcsolódó figyelőt, lásd:: [Configure AlwaysOn Availability Groups in Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md) (AlwaysOn rendelkezésre állási csoportok konfigurálása Azure virtuális gépeken).

További magas rendelkezésre állási lehetőségekért lásd: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren).

### Az adatok biztonsági mentése
Az Azure virtuális gépek használni képesek az [automatikus biztonsági mentés](virtual-machines-windows-sql-automated-backup.md) előnyeit, amely rendszeresen biztonsági másolatokat készít az adatbázisról a Blob Storage tárhelyen. Ezt a technikát manuálisan is alkalmazhatja. További információ: [Use Azure Storage for SQL Server Backup and Restore](virtual-machines-windows-use-storage-sql-server-backup-restore.md) (Az Azure Storage használata az SQL Server biztonsági mentéséhez és helyreállításához). A biztonsági mentési és helyreállítási lehetőségek teljes körű áttekintéséért lásd: [Backup and Restore for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md) (Az SQL Server biztonsági mentése és helyreállítása Azure virtuális gépeken).

### Frissítések automatizálása
Az Azure virtuális gépek az [automatikus javítás](virtual-machines-windows-sql-automated-patching.md) segítségével ütemezhetnek egy karbantartási időszakot a fontos Windows- és SQL Server-frissítések automatikus telepítéséhez.

### Felhasználói élmény fokozása program (CEIP)
A Felhasználói élmény fokozása program (CEIP) alapértelmezés szerint engedélyezve van. Ez nem felügyeleti feladat, hacsak nem szeretné letiltani a CEIP-et a kiépítés után. A CEIP testreszabásához vagy letiltásához csatlakozzon a virtuális géphez a távoli asztalról. Ezután futtassa az **SQL Server hiba- és használatai jelentések** segédprogramot. A jelentések letiltásához kövesse az utasításokat.

## Következő lépések
Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).

További kérdései vannak? Először tekintse át [Az SQL Server használatának első lépései az Azure Virtual Machines szolgáltatásban GYIK részét](virtual-machines-windows-sql-server-iaas-faq.md). Emellett a kérdéseit vagy megjegyzéseit az SQL virtuális gépekre vonatkozó bármelyik témakör alján megírhatja, így kapcsolatba léphet a Microsofttal és a közösséggel.



<!--HONumber=Sep16_HO3-->


