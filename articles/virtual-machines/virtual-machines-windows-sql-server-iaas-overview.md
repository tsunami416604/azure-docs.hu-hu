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
    ms.date="10/11/2016"
    ms.author="jroth"/>


# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Az SQL Server használata az Azure Virtual Machines szolgáltatásban – áttekintés

Ez a témakör az SQL Server Azure virtuális gépeken (VM) történő futtatásának lehetőségeit írja le, [hivatkozásokat tartalmaz a portálon elérhető rendszerképekhez](#option-1-create-a-sql-vm-with-per-minute-licensing), valamint tartalmazza a [gyakori feladatok](#manage-your-sql-vm) áttekintését.

>[AZURE.NOTE] Ha már ismeri az SQL Servert, és csak azt szeretné megtudni, hogyan helyezhet üzembe egy SQL Servert futtató virtuális gépet, tekintse meg az [SQL Server rendszerű virtuális gép létrehozása az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md) című cikket.

## <a name="overview"></a>Áttekintés
Amennyiben Ön adatbázis-rendszergazda vagy fejlesztő, az Azure virtuális gépek lehetőséget nyújtanak a helyszíni SQL Server számítási feladatok és alkalmazások áthelyezésére a felhőbe. A következő videó az SQL Server Azure virtuális gépek műszaki áttekintését tartalmazza.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

A videó a következő területeket fedi le:

|Time|Terület|
|---|---|
| 00:21 | Mik azok az Azure virtuális gépek? |
| 01:45 | Biztonság |
| 02:50 | Kapcsolatok |
| 03:30 | Tárolómegbízhatóság és -teljesítmény |
| 05:20 | A virtuális gépek mérete |
| 05:54 | Magas rendelkezésre állás és szolgáltatásszint-szerződés |
| 07:30 | Konfigurációs támogatás |
| 08:00 | Figyelés |
| 08:32 | Bemutató: SQL Server 2016 virtuális gép létrehozása |

>[AZURE.NOTE] A videó középpontjában az SQL Server 2016 áll, de az Azure az SQL Server több verziójához (köztük a 2008-as, 2012-es, 2014-es és 2016-os verziókhoz) biztosít virtuálisgép-rendszerképet. 

## <a name="understand-your-options"></a>A lehetőségek megismerése
Számos oka lehet annak, amiért úgy dönt, az adatokat az Azure-ban tárolja. Ha az alkalmazást áthelyezi az Azure-ba, az növeli a teljesítményt az adatok áthelyezéséhez. De a használata további előnyökkel is jár. Automatikusan hozzáférést kap több különböző adatközponthoz is a globális jelenlét érdekében, illetve vészhelyreállítás esetére. Emellett az adatok is tartósak, és magas szintű biztonság védi őket.

Az Azure virtuális gépeken futó SQL Server a relációs adatok Azure-ban való tárolásának egyik módja. A következő táblázat az Azure-ra vonatkozó SQL-ajánlatok gyors összefoglalását tartalmazza.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| SQL-ajánlat | Leírás |
|---:|---|---|
|![SQL Server az Azure Virtual Machines szolgáltatásban](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server az Azure Virtual Machines szolgáltatásban](https://azure.microsoft.com/services/virtual-machines/sql-server/)|SQL Servert futtathat az Azure Virtual Machines szolgáltatásban (a jelen témakör témája). Közvetlenül felügyelheti a virtuális gépet, és futtathatja az adatbázist az SQL Server kereskedelmi verzióin. |
|![SQL Database](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[SQL Database](https://azure.microsoft.com/services/sql-database/)|Az SQL Database szolgáltatás használatával anélkül férhet hozzá az adatbázisokhoz és méretezheti azokat, hogy kezelnie kellene az alapul szolgáló infrastruktúrát.|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/en-us/services/sql-data-warehouse/)|Az Azure SQL Data Warehouse használatával nagy mennyiségű relációs és nem relációs adatot dolgozhat fel. Skálázható adatraktározási képességeket biztosít szolgáltatásként.|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/en-us/services/sql-server-stretch-database/)|Dinamikusan kiterjesztheti az Azure-ra a Microsoft SQL Server 2016 helyszíni tranzakciós adatait.|

Ezekkel a különböző lehetőségekkel számos forgatókönyv esetében jó választás lehet az SQL Server futtatása Azure-beli virtuális gépeken. Előfordulhat például, hogy egy helyszíni SQL Server-géphez hasonlóan konfigurált Azure-beli virtuális gépre van szüksége, vagy további alkalmazásokat és szolgáltatásokat szeretne futtatni ugyanazon az adatbázis-kiszolgálón. Két forrásanyag segíthet a döntésben szerepet játszó további tényezők végiggondolásában:

 - [Az SQL Server az Azure-beli virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/) témakör áttekinti az SQL Server Azure-beli virtuális gépeken való használatának ajánlott forgatókönyveit. 
 - A [Felhőalapú SQL Server-verzió választása: Azure SQL Database (PaaS) adatbázis vagy az Azure virtuális gépeken futó SQL Server (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) című témakör részletesen összehasonlítja az SQL Database-t és a virtuális gépeken futó SQL Servert.

## <a name="create-a-new-sql-vm"></a>Új SQL virtuális gép létrehozása
Az alábbi szakaszokban közvetlen hivatkozások találhatók az Azure Portalra az SQL Server virtuálisgép-katalógus rendszerképeinek letöltéséhez. Attól függően, hogy milyen rendszerképet választott ki, fizetheti percalapon az SQL Server licencelési költségeit, vagy használhatja a saját licencét (BYOL).

A folyamat részletes útmutatója az [SQL Server rendszerű virtuális gép létrehozása az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md) című oktatóanyagban található. Emellett tekintse át az [Performance best practices for SQL Server VMs](virtual-machines-windows-sql-performance.md) (Az SQL Server virtuális gépek teljesítményével kapcsolatos ajánlott eljárások) című cikket, amely leírja, hogyan válassza ki a kiépítés során a megfelelő gépméretet és egyéb lehetőségeket.

## <a name="option-1:-create-a-sql-vm-with-per-minute-licensing"></a>1. lehetőség: SQL virtuális gép létrehozása percalapú licenceléssel
A következő táblázat a virtuálisgép-katalógus elérhető SQL Server-rendszerképeit foglalja össze. Kattintson bármelyik hivatkozásra egy új SQL virtuális gép létrehozásának megkezdéséhez az Ön által választott verzióval, kiadással és operációs rendszerrel.

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

## <a name="option-2:-create-a-sql-vm-with-an-existing-license"></a>2. lehetőség: SQL virtuális gép létrehozása meglévő licenchez
Saját licencet is használhat (BYOL). Ebben az esetben csak a virtuális gépért kell fizetnie, az SQL Server licencelésével kapcsolatos egyéb költségek nélkül. A saját licenc használatához használja az SQL Server verzióinak, kiadásainak és operációs rendszereinek alábbi mátrixát. A portálon ezek a rendszerképnevek **{BYOL}** előtagot kapnak.

|Verzió|Operációs rendszer|Kiadás|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] A BYOL VM-rendszerképek használatához nagyvállalati szerződéssel kell rendelkeznie: [Az Azure frissítési garancián keresztüli licenchordozhatósági programja](https://azure.microsoft.com/pricing/license-mobility/). Továbbá szüksége van egy érvényes licencre is az SQL Server kívánt verziójához/kiadásához. [Meg kell adnia a Microsoftnak a szükséges BYOL-információkat](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) a virtuális gép kiépítésétől számított **10** napon belül.

## <a name="manage-your-sql-vm"></a>Az SQL virtuális gép felügyelete
Az SQL Server virtuális gép kiépítése után több választható felügyeleti feladatot is végrehajthat. Bizonyos szempontokból az SQL Server konfigurálását és felügyeletét pontosan ugyanúgy kell elvégeznie, ahogy egy helyszíni SQL Server-példánnyal tenné. Néhány feladat azonban csak az Azure-ra érvényes. A következő szakaszok kiemelnek néhány ilyen területet, és további információkra mutató hivatkozásokat is tartalmaznak.

### <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez
Az egyik legalapvetőbb felügyeleti lépés az SQL Serverhez való csatlakozás olyan eszközök segítségével, mint az SQL Server Management Studio (SSMS). További információ az SQL Servert futtató új virtuális géphez való csatlakozásról:[Csatlakozás SQL Servert futtató virtuális gépekhez az Azure-ban](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Adatok áttelepítése
Ha van meglévő adatbázisa, érdemes áthelyeznie az újonnan kiépített SQL virtuális gépre. Az áttelepítési lehetőségekért és útmutatásért lásd: [Migrating a Database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md) (Adatbázis áttelepítése egy Azure virtuális gépen SQL Serverre).

### <a name="configure-high-availability"></a>Magas rendelkezésre állás konfigurálása
Ha magas rendelkezésre állásra van szüksége, fontolja meg az SQL Server rendelkezésre állási csoportok konfigurálását. Ehhez több Azure virtuális gépre van szükség egy virtuális hálózaton. Az Azure portálon találhat egy sablont, amely beállítja ezt a konfigurációt. További információ: [Configure an AlwaysOn availability group in Azure Resource Manager virtual machines](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) (AlwaysOn rendelkezésre állási csoport konfigurálása Azure Resource Manager virtuális gépeken). Ha saját kezűleg szeretné konfigurálni a rendelkezésre állási csoportot és a kapcsolódó figyelőt, lásd:: [Configure AlwaysOn Availability Groups in Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md) (AlwaysOn rendelkezésre állási csoportok konfigurálása Azure virtuális gépeken).

További magas rendelkezésre állási lehetőségekért lásd: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren).

### <a name="back-up-your-data"></a>Az adatok biztonsági mentése
Az Azure virtuális gépek használni képesek az [automatikus biztonsági mentés](virtual-machines-windows-sql-automated-backup.md) előnyeit, amely rendszeresen biztonsági másolatokat készít az adatbázisról a Blob Storage tárhelyen. Ezt a technikát manuálisan is alkalmazhatja. További információ: [Use Azure Storage for SQL Server Backup and Restore](virtual-machines-windows-use-storage-sql-server-backup-restore.md) (Az Azure Storage használata az SQL Server biztonsági mentéséhez és helyreállításához). A biztonsági mentési és helyreállítási lehetőségek teljes körű áttekintéséért lásd: [Backup and Restore for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md) (Az SQL Server biztonsági mentése és helyreállítása Azure virtuális gépeken).

### <a name="automate-updates"></a>Frissítések automatizálása
Az Azure virtuális gépek az [automatikus javítás](virtual-machines-windows-sql-automated-patching.md) segítségével ütemezhetnek egy karbantartási időszakot a fontos Windows- és SQL Server-frissítések automatikus telepítéséhez.

### <a name="customer-experience-improvement-program-(ceip)"></a>Felhasználói élmény fokozása program (CEIP)
A Felhasználói élmény fokozása program (CEIP) alapértelmezés szerint engedélyezve van. Ez a program rendszeres időközönként jelentéseket küld a Microsoftnak az SQL Server fejlesztése érdekében. A CEIP-pel nem kell felügyeleti feladatokat végezni, hacsak nem kívánja letiltani az üzembe helyezés után. A CEIP testreszabásához vagy letiltásához csatlakozzon a virtuális géphez a távoli asztalról. Ezután futtassa az **SQL Server hiba- és használatai jelentések** segédprogramot. A jelentések letiltásához kövesse az utasításokat. 

Tovább információért lásd a [Licencfeltételek elfogadása](https://msdn.microsoft.com/library/ms143343.aspx) című témakör CEIP-ről szóló szakaszát. 

## <a name="next-steps"></a>Következő lépések
Az Azure virtuális gépeken futó SQL Server [képzési tervének felfedezése](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).

További kérdései vannak? Először tekintse át [Az SQL Server használatának első lépései az Azure Virtual Machines szolgáltatásban GYIK részét](virtual-machines-windows-sql-server-iaas-faq.md). Emellett a kérdéseit vagy megjegyzéseit az SQL virtuális gépekre vonatkozó bármelyik témakör alján megírhatja, így kapcsolatba léphet a Microsofttal és a közösséggel.



<!--HONumber=Oct16_HO3-->


