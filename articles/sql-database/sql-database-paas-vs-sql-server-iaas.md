---
title: A platformszolgáltatásos SQL Database és A SQL Server futtatása felhőben, virtuális gépeken (Iaas) | Microsoft Docs
description: 'Cikkünkből megtudhatja, melyik felhőalapú SQL Server-verzió illik a legjobban az alkalmazásához: az Azure SQL Database (platformszolgáltatásos) adatbázis vagy az Azure virtuális gépeken futó felhőalapú SQL Server?'
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-felhő, SQL Server a felhőben, PaaS-adatbázis, felhőalapú SQL Server, DBaaS
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: ff6b535b67608d9331e134ff3b3d943601e73a48
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364489"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Felhőalapú SQL Server-verzió választása: Azure SQL Database (PaaS) adatbázis vagy az Azure virtuális gépeken futó SQL Server (IaaS)

Az Azure-ban, az SQL Server számítási feladatok egy szolgáltatott infrastruktúra (IaaS) futtatja, vagy egy üzemeltetett szolgáltatást futtató rendelkezhet ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

- [Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/): egy SQL-adatbázismotor, az Enterprise Edition az SQL Server, a modern alkalmazások fejlesztését optimalizált alapján. Az Azure SQL Database több központi telepítési lehetőséget kínál:

  - Telepíthet egyetlen-adatbázist egy [logikai kiszolgáló](sql-database-logical-servers.md).
  - Üzembe helyezhető egy [rugalmas készlet](sql-database-elastic-pool.md) a egy [logikai kiszolgáló](sql-database-logical-servers.md) erőforrások megosztásához, és csökkentheti a költségeket.
  - Telepíthet egy [Azure SQL Database felügyelt példányain](sql-database-managed-instance.md).

   Az alábbi ábra ezt a három üzembehelyezési modellt mutatja be:

     ![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

     > [!NOTE]
     > Összes három verzió, az Azure SQL Database ad a további funkciók, amelyek nem érhetők el az SQL Server, például a beépített intelligenciával és a felügyeleti. Egy logikai kiszolgálón egyetlen vagy készletezett adatbázisokat tartalmazó legtöbb az SQL Server adatbázis-specifikus szolgáltatást kínál. Az Azure SQL Database felügyelt példányába az Azure SQL Database megosztott erőforrásokat az adatbázisok és további példányok hatókörébe tartozó szolgáltatásokat kínál. Az Azure SQL Database felügyelt példánya támogatja az adatbázis-migrálás minimális nincs adatbázis módosítása.

- [Az SQL Server Azure virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server telepítve van, és az Azure-ban, más néven egy infrastruktúra-szolgáltatás (IaaS) futó Windows Server vagy Linux rendszerű virtuális gépeken (VM) a felhőben. Migrálás jó választás a helyszíni SQL Server-adatbázisokat és alkalmazásokat adatbázis változás nélkül, egy SQL Server Azure-beli virtuális gépeken. Az összes legújabb verzióit és kiadásait az SQL Server telepítési IaaS virtuális gépen érhetők el. Az SQL Database-ből legfontosabb különbség az, hogy engedélyezik-e teljes körűen felügyelve az adatbázismotor SQL Server virtuális gépek. Amikor karbantartás vagy javítások elindul, változtassa meg a helyreállítási modelljét egyszerűre vagy Tömegesen naplózott ahhoz, hogy gyorsabban betöltődő kevésbé napló, szüneteltetése vagy indítsa el a motor, amikor szükséges, és teljes mértékben testre szabható az SQL Server adatbázismotor választhat. A további felügyelet együttműködik a hozzáadott feladata, hogy a virtuális gépek felügyeletéhez.

Ismerje meg, hogyan illeszkedik a különböző telepítési lehetőségek a Microsoft data platformon, és rátalálhat az üzleti igényeinek leginkább megfelelő lehetőségre. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.

## <a name="microsofts-sql-data-platform"></a>A Microsoft SQL-adatplatform

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását. Az Azure-beli virtuális gépeken futó SQL Server helyszíni és felhőben futó üzemelő példányok együttes használatával segít megfelelni az egyedi és sokszínű üzleti igényeiknek, ráadásul Ön az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőalapú SQL Server-verziók: SQL Server infrastruktúra-szolgáltatásban vagy SaaS SQL-adatbázis a felhőben.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

- SQL Server nem virtuális, fizikai gépeken
- SQL Server helyszíni virtuális gépeken (magánfelhő)
- SQL Server Azure virtuális gépeken (Microsoft nyilvános felhő)
- Azure SQL Database (Microsoft nyilvános felhő)

A következő szakaszokban megismerheti a Microsoft nyilvános felhőben futó SQL Servert: Azure SQL Database és Azure virtuális gépeken futó SQL Server. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása

- **Azure SQL Database**

Egy relációs adatbázis--szolgáltatásként (DBaaS), amely az iparági kategóriába esik az Azure felhőalapú *Platform--szolgáltatásként (PaaS)*. Az [SQL Database](sql-database-technical-overview.md) a Microsoft tulajdonában álló, illetve általa futtatott és fenntartott szabványos hardvereken és szoftvereken fut. Az SQL Database beépített szolgáltatásai és funkciói széles körű konfigurációt az SQL Server igénylő is használhatja. Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. Azure SQL Database egy új alkalmazások felhőbeli fejlesztéséhez ideális környezetet. És a [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md), a saját licencét is tenné. Ez a beállítás emellett biztosít minden Azure SQL Database a PaaS előnyei, de csak korábban elérhető SQL virtuális gépek képességekkel bővíti. Ez magában foglalja egy natív virtuális hálózati (VNet) és közel 100 %-os kompatibilitást a helyszíni SQL Server. [Felügyelt példány](sql-database-managed-instance.md) ideális megoldást biztosít a helyszíni migrálását az Azure-adatbázis minimális módosítással.

- **Az SQL Server Azure virtuális gépeken (VM)**

Az iparági kategóriába esik *infrastruktúra--szolgáltatásként (IaaS)* , és lehetővé teszi, hogy az SQL Server futtatása a felhőben egy virtuális gépen. [Az SQL Server-virtuálisgépek](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) szabványos hardvereken, hogy a rendszer tulajdonában lévő üzemeltetett és a Microsoft által karbantartott is futtathatja. Az SQL Server virtuális gépen is fizetheti-,-nyissa meg az SQL Server-licence már szerepel egy SQL Server-lemezképet, vagy egyszerűen használhatja a meglévő licenchez. Állítsa le vagy folytatása a virtuális gép igény szerint is.

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

- **Azure SQL Database**

Optimalizált kiépítéséhez és a számos adatbázis kezelése a minimális átfogó felügyeleti költségek csökkentése érdekében. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](sql-database-automated-backups.md) kezelésével sem. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá. [Rugalmas készletek](sql-database-elastic-pool.md) is támogatja a SaaS több-bérlős architektúrák funkciókat, beleértve a bérlők elkülönítését és az erőforrások adatbázisok közötti megosztása a költségcsökkentés érdekében méretezhető. [Az Azure SQL Database felügyelt példányain](sql-database-managed-instance.md) támogatást biztosít a példány hatókörű funkciói a meglévő alkalmazások egyszerű migrálás engedélyezéséhez, valamint az erőforrások adatbázisok közötti megosztása.

- **Azure virtuális gépeken futó SQL Server**

Meglévő alkalmazások Azure-ba való migrálás vagy kiterjesztése a meglévő helyszíni alkalmazásokkal, a hibrid felhőre optimalizált. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

| | Azure SQL Database<br>Logikai kiszolgáló, a rugalmas készletek és az önálló adatbázisok | Azure SQL Database<br>Felügyelt példány |Az Azure virtuális gépek szolgáltatás<br>SQL Server |
| --- | --- | --- |---|
| **A következőkre alkalmas:** |Új felhőalapú alkalmazásai számára, hogy a legújabb stabil az SQL Server funkciói és fejlesztési és a marketing szűkösebb időkeret áll. | Új alkalmazások vagy a meglévő helyszíni alkalmazásokat, amelyeket szeretne a legújabb stabil SQL Server-funkciók és, amely a felhőbe legfeljebb minimális változtatásokra lesznek áttelepítve.  | Igénylő alkalmazásokhoz, gyors áttelepítés a felhőbe, a minimális módosításait vagy módosítása nélkül. Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. |
|  | Olyan csoportok számára, amelyeknek beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokkal rendelkező adatbázisokra van szüksége. | Ugyanaz, mint az SQL Database. | Csoportok, amelyek konfigurálni, részletes hangolása, testreszabása és magas rendelkezésre állást, vészhelyreállítást és javítása az SQL Server kezeléséhez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. | |
|  | Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni. | Ugyanaz, mint az SQL Database. | Teljes körű rendszergazdai jogosultságokkal rendelkező testre szabott környezetre van szüksége. | |
|  | Akár 100 TB-os adatbázis. | Ugyanaz, mint az SQL Database. | Akár 64 TB tárterülettel rendelkező SQL Server-példányokhoz. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| **Kompatibilitási** | Támogatja a legtöbb a helyszíni adatbázis-szintű képességeit. | Szinte minden támogatja a helyszíni példányszintű és az adatbázis-szintű képességek. | Támogatja az összes helyszíni képességeit. |
| **Erőforrások:** | Nem szeretne konfigurálása és felügyelete az alapul szolgáló infrastruktúra informatikai erőforrásokat fordítani, de szeretné az alkalmazási rétegre összpontosíthat. | Ugyanaz, mint az SQL Database. | Ha rendelkezik bizonyos informatikai erőforrásokkal a konfigurációhoz és felügyelethez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
| **Tulajdonosi költségek:** | Nincsenek hardverköltségek és alacsonyabbak a felügyeleti költségek. | Ugyanaz, mint az SQL Database. | Nincsenek hardverköltségek. |
| **Az üzletmenet folytonossága** |Mellett [infrastruktúra beépített hibatűrési képességeket](sql-database-high-availability.md), Azure SQL Database olyan szolgáltatásokat nyújt, mint például [automatizált biztonsági mentések](sql-database-automated-backups.md), [-időponthoz visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore), és [feladatátvételi csoportok és az aktív georeplikáció](sql-database-geo-replication-overview.md) üzletmenet-folytonossági növelése érdekében. További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése). | Ugyanaz, mint az SQL Database, valamint a felhasználó által kezdeményezett, csak másolatot biztonsági mentéseket érhetők el. | Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren). |
| **Hibrid felhő:** |A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére. | [Natív virtuálishálózat-implementáció](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration) és a helyszíni környezet Azure Express Route vagy VPN-átjáró használatával való kapcsolódás. | Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. Ezenfelül az [Azure SQL Server-adatfájlok](http://msdn.microsoft.com/library/dn385720.aspx) funkciójával elhelyezheti a helyszíni adatfájlokat az Azure Storage tárhelyen. További információk: [Introduction to SQL Server 2014 Hybrid Cloud](http://msdn.microsoft.com/library/dn606154.aspx) (Bevezetés az SQL Server 2014 által biztosított hibrid felhő használatába). |
|  | Előfizetőként támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx) az adatok replikálása céljából. | Replikálás az Azure SQL Database felügyelt példányain nem támogatott. | Teljes körűen támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx), [Always On rendelkezésre állási csoportok](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services és a Naplóküldést az adatok replikálása. Emellett a hagyományos SQL Server-biztonságimásolatok is teljes körűen támogatottak. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok

### <a name="cost"></a>Költségek

Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a részben először az Azure-ben elérhető két relációsadatbázis-változatra (az SQL Database-re és az Azure virtuális gépeken futó SQL Serverre) vonatkozó számlázási és licencelési alapokkal ismerkedhet meg. Megismerheti az alkalmazás összköltségének kiszámításának módját is.

#### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

Jelenleg **SQL Database** szolgáltatásként vásárolható meg, és a különböző árakat, erőforrások, amelyek mindegyike számlázzuk óránként egy rögzített a szolgáltatási szint és a választott számítási mérete alapján többféle szolgáltatáscsomagban érhető el. Az SQL Database felügyelt példánya a saját licencét is használhatja. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. Módosíthatja a szolgáltatási szintek és méretek szerint változó átviteli igényekhez az alkalmazás számítási dinamikusan. A legfrissebb információkért támogatott az aktuális szolgáltatásszintek, lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). Ezenkívül létrehozhat [rugalmas készletek](sql-database-elastic-pool.md) megosztott erőforrások adatbázispéldányok csökkentheti a költségeket és befogadásához használat hirtelen megugró kihasználtság.

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-automated-backups.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure virtuális gépeken futó SQL Server** segítségével használhatja a platform által biztosított bármelyik SQL Server-rendszerképet (amely tartalmaz licencet) vagy saját SQL Server-licencét. A támogatott SQL Server-verziók (2008R2, 2012, 2014, 2016) és -kiadások (Developer, Express, Web, Standard, Enterprise) mindegyike elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. Függetlenül a virtuális gép méretétől és az SQL Server percalapú licencelési költségeit az SQL Server és a Windows vagy Linux-kiszolgálón, valamint a virtuális gép lemezeivel kapcsolatban felmerülő Azure Storage költségeket kell fizetnie. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Az Azure-ban tenné a saját SQL Server-licencét, ha a kiszolgáló, és csak a tárolási költségek díjkötelesek. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása

