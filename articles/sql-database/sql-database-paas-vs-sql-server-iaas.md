---
title: A platformszolgáltatásos SQL Database és A SQL Server futtatása felhőben, virtuális gépeken (Iaas) | Microsoft Docs
description: 'Cikkünkből megtudhatja, melyik felhőalapú SQL Server-verzió illik a legjobban az alkalmazásához: az Azure SQL Database (platformszolgáltatásos) adatbázis vagy az Azure virtuális gépeken futó felhőalapú SQL Server?'
services: sql-database, virtual-machines
keywords: SQL Server-felhő, SQL Server a felhőben, PaaS-adatbázis, felhőalapú SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: a212a595c02a048721f1a8753b437f74f2fc4973
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308954"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Felhőalapú SQL Server-verzió választása: Azure SQL Database (PaaS) adatbázis vagy az Azure virtuális gépeken futó SQL Server (IaaS)

Az Azure, az SQL Server számítási feladatait a szolgáltatott infrastruktúra (IaaS) futtatja, vagy egy üzemeltetett szolgáltatást futtató lehet ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

* [Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/): A SQL adatbázis-kezelő, a vállalati az SQL Server kiadása, modern alkalmazások fejlesztésére optimalizált alapján. Az Azure SQL Database kínál az üzemeltetett szolgáltatás SQL két verziója: logikai kiszolgáló és [Azure SQL adatbázis felügyelt-példány (előzetes verzió)](sql-database-managed-instance.md). Mindkét verzióival Azure SQL Database ad további szolgáltatásokat, amelyek nem érhető el az SQL Server, például a beépített funkciói és kezelése. Az első verzióval is rendelkezik egy logikai kiszolgáló tartalmazó [adatbázisok egyetlen](sql-database-servers-databases.md) és csoportosíthatja a kiszolgálók egy [rugalmas készlet](sql-database-elastic-pool.md) osztozik az erőforrásokon, és csökkentheti a költségeket. Egy Azure SQL Database logikai kiszolgáló egyetlen és készletezett adatbázisokat tartalmazó legtöbb az SQL Server adatbázis-alapú kötődő szolgáltatást kínál. Azure SQL adatbázis felügyelt példánnyal az Azure SQL Database megosztott erőforrások-adatbázisokhoz és további példányt hatókörbe tartozó szolgáltatásokat kínál. Az Azure SQL adatbázis felügyelt példány adatbázis áttelepítését támogatja az adatbázis változás minimális.
* [SQL Server Azure virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server telepített és futó Azure, más néven az infrastruktúrák (IaaS) a Windows Server vagy Linux rendszerű virtuális gépek (VM) felhőalapú. Azure virtuális gépeken futó SQL Server rendszer áttelepítése jó választás a helyszíni SQL Server-adatbázisok és az alkalmazások bármely adatbázis módosítása nélkül. Minden újabb verzióit és kiadásait az SQL Server IaaS virtuális gépként való telepítésekor érhetők el. SQL-adatbázisból a legfontosabb különbség az, hogy az SQL Server virtuális gépen engedélyezik teljes hozzáféréssel az adatbázismotor. Dönthet úgy, ha karbantartási/javítását elindul, változtassa meg a helyreállítási modellt egyszerűre vagy Tömegesen naplózott gyorsabb terhelés kevésbé felfüggesztése vagy motor szükség esetén indítsa el a napló engedélyezése és az SQL Server adatbázismotor teljes mértékben testreszabható. További vezérlőhöz hozzáadott felelőssége, hogy a virtuális gépeket tartalmaz.

Ebből a cikkből megtudhatja, hogyan illeszkednek ezek a verziók a Microsoft adatplatformjának rendszerébe, és rátalálhat az üzleti igényeinek leginkább megfelelő lehetőségre. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.

## <a name="microsofts-sql-data-platform"></a>A Microsoft SQL data platform

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását. Az Azure-beli virtuális gépeken futó SQL Server helyszíni és felhőben futó üzemelő példányok együttes használatával segít megfelelni az egyedi és sokszínű üzleti igényeiknek, ráadásul Ön az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőalapú SQL Server-verziók: SQL Server infrastruktúra-szolgáltatásban vagy SaaS SQL-adatbázis a felhőben.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

* SQL Server nem virtuális, fizikai gépeken
* SQL Server helyszíni virtuális gépeken (magánfelhő)
* SQL Server Azure virtuális gépeken (Microsoft nyilvános felhő)
* Azure SQL Database (Microsoft nyilvános felhő)

A következő szakaszokban megismerheti a Microsoft nyilvános felhőben futó SQL Servert: Azure SQL Database és Azure virtuális gépeken futó SQL Server. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása

**Az Azure SQL Database** egy relációs adatbázis-a-szolgáltatás (típusú), amely a kategóriába esik az Azure felhőalapú rendszer *Platform,--szolgáltatás (PaaS)*. Az [SQL Database](sql-database-technical-overview.md) a Microsoft tulajdonában álló, illetve általa futtatott és fenntartott szabványos hardvereken és szoftvereken fut. Az SQL Database beépített szolgáltatásai és funkciói széles körű konfigurációs SQL Server igénylő is használhatja. Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. Az Azure SQL-adatbázis, mind a támogató [adatbázisok egyetlen](sql-database-servers-databases.md) és [rugalmas készletek](sql-database-elastic-pool.md) erőforrások megosztása esetén ideális környezetet a felhőben új alkalmazások fejlesztésével. És a [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md), saját licenc helyezheti. Emellett ezzel a beállítással biztosít minden Azure SQL Database PaaS előnyei, azonban, korábban csak az SQL virtuális gépeken lényeges képességét biztosítja. Ez egy natív virtuális hálózatot (VNet) és tartalmazza a helyszíni SQL Server 100 %-os-kompatibilitási közelében. [Felügyelt példány](sql-database-managed-instance.md) ideális, az a helyi adatbázis áttelepítése az Azure legfeljebb minimális változtatásokra szükséges. 

Az **Azure virtuális gépeken futó SQL Server** az *infrastruktúra-szolgáltatás (IaaS)* kategóriába esik. Ebben a verzióban az SQL Server egy felhőalapú virtuális gépen fut. [SQL Server virtuális gépek](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) is futtathat, amely tulajdonban lévő, futó, és a Microsoft által fenntartott szabványos hardvereken. SQL Server használata a virtuális gép, vagy a fizetési is – akkor-nyissa meg az SQL Server licenccel már szerepel egy SQL Server-rendszerképet, mivel a(z) vagy egy meglévő licenc könnyen használható. Állítsa le vagy folytathatja a virtuális gép, igény szerint is.

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

* **Az Azure SQL Database** csökkenthetők a költségek felügyeleti kiépítése és kezelése számos más adatbázis esetében a lehető megfelelően lett optimalizálva. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](sql-database-automated-backups.md) kezelésével sem. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá. [Rugalmas készletek](sql-database-elastic-pool.md) is támogatja a Szolgáltatottszoftver-alkalmazás több-bérlős architektúrák szolgáltatásokkal, beleértve a bérlők elkülönítését és a költségek csökkentése az erőforrások megosztása az adatbázisok közötti méretezési képességét. [Az Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md) támogatást nyújt a példány hatókörbe tartozó szolgáltatások a meglévő alkalmazások egyszerű áttelepítési engedélyezése, valamint a megosztott erőforrásokat adatbázisok között.
* Az **Azure virtuális gépeken futó SQL Server** segítségével az Azure-ba helyezhet meglévő alkalmazásokat, vagy hibrid megoldásként terjeszthet ki a felhőbe meglévő helyszíni alkalmazásokat. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

