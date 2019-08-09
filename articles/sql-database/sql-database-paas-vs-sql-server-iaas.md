---
title: Választás az Azure-beli SQL-lehetőségek közül | Microsoft Docs
description: Megtudhatja, hogyan választhat az üzembe helyezési lehetőségek közül a Azure SQL Databaseon, valamint az Azure-beli virtuális gépeken található Azure SQL Database és SQL Server között
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-felhő, SQL Server a felhőben, PaaS-adatbázis, felhőalapú SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/11/2019
ms.openlocfilehash: fc0204e774e66e9304887f47377a102df1b828cb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884329"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Válassza ki a megfelelő üzembe helyezési lehetőséget az Azure SQL-ben

Az Azure-ban egy üzemeltetett infrastruktúrában (IaaS) futtatott és üzemeltetett szolgáltatásként ([Pásti](https://azure.microsoft.com/overview/what-is-paas/)) futtatott SQL Server számítási feladatok is elérhetők. A Pásti-n belül több üzembe helyezési lehetőség és szolgáltatási szint is található az egyes telepítési lehetőségeken belül. Az a kérdés, hogy meg kell-e kérdezni a Pásti vagy a IaaS közötti döntéskor, hogy szeretné-e felügyelni az adatbázist, a javítások alkalmazását, a biztonsági mentéseket, vagy a műveleteket az Azure-ba delegálni?
A választól függően a következő lehetőségek közül választhat:

- [Azure SQL Database](sql-database-technical-overview.md): Egy teljes körűen felügyelt SQL Database-motor, amely a SQL Server legújabb stabil Enterprise-kiadásán alapul. Ez egy olyan, az Azure-felhőben üzemeltetett DBaaS-szolgáltatás, amely a *platform-szolgáltatás (Pásti)* iparági kategóriába tartozik. Az SQL Database több üzembe helyezési lehetőséggel rendelkezik, amelyek mindegyike a Microsoft által birtokolt, üzemeltetett és karbantartott szabványos hardverekre és szoftverekre épül. A SQL Database használatával olyan beépített funkciókat és funkciókat használhat, amelyek kiterjedt konfigurációt igényelnek SQL Server (helyszíni vagy Azure-beli virtuális gépen) való használatkor. Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. A SQL Database olyan további szolgáltatásokat tartalmaz, amelyek nem érhetők el SQL Serverban, például beépített magas rendelkezésre állás, intelligencia és felügyelet. Azure SQL Database a következő üzembe helyezési lehetőségeket kínálja:
  
  - [Önálló adatbázis](sql-database-single-database.md) , amely egy SQL Database-kiszolgálón keresztül felügyelt erőforrás-készlettel rendelkezik. Az önálló adatbázisok a SQL Serverban [lévő tárolt adatbázisokhoz](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) hasonlítanak. Ez a beállítás az új felhőalapú alkalmazások modern alkalmazás-fejlesztésére van optimalizálva.
  - Egy [rugalmas készlet](sql-database-elastic-pool.md), amely egy SQL Database-kiszolgálón keresztül felügyelt erőforrások megosztott készletével rendelkező adatbázisok gyűjteménye. Az önálló adatbázisok egy rugalmas készletbe helyezhetők és kivehetők. Ez a beállítás a több-bérlős SaaS-alkalmazást használó új felhőalapú alkalmazások modern alkalmazás-fejlesztésére van optimalizálva.
  - [Felügyelt példány](sql-database-managed-instance.md), amely egy megosztott erőforrásokkal rendelkező rendszer-és felhasználói adatbázisok gyűjteménye. A felügyelt példányok a [Microsoft SQL Server adatbázismotor] egy példányához hasonlók, amelyek közös erőforrásokat kínálnak az adatbázisok és a további példányokra vonatkozó hatókörű funkciók számára. A felügyelt példány támogatja a helyszíni adatbázis-áttelepítést, amely minimális, adatbázis-változás nélküli. Ez a beállítás biztosítja az összes, a Azure SQL Database által kiváltott, de az SQL virtuális gépeken korábban elérhető képességeket. Ide tartozik a natív virtuális hálózat (VNet) és a közel 100%-os kompatibilitás a helyszíni SQL Serverokkal.
- Az [azure Virtual Machines SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) az *infrastruktúra-szolgáltatás (IaaS)* kategóriába tartozik, és lehetővé teszi SQL Server futtatását egy teljes körűen felügyelt virtuális gépen az Azure-felhőben. [SQL Server virtuális gépek](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) a Microsoft által birtokolt, üzemeltetett és karbantartott szabványos hardvereken is futnak. Ha SQL Servert használ egy virtuális gépen, akkor a SQL Server-lemezképben már foglalt SQL Server-licencért fizethet be, vagy egyszerűen használhatja a meglévő licenceket. Igény szerint leállíthatja vagy folytathatja a virtuális gépet. Az Azure-on futó Windows Server-vagy Linux-alapú virtuális gépek (VM-EK) SQL Server a felhőben vannak telepítve és üzemeltetve, más néven infrastruktúra-szolgáltatásként (IaaS). Az Azure Virtual Machines szolgáltatásban a SQL Server jó lehetőség a helyszíni SQL Server adatbázisok és alkalmazások áttelepítésére adatbázis-módosítás nélkül. Az SQL Server összes újabb verziója és kiadása elérhető a IaaS virtuális gépeken való telepítéshez. A SQL Database legjelentősebb különbség az, hogy SQL Server virtuális gépek teljes körű vezérlést tesznek lehetővé az adatbázismotor felett. Kiválaszthatja, hogy mikor induljon el a karbantartás/javítás, a helyreállítási modellt egyszerűen vagy tömegesen naplózva, szüneteltetheti vagy indíthatja el, ha szükséges, valamint teljes mértékben testre is szabhatja a SQL Server adatbázis-motort. Ezzel a további szabályozással a virtuális gép felügyelete is felmerül.

A fenti lehetőségek közötti fő különbségek a következő táblázatban láthatók:

| SQL Server virtuális gépen | Felügyelt példány SQL Database | Önálló adatbázis/rugalmas készlet SQL Database |
| --- | --- | --- |
|A SQL Server motor teljes körű vezérléssel rendelkezik.<br/>Akár 99,99%-os rendelkezésre állást.<br/>Teljes paritás a helyszíni SQL Server megfelelő verziójával.<br/>Javított, jól ismert adatbázismotor-verzió.<br/>Könnyű Migrálás SQL Server helyszíni környezetből.<br/>Magánhálózati IP-cím az Azure VNet-n belül.<br/>Az alkalmazást vagy szolgáltatásokat a gazdagépen helyezheti üzembe, ahol a SQL Server elhelyezve.| Nagy kompatibilitás a SQL Server helyszíni környezettel.<br/>99,99%-os rendelkezésre állás garantált.<br/>Beépített biztonsági másolatok, javítás, helyreállítás.<br/>A stabil adatbázismotor legújabb verziója.<br/>SQL Server egyszerű Migrálás.<br/>Magánhálózati IP-cím az Azure VNet-n belül.<br/>Beépített speciális intelligenciát és biztonságot.<br/>Erőforrások online változása (CPU/Storage).|A leggyakrabban használt SQL Server funkciók érhetők el.<br/>99,99%-os rendelkezésre állás garantált.<br/>Beépített biztonsági másolatok, javítás, helyreállítás.<br/>A stabil adatbázismotor legújabb verziója.<br/>A szükséges erőforrások (CPU/tárterület) egyedi adatbázisokhoz való hozzárendelésének lehetősége.<br/>Beépített speciális intelligenciát és biztonságot.<br/>Erőforrások online változása (CPU/Storage).|
|Kezelnie kell a biztonsági mentéseket és a javításokat.<br>Saját, magas rendelkezésre állású megoldást kell megvalósítani.<br/>Az erőforrások (processzor/tároló) módosítása közben állásidő van|Még mindig van néhány minimális számú SQL Server szolgáltatás, amely nem érhető el.<br/>Nincs garantált pontos karbantartási idő (de majdnem transzparens).<br/>A SQL Server verziójával való kompatibilitás csak az adatbázis-kompatibilitási szintek használatával érhető el.|Lehet, hogy a Migrálás SQL Server nehéz.<br/>Néhány SQL Server szolgáltatás nem érhető el.<br/>Nincs garantált pontos karbantartási idő (de majdnem transzparens).<br/>A SQL Server verziójával való kompatibilitás csak az adatbázis-kompatibilitási szintek használatával érhető el.<br/>A magánhálózati IP-cím nem rendelhető hozzá (a tűzfalszabályok használatával korlátozhatja a hozzáférést).|

Ismerje meg, hogy az egyes üzembe helyezési lehetőségek hogyan illeszkednek a Microsoft adatplatformba, és hogy segítséget nyújtson az üzleti igényeknek megfelelő megoldáshoz. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.

## <a name="microsofts-sql-data-platform"></a>A Microsoft SQL-adatplatformja

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását. Az Azure-beli virtuális gépeken futó SQL Server helyszíni és felhőben futó üzemelő példányok együttes használatával segít megfelelni az egyedi és sokszínű üzleti igényeiknek, ráadásul Ön az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőbeli SQL Server lehetőségek: SQL Server a IaaS vagy SaaS SQL Database-ben a felhőben.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

- SQL Server nem virtuális, fizikai gépeken
- SQL Server helyszíni virtuális gépeken (magánfelhő)
- SQL Server Azure virtuális gépeken (Microsoft nyilvános felhő)
- Azure SQL Database (Microsoft nyilvános felhő)

A következő fejezetekben megismerheti SQL Server a Microsoft nyilvános felhőben: Azure SQL Database és SQL Server Azure-beli virtuális gépeken. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

- **Azure SQL Database**

Optimalizálva, hogy csökkentse a teljes körű felügyeleti költségeket a sok adatbázis kiépítés és felügyeletének minimális szintjén. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](sql-database-automated-backups.md) kezelésével sem. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá. A [rugalmas készletek](sql-database-elastic-pool.md) támogatják az SaaS több-bérlős alkalmazások architektúráját is, beleértve a bérlők elkülönítését, valamint a költségek csökkentésének lehetőségét az erőforrások adatbázisok közötti megosztásával. [](sql-database-managed-instance.md) A felügyelt példányok támogatják a meglévő alkalmazások egyszerű áttelepítését, valamint az erőforrások adatbázisok közötti megosztását lehetővé tévő példányokra kiterjedő funkciókat.

- **Azure-beli virtuális gépeken futó SQL Server**

Meglévő alkalmazások Azure-ba való áttelepítésére, vagy a meglévő helyszíni alkalmazások felhőbe való kiterjesztésére optimalizált hibrid üzembe helyezés esetén. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

| | Önálló adatbázisok és rugalmas készletek SQL Database | Felügyelt példányok SQL Database |Azure-beli virtuális gépek SQL Server |
| --- | --- | --- |---|
| **A következőkre alkalmas:** |Új, felhőben tervezett alkalmazások, amelyek a legújabb stabil SQL Server funkciókat szeretnék használni, és időbeli korlátozásokkal rendelkeznek a fejlesztés és a marketing terén. | Új alkalmazások vagy meglévő helyszíni alkalmazások, amelyek a legújabb stabil SQL Server szolgáltatásokat szeretnék használni, és amelyek a felhőbe migrálva minimális módosításokkal.  | A felhőbe történő gyors áttelepítést igénylő meglévő alkalmazások minimális változtatásokkal vagy módosításokkal. Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. |
|  | Olyan csoportok számára, amelyeknek beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokkal rendelkező adatbázisokra van szüksége. | Ugyanaz, mint az önálló és a készletezett adatbázisok SQL Database. | A magas rendelkezésre állást, a vész-helyreállítást és a javításokat a SQL Server konfigurálhatja, finomíthatja, testreszabhatja és kezelheti. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
|  | Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni. | Ugyanaz, mint az önálló és a készletezett adatbázisok SQL Database. | A teljes rendszergazdai jogokkal rendelkező testreszabott környezetre van szüksége. |
|  | Legfeljebb 100 TB-os adatbázisok. | Akár 8 TB-ig. | SQL Server példányok száma akár 256 TB tárhellyel. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| **Kompatibilitási** | A a legtöbb helyszíni adatbázis-szintű funkciót támogatja. | Szinte minden helyszíni példány-és adatbázis-szintű funkciót támogat. | Támogatja az összes helyszíni funkciót. |
| **Erőforrások:** | A mögöttes infrastruktúra konfigurálásához és kezeléséhez nem kívánja alkalmazni az informatikai erőforrásokat, de az alkalmazási rétegre szeretne összpontosítani. | Ugyanaz, mint az önálló és a készletezett adatbázisok SQL Database. | Ha rendelkezik bizonyos informatikai erőforrásokkal a konfigurációhoz és felügyelethez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
| **Tulajdonosi költségek:** | Nincsenek hardverköltségek és alacsonyabbak a felügyeleti költségek. | Ugyanaz, mint az önálló és a készletezett adatbázisok SQL Database. | Nincsenek hardverköltségek. |
| **Az üzletmenet folytonossága** |A [beépített hibatűrési infrastruktúra képességein](sql-database-high-availability.md)kívül a Azure SQL Database olyan funkciókat biztosít, mint például az [automatikus biztonsági mentés](sql-database-automated-backups.md), az időponthoz [való visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore), a [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore), az [aktív földrajzi replikálás](sql-database-active-geo-replication.md)és [a Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) az üzletmenet folytonosságának növeléséhez. További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése). | Ugyanaz, mint a SQL Database az önálló és a készletezett adatbázisok, valamint a felhasználó által kezdeményezett, csak a másolásra szolgáló biztonsági mentések érhetők el. | Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren). |
| **Hibrid felhő:** |A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére. | [Natív virtuális hálózat megvalósítása](sql-database-managed-instance-vnet-configuration.md) és a helyszíni környezethez való kapcsolódás az Azure Express Route vagy a VPN Gateway használatával. | Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. A hibrid felhőalapú megoldásokkal kapcsolatos további információkért lásd: helyszíni Adatmegoldások [kiterjesztése a felhőre](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Előfizetőként támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx) az adatok replikálása céljából. | A felügyelt példányok esetében a replikáció előzetes verziójú szolgáltatásként támogatott. | Teljes mértékben támogatja a [SQL Server tranzakciós replikálást](https://msdn.microsoft.com/library/mt589530.aspx), Always [on rendelkezésre állási csoportokat](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), integrációs szolgáltatásokat és naplózást az adatreplikáláshoz. Emellett a hagyományos SQL Server-biztonságimásolatok is teljes körűen támogatottak. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok

Több tényező is befolyásolhatja az SQL-adatbázisok üzemeltetéséhez szükséges, hogy a Péter vagy a IaaS közül választhat:

- [](#cost) A Pásti és a IaaS beállítás is magában foglalja az alapul szolgáló infrastruktúrát és licencelést biztosító alapdíjat. A IaaS beállítással azonban további időt és erőforrásokat kell megadnia az adatbázis kezeléséhez, míg a Pástiban a jelen adminisztrációs funkciók bekerülnek a díjszabásba. A IaaS beállítás lehetővé teszi az erőforrások leállítását, miközben Ön nem használja őket a díjak csökkentése érdekében, míg a Pásti-verzió mindig fut, kivéve, ha eldobja, és újból létrehozza az erőforrásokat, amikor szükséges.
- [Adminisztráció](#administration) – a Pásti-beállítások csökkentik az adatbázis felügyeletéhez szükséges idő mennyiségét. Emellett korlátozza az egyéni adminisztrációs feladatok és a végrehajtható vagy futtatható parancsfájlok tartományát is. Például a CLR nem támogatott egyetlen vagy készletezett adatbázis esetén, de felügyelt példány esetén támogatott. Emellett a Pásti-beli üzembe helyezési lehetőségek nem támogatják a nyomkövetési jelzők használatát.
- [Szolgáltatói szerződés](#service-level-agreement-sla) – a IaaS és a Péter is magas, iparági szabványoknak megfelelő SLA-t biztosít. A Péter opció garantálja a 99,99%-os SLA-t, míg a IaaS garantálja a 99,95%-os SLA-t az infrastruktúrához, ami azt jelenti, hogy további mechanizmusokra van szükség az adatbázisok rendelkezésre állásának A magas rendelkezésre állású megoldás 99,99%-ban valósítható meg egy további SQL Server létrehozásával a virtuális gépen, és konfigurálhatja az alAlwaysOnon rendelkezésre állási csoportokat.
- [](#market) Az Azure-beli virtuális gépen SQL Server az Azure-ba való áttérés a környezet pontos egyezése, így a helyszínről az Azure SQL virtuális gépre történő áttelepítés nem különbözik, mint az adatbázisok egyik helyszíni kiszolgálóról a másikra való áthelyezése. A felügyelt példány rendkívül egyszerű áttelepítést is lehetővé tesz; Előfordulhat azonban, hogy a felügyelt példányra való Migrálás előtt végre kell térnie néhány módosításra.

A következő szakaszokban részletesen ismertetjük ezeket a tényezőket.

### <a name="cost"></a>Költség

Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a szakaszban megismerheti a számlázási és licencelési alapismereteket az Azure-ban, amelyek a következő két kapcsolódó adatbázis-beállításra vonatkoznak: SQL Database és SQL Server Azure-beli virtuális gépeken. Megismerheti az alkalmazás összköltségének kiszámításának módját is.

#### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

A **SQL Database** jelenleg szolgáltatásként van értékesítve, és számos üzembe helyezési lehetőséggel és számos szolgáltatási szinten érhető el, és az erőforrásokhoz eltérő díjszabással rendelkezik, amelyek mindegyike óránként, a szolgáltatási szinten és a kiválasztott számítási méreten alapul. . A jelenleg támogatott szolgáltatási rétegekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

- SQL Database önálló adatbázis esetében kiválaszthatja az igényeinek megfelelő szolgáltatási szintet, amely az alapszintű csomaghoz képest 5 USD/hó kezdődően.
- Létrehozhat [rugalmas készleteket](sql-database-elastic-pool.md) az erőforrások megosztásához az adatbázis-példányok között a költségek csökkentése és a használati tüskék kielégítése érdekében.
- SQL Database felügyelt példány használatával saját licenc is létrehozható. A saját licenceléssel kapcsolatos további információkért lásd: [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility/) -ban, vagy a [Azure Hybrid Benefit Calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) használatával megtudhatja, hogyan takaríthat meg **akár 40%-ot**.

Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek.

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-automated-backups.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure virtuális gépeken futó SQL Server** segítségével használhatja a platform által biztosított bármelyik SQL Server-rendszerképet (amely tartalmaz licencet) vagy saját SQL Server-licencét. A támogatott SQL Server-verziók (2008R2, 2012, 2014, 2016) és -kiadások (Developer, Express, Web, Standard, Enterprise) mindegyike elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. A virtuálisgép-mérettől vagy a SQL Server kiadástól függetlenül a SQL Server és a Windows-vagy Linux-kiszolgáló, valamint a virtuálisgép-lemezek Azure Storage-díjait is igénybe véve kell fizetnie. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha a saját SQL Server licencét az Azure-ba viszi, csak a kiszolgáló-és tárolási költségekért kell fizetnie. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása

A felhőalapú platform használatának megkezdése után az alkalmazás futtatásának költsége magában foglalja az új fejlesztési és folyamatos adminisztrációs költségek költségeit, valamint a nyilvános Cloud platform szolgáltatási költségeit.

**Az Azure SQL Database használata esetében:**

- Nagymértékben lecsökkenthető adminisztrációs költségek
- Korlátozott fejlesztési költségek az áttelepített alkalmazások esetében (felügyelt példányok)
- Szolgáltatási költségek SQL Database
- Nincs hardveres vásárlási költség

**Az Azure virtuális gépeken futó SQL Server esetében:**

- Magasabb adminisztrációs költségek
- Az áttelepített alkalmazások fejlesztési költségeire korlátozódik
- Virtuális gépek szolgáltatásának költségei
- Nincs hardveres vásárlási költség

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [Díjszabás SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- Az [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) rendszerhez készült [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Felügyelet

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. A IaaS és a Péter segítségével a Microsoft felügyeli a mögöttes infrastruktúrát, és automatikusan replikálja az összes adatát a vész-helyreállítás biztosítására, az adatbázis-szoftverek konfigurálására és frissítésére, kezeli a terheléselosztást és a transzparens feladatátvételt, ha van kiszolgálóhiba az adatközponton belül.

- A **Azure SQL Database**segítségével továbbra is felügyelheti az adatbázist, de többé nem kell kezelnie az adatbázismotor, az operációs rendszer vagy a hardver kezelését. Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimális konfigurációt és felügyeletet igényel.
- Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

### <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

**SQL Database**a Microsoft 99,99%-os rendelkezésre állási SLA-t biztosít. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/).

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Az adatbázisok magas rendelkezésre állása (HA) a virtuális gépeken belül a támogatott magas rendelkezésre állási lehetőségek egyikét kell konfigurálnia SQL Serverban, például [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

### <a name="market"></a>Az Azure-ba való áttérés ideje

**SQL Database önálló adatbázisok vagy rugalmas készletek** a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztői hatékonyság és a gyors piacra kerülési idő az új megoldások esetében kritikus fontosságú. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt.

**SQL Database felügyelt példány** nagy mértékben leegyszerűsíti a meglévő alkalmazások Azure SQL Databaseba való áttelepítését, és lehetővé teszi az áttelepített adatbázis-alkalmazások gyors üzembe helyezését az Azure-ban.

Az Azure-beli **virtuális gépeken futó SQL Server** tökéletes megoldás, ha meglévő vagy új alkalmazásai nagyméretű adatbázisokat igényelnek, vagy SQL Server vagy Windows/Linux összes szolgáltatásához hozzáférnek, és el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét. Ez akkor is megfelelő, ha a meglévő helyszíni alkalmazásokat és adatbázisokat át szeretné telepíteni az Azure-ba – olyan esetekben, amikor Azure SQL Database felügyelt példány nem megfelelő. Mivel nem kell módosítania a bemutatót, az alkalmazást és az adatréteget, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## <a name="next-steps"></a>További lépések

- Tekintse meg [az első Azure SQL-adatbázissal](sql-database-single-database-get-started.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).
- [Azonosítsa a helyszíni adatbázis megfelelő Azure SQL Database/felügyelt példányának SKU-](/sql/dma/dma-sku-recommend-sql-db/)t.