Ha elkezd használni egy felhőalapú platform, az alkalmazás futtatásának díja tartalmazza az új fejlesztési költség- és adminisztrációra fordítandó összeget, valamint a nyilvános felhő platform szolgáltatási költségei teszik.

**Az Azure SQL Database használata esetében:**

- Legalacsonyabbra csökkentett adminisztrációs költségek
- Az áttelepített alkalmazások korlátozott szoftverfejlesztési költségek
- Az SQL Database szolgáltatási költségei
- Nincsenek hardverköltségek vásárlási

**Az Azure virtuális gépeken futó SQL Server esetében:**

- Nagyobb adminisztrációs költségek
- Kizárólag a migrált alkalmazások fejlesztési költségek nélkül
- Virtuális gép szolgáltatási költségei
- Az SQL Server-licenc költségeit
- Nincsenek hardverköltségek vásárlási

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtuális gépek – Díjszabás ](https://azure.microsoft.com/pricing/details/virtual-machines/) ([SQL-hez](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és [Windowshoz](https://azure.microsoft.com/pricing/details/virtual-machines/#windows))
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Adminisztráció

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az IaaS és PaaS, a Microsoft felügyeli az alapul szolgáló infrastruktúra, és automatikusan biztosít a vész-helyreállítási adatok, konfigurálja és frissíti az adatbázisszoftvert, kezeli a terheléselosztást és does a transzparens feladatátvételt, ha van egy Kiszolgálóhiba adatközponton belül.

- A **Azure SQL Database**, továbbra is az adatbázis, de az adatbázismotor, a kiszolgálói operációs rendszer vagy a hardveres már nincs szüksége.  Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett egy másik adatközpontba magas rendelkezésre állás konfigurálása szükséges minimális konfiguráció és felügyelet.
- Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

### <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

A **SQL Database**, a Microsoft biztosít garantált 99,99 %. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/).

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Az adatbázis magas rendelkezésre ÁLLÁS virtuális gépeken belül, konfigurálnia kell a támogatott magas rendelkezésre állású egyikét az SQL Server, mint például [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

### <a name="market"></a>Az Azure-bA áthelyezéséhez szükséges idő

**Az SQL Database logikai kiszolgálók, rugalmas készletek és önálló adatbázisokat** fejlesztői hatékonyságot és gyors-piacra új megoldások különösen fontos a megfelelő megoldás az felhőbeli környezethez tervezett alkalmazások esetén. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt.

**SQL Database felügyelt példányain** jelentősen leegyszerűsíti a meglévő alkalmazások az Azure SQL Database, lehetővé téve a migrált adatbázis alkalmazások az Azure-ban gyorsan piacra használata az áttelepítés.

**Azure virtuális gépeken futó SQL Server** tökéletes megoldás, ha meglévő vagy új alkalmazásainak nagy méretű adatbázisokhoz szükséges, vagy az SQL Server vagy Windows/Linux, az összes funkciójához hozzáférést, és el szeretné kerülni az idő és új beszerzésének költségek a helyszíni hardver. Az is jó megoldás, ha meg szeretné áttelepíteni meglévő helyszíni alkalmazásokat és adatbázisokat az Azure-ba,-van – az esetekben, ahol az Azure SQL Database felügyelt példánya nem jó megoldás. Mivel nem kell a bemutató, alkalmazási és adatréteg módosítására, időt és pénzt takarít költségvetés a meglévő megoldást kell újratervezni a. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## <a name="next-steps"></a>További lépések

- Tekintse meg [az első Azure SQL Database-adatbázissal](sql-database-get-started-portal.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).