| | Azure SQL Database<br>Logikai kiszolgáló, a rugalmas készletek és az önálló adatbázisok | Azure SQL Database<br>Felügyelt példány |Az Azure virtuális gépek szolgáltatás<br>SQL Server |
| --- | --- | --- |---|
| **A következőkre alkalmas:** |Új felhőalapú alkalmazásokhoz, szeretne a legújabb stabil SQL Server szolgáltatások andhave idő korlátozásokat használni a fejlesztési és marketing. | Új alkalmazások vagy a meglévő helyszíni alkalmazásokat, és hogy a legújabb stabil SQL Server szolgáltatásai használni kívánt települnek át a felhőbe legfeljebb minimális változtatásokra.  | Gyors áttelepítés a felhőbe a minimális módosításait vagy nem végez módosítást igénylő meglévő alkalmazásokat. Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. |
|  | Olyan csoportok számára, amelyeknek beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokkal rendelkező adatbázisokra van szüksége. | Ugyanaz, mint az SQL-adatbázis. | Csoportok, amelyeket konfigurálhat, részletes hangolására, testreszabása és magas rendelkezésre állás, a vész-helyreállítási és a javítás az SQL Server kezeléséhez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. | |
|  | Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni. | Ugyanaz, mint az SQL-adatbázis. | Teljes körű rendszergazdai jogosultságokkal rendelkező testreszabott környezetre van szükség. | |
|  | Akár 4 TB-adatbázisok vagy nagyobb adatbázisok, amelyek lehetnek [vízszintes vagy függőleges irányú particionálva](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) kibővített minta használatával. | Ugyanaz, mint az SQL-adatbázis. | Akár 64 TB tárterülettel rendelkező SQL Server-példányokhoz. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| **Kompatibilitási** | A legtöbb a helyi adatbázis-szintű lehetőségeket támogat. | Szinte minden támogatja a helyi példány- és adatbázis képességeket. | Minden a helyszíni szolgáltatásokkal támogatja. |
| **Erőforrások:** | Ha nem szeretne az alapul szolgáló infrastruktúra konfigurálására és felügyeletére informatikai erőforrásokat fordítani, hanem az alkalmazásrétegre kíván összpontosítani. | Ugyanaz, mint az SQL-adatbázis. | Ha rendelkezik bizonyos informatikai erőforrásokkal a konfigurációhoz és felügyelethez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
| **Tulajdonosi költségek:** | Nincsenek hardverköltségek és alacsonyabbak a felügyeleti költségek. | Ugyanaz, mint az SQL-adatbázis. | Nincsenek hardverköltségek. |
| **Az üzletmenet folytonossága** |Kívül [beépített tartalék tolerancia infrastruktúra lehetőségeit](sql-database-high-availability.md), az Azure SQL Database funkciókat is biztosítanak, például a [biztonsági mentések automatikus](sql-database-automated-backups.md), [pont időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore), [georedundáns helyreállítás](sql-database-recovery-using-backups.md#geo-restore), és [feladatátvételi csoportok és aktív georeplikáció](sql-database-geo-replication-overview.md) üzletmenet folytonosságát elősegítő. További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése). | Ugyanaz, mint az SQL-adatbázis, valamint a felhasználó által kezdeményezett, csak másolatot biztonsági mentéseket érhetők el. | Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren). |
| **Hibrid felhő:** |A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére. | [Natív virtuális hálózati megvalósítási](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration) és a helyszíni környezetben Azure Express Route vagy VPN-átjárót, kapcsolattal. | Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. Ezenfelül az [Azure SQL Server-adatfájlok](http://msdn.microsoft.com/library/dn385720.aspx) funkciójával elhelyezheti a helyszíni adatfájlokat az Azure Storage tárhelyen. További információk: [Introduction to SQL Server 2014 Hybrid Cloud](http://msdn.microsoft.com/library/dn606154.aspx) (Bevezetés az SQL Server 2014 által biztosított hibrid felhő használatába). |
|  | Előfizetőként támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx) az adatok replikálása céljából. | Azure SQL adatbázis felügyelt példány replikálása nem támogatott. | Teljes mértékben támogatja [SQL Server tranzakciós replikáció](https://msdn.microsoft.com/library/mt589530.aspx), [Always On rendelkezésre állási csoportok](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), az Integration Services és a Naplóküldés replikálják az adatokat. Emellett a hagyományos SQL Server-biztonságimásolatok is teljes körűen támogatottak. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok
### <a name="cost"></a>Költségek
Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a részben először az Azure-ben elérhető két relációsadatbázis-változatra (az SQL Database-re és az Azure virtuális gépeken futó SQL Serverre) vonatkozó számlázási és licencelési alapokkal ismerkedhet meg. Megismerheti az alkalmazás összköltségének kiszámításának módját is.

#### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

Jelenleg **SQL-adatbázis** szolgáltatásként értékesítik, és elérhető erőforrások, amelyek amelyekért óránkénti alapján választja szolgáltatásszintet és teljesítményszintet rögzített alapján különböző árakkal többféle szolgáltatáscsomagban kapható. SQL adatbázis felügyelt példánnyal is helyezheti a saját licenc. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. Szolgáltatásszintek és teljesítményszintek az alkalmazás változó sebességigényét dinamikusan módosíthatja. A legfrissebb információkat az aktuálisan választható szolgáltatásszintekkel, lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md). Is létrehozhat [rugalmas készletek](sql-database-elastic-pool.md) megoszthatják az erőforrásokat, hogy a költségek csökkentése és a használati megfeleljen a napra.

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-automated-backups.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ. 

Az **Azure virtuális gépeken futó SQL Server** segítségével használhatja a platform által biztosított bármelyik SQL Server-rendszerképet (amely tartalmaz licencet) vagy saját SQL Server-licencét. A támogatott SQL Server-verziók (2008R2, 2012, 2014, 2016) és -kiadások (Developer, Express, Web, Standard, Enterprise) mindegyike elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. Virtuálisgép-méretet és az SQL Server olyan kiadása, függetlenül percalapú licencelési költségeit-az SQL Server és a Windows vagy Linux-kiszolgálón, az Azure Storage költség, a virtuális gép lemezeivel együtt kell fizetnie. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha később saját SQL Server licence az Azure-ba, van szó, a kiszolgáló, és csak a tárolási költségeket. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása
Ha a felhőplatform használatának megkezdése az alkalmazás futtatásának tartalmazza a új fejlesztésére és adminisztrációra fordítandó összeget, költségét a nyilvános felhő platform szolgáltatási költségei.

**Az Azure SQL Database használata esetében:**

- Legalacsonyabbra csökkentett adminisztrációs költségek
- Az áttelepített alkalmazások korlátozott költségek
- SQL Database szolgáltatási költségei
- Nincsenek hardverköltségek beszerzési

**Az Azure virtuális gépeken futó SQL Server esetében:**

- Nagyobb adminisztrációs költségek
- Az áttelepített alkalmazások fejlesztési költség legfeljebb
- Virtuális gép szolgáltatási költségei
- SQL Server-licenc költségek
- Nincsenek hardverköltségek beszerzési

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

* [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
* [Virtuális gépek – Díjszabás ](https://azure.microsoft.com/pricing/details/virtual-machines/) ([SQL-hez](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és [Windowshoz](https://azure.microsoft.com/pricing/details/virtual-machines/#windows))
* [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Adminisztráció
Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az IaaS és PaaS, a Microsoft felügyeli az alkalmazás mögötti infrastruktúra és automatikusan replikálja az összes adatot adja meg a vész-helyreállítási, konfigurálja és az adatbázisszoftver konfigurálásáért, kezeli a terheléselosztás és does a transzparens feladatátvételt, ha van egy Kiszolgálóhiba adatközponton belül. 

- A **Azure SQL Database**, továbbra is az adatbázis felügyeletére, de már nincs szüksége az adatbázis-kezelő, a kiszolgáló operációs rendszer vagy a hardver felügyeletéhez.  Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett egy másik adatközpontba magas rendelkezésre állás konfigurálása szükséges minimális konfigurációs és felügyeleti.
- Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban). 

### <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)
Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

A **SQL-adatbázis**, a Microsoft 99,99 %-os SLA-elérhetőséget garantál biztosít. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/). 

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Adatbázis magas rendelkezésre ÁLLÁS virtuális gépeken, úgy kell konfigurálnia a támogatott magas rendelkezésre állású lehetőség egyikét az SQL Server, például a [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

### <a name="market"></a>Az Azure-bA áthelyezéséhez szükséges idő
**SQL Database logikai kiszolgáló, a rugalmas készletek és az önálló adatbázisok** akkor ideális megoldás a felhőalapú alkalmazásokhoz, ha a fejlesztés és a gyors idő piacra jutási noew megoldások kritikus. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt. 

**SQL-adatbázis felügyelt példány** jelentősen egyszerűbb a meglévő alkalmazások az Azure SQL Database, amely lehetővé teszi, hogy áttelepített adatbázis alkalmazások az Azure-ban a gyorsan piaci áttelepítését.

**Azure virtuális gépeken futó SQL Server** tökéletes, ha a meglévő vagy új alkalmazásainak igényelnek nagy adatbázisok vagy nem éri el az SQL Server vagy windowsos/Linuxos összes szolgáltatásait, és el szeretné kerülni, hogy időt és költséget az beszerzése a helyi új hardver. Egyúttal remekül beválik, ha meg szeretné áttelepíteni meglévő helyszíni alkalmazásokat és adatbázisokat Azure-bA-az - azokban az esetekben, ahol Azure SQL adatbázis felügyelt példány nincs remekül beválik. Mivel így nincs szükség a prezentációs, alkalmazási és adatréteg módosítására, időt és pénzt takarít meg, amit nem kell meglévő termékeinek egy új architektúrára való áthelyezésére fordítania. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## <a name="summary"></a>Összegzés
Ebben a cikkben bemutattuk az SQL Database-t és az Azure virtuális gépeken futó SQL Servert, és szót ejtettünk azokról az üzleti szempontokról, amelyek hatással lehetnek választására. Az alábbiakban a megfontolásra érdemes tényeket foglaljuk össze:

Válassza az **Azure SQL Database-t**, ha:

* Új felhőalapú alkalmazásokat fejleszt, hogy igénybe vehesse a felhőszolgáltatások által biztosított költségmegtakarításokat és optimalizált teljesítményt. Ezzel a verzióval a teljes körűen felügyelt felhőszolgáltatások minden előnyét élvezheti, ezenfelül csökkentheti a piacra jutási időt, illetve hosszú távon is optimalizálhatja kiadásait.
* E megoldás választása esetén érdemes a Microsoftra bízni az adatbázisok általános felügyeleti műveleteinek elvégzését, és erősebb rendelkezésre állási SLA-kat igényelni az adatbázisokhoz.
* Egy meglévő alkalmazást, mint az áttelepítendő-Azure SQL adatbázis felügyelt példányhoz, és az SQL Server és/vagy a speciális biztonsági és a hálózat további paritásos előnyeit. Felügyelt példány jó választás az új és meglévő alkalmazások.

Válassza az **Azure virtuális gépeken futó SQL Servert**, ha:

* Meglévő helyszíni alkalmazásokat használni kívánt áttelepítése vagy kiterjesztése a felhőbe, vagy ha azt szeretné, ha 4 TB-nál nagyobb vállalati alkalmazásokat szeretne összeállítani. Ezt a módszert biztosít az az előnye, hogy az SQL Server verziójának és kiadásának a választott, a nagy adatbázis, az SQL Server és a Windows/Linux teljes hozzáféréssel használ, és secure tunneling helyszíni. Ez a verzió a lehető legalacsonyabbra csökkenti a meglévő alkalmazások fejlesztési és átalakítási költségeit.
* Meglévő számítógépes erőforrások használatával Ön irányíthatja a frissítéseket, a biztonsági mentéseket és az adatbázisok magas rendelkezésre állását. Megjegyzendő, hogy egyes automatizált funkciók jelentősen leegyszerűsítik ezeket a műveleteket. 

## <a name="next-steps"></a>További lépések

* Tekintse meg [az első Azure SQL Database-adatbázissal](sql-database-get-started-portal.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
* [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
* Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).