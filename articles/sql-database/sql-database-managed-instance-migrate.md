---
title: Áttelepítés SQL Serverról felügyelt példányra
description: Megtudhatja, hogyan telepíthet át egy adatbázist SQL Server-példányról Azure SQL Database felügyelt példányra.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 802dfa7e3b2d0b9deac957662ac1e7604d085fd9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828077"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server példány áttelepítése Azure SQL Database felügyelt példányra

Ebből a cikkből megtudhatja, hogyan telepíthet át egy SQL Server 2005-es vagy újabb verziójú példányt [Azure SQL Database felügyelt példányra](sql-database-managed-instance.md). Az önálló adatbázisra vagy rugalmas készletre való áttelepítéssel kapcsolatos információkért lásd: [áttelepítés egyetlen vagy készletezett adatbázisba](sql-database-cloud-migrate.md). A más platformokról való áttelepítéssel kapcsolatos információkért lásd: az [Azure Database áttelepítési útmutatója](https://datamigration.microsoft.com/).

> [!NOTE]
> Ha gyorsan szeretné elindítani a felügyelt példányokat, érdemes lehet ezt a lapot használni a [gyors üzembe helyezési útmutatóban](sql-database-managed-instance-quickstart-guide.md) . 

Az adatbázis-áttelepítési folyamat magas szinten a következőképpen néz ki:

![áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-process.png)

- A [felügyelt példányok kompatibilitásának felmérése](#assess-managed-instance-compatibility) , ha biztosítania kell, hogy ne legyenek blokkoló problémák az áttelepítés megakadályozása érdekében.
  - Ez a lépés a [teljesítmény](#create-performance-baseline) alapkonfigurációjának létrehozását is magában foglalja a forrás-SQL Server példány erőforrás-felhasználásának meghatározásához. Erre a lépésre akkor van szükség, ha a megfelelő méretű felügyelt példányt kívánja telepíteni, és azt szeretné ellenőrizni, hogy az áttelepítés után milyen teljesítményekre van hatással.
- [Alkalmazás-kapcsolódási beállítások kiválasztása](sql-database-managed-instance-connect-app.md)
- [Helyezzen üzembe egy optimálisan méretezett felügyelt példányon](#deploy-to-an-optimally-sized-managed-instance) , ahol kiválaszthatja a felügyelt példány technikai jellemzőit (virtuális mag száma, memória mennyisége) és teljesítményszint (üzletileg kritikus, általános célú).
- [Válassza ki az áttelepítési módszert, és telepítse át](#select-migration-method-and-migrate) az adatbázisokat offline áttelepítéssel (natív biztonsági mentés/visszaállítás, adatbázis-Importálás/exportálás) vagy online Migrálás (adatáttelepítési szolgáltatás, tranzakciós replikáció) használatával.
- [Figyelje az alkalmazásokat](#monitor-applications) , és győződjön meg arról, hogy a teljesítmény várható.

> [!NOTE]
> Ha önálló adatbázist szeretne áttelepíteni egyetlen adatbázisba vagy rugalmas készletbe, tekintse meg [a SQL Server adatbázis Áttelepítését Azure SQL Databasere](sql-database-single-database-migrate.md)című témakört.

## <a name="assess-managed-instance-compatibility"></a>Felügyelt példányok kompatibilitásának felmérése

Először határozza meg, hogy a felügyelt példány kompatibilis-e az alkalmazás adatbázis-követelményeivel. A felügyelt példányok központi telepítésének lehetősége úgy lett kialakítva, hogy könnyen áthelyezhető áttelepítést biztosítson a SQL Server helyszíni vagy virtuális gépeken használó meglévő alkalmazások többsége számára. Előfordulhat azonban, hogy esetenként olyan szolgáltatásokat vagy képességeket igényel, amelyek még nem támogatottak, és a megkerülő megoldás megvalósításának díja túl magas.

A [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) használatával észlelheti az adatbázis-funkciókat érintő lehetséges kompatibilitási problémákat Azure SQL Databaseeken. A DMA még nem támogatja a felügyelt példányok áttelepítési célhelyként való használatát, de javasoljuk, hogy az értékelést Azure SQL Database, és körültekintően tekintse át a jelentett szolgáltatás paritásának és kompatibilitási problémáinak listáját a termékdokumentációban. [Azure SQL Database](sql-database-features.md) tekintse meg, hogy vannak-e olyan blokkoló hibák a felügyelt példányokban, amelyek nem blokkolók a felügyelt példányokban, mert a letiltások nagy része, hogy a rendszer a felügyelt példányok miatt el tudta távolítani a Azure SQL Database áttelepítés Ilyenek például az olyan funkciók, mint például az adatbázisok közötti lekérdezések, az azonos példányon belüli adatbázis-tranzakciók, a más SQL-forrásokhoz csatolt kiszolgálók, a CLR, a globális Temp táblák, a példány-szintű nézetek, a Service Broker és a hasonlók a felügyelt példányokban.

Ha vannak olyan jelentett blokkoló problémák, amelyeket a rendszer nem távolít el a felügyelt példányok központi telepítésével, előfordulhat, hogy egy másik lehetőséget kell figyelembe vennie, például [SQL Server az Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)szolgáltatásban. Néhány példa:

- Ha közvetlen hozzáférésre van szüksége az operációs rendszerhez vagy a fájlrendszerhez, például ha külső gyártót vagy egyéni ügynököt szeretne telepíteni ugyanarra a virtuális gépre SQL Server.
- Ha olyan funkciókkal rendelkezik, amelyek még nem támogatottak, például a FileStream/leválasztható, a bázisterület és a több példány tranzakciója.
- Ha mindenképpen a SQL Server egy adott verziójára van szüksége (például 2012).
- Ha a számítási követelmények sokkal alacsonyabbak, mint a felügyelt példányok (egy virtuális mag, például), és az adatbázis-konszolidáció nem elfogadható megoldás.

Ha feloldotta az összes azonosított áttelepítési blokkot, és folytatja az áttelepítést a felügyelt példányra, vegye figyelembe, hogy egyes módosítások befolyásolhatják a munkaterhelés teljesítményét:
- A kötelező teljes helyreállítási modell és a rendszeres automatikus biztonsági mentés ütemezése hatással lehet a számítási feladatok teljesítményére vagy a karbantartási/ETL-műveletekre, ha rendszeresen használ egyszerű/tömegesen naplózott modellt, vagy igény szerint állított le biztonsági mentést.
- Különböző kiszolgálói vagy adatbázis-szintű konfigurációk, például nyomkövetési jelzők vagy kompatibilitási szintek
- A használt új funkciók, például az átlátszó adatbázis-titkosítás (TDE) vagy az automatikus feladatátvételi csoportok befolyásolhatják a CPU-t és az IO-használatot.

A felügyelt példány garantálja a 99,99%-os rendelkezésre állást, még a kritikus helyzetekben is, így a szolgáltatások által okozott terhelés nem tiltható le. További információkért tekintse [meg a SQL Server és felügyelt példányon eltérő teljesítményt okozó kiváltó okokat](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Teljesítmény-alapterv létrehozása

Ha össze kell hasonlítani a számítási feladatok teljesítményét a felügyelt példányon a SQL Server-on futó eredeti számítási feladattal, létre kell hoznia egy, az összehasonlításhoz használandó alapkonfigurációt. 

A teljesítmény alapkonfigurációja olyan paraméterek összessége, mint például az átlagos/maximális CPU-használat, az átlagos/maximális lemez i/o-késés, az átviteli sebesség, a IOPS, az átlagos/maximális oldal élettartama, a tempdb átlagos maximális mérete. A Migrálás után hasonló vagy még jobb paramétereket szeretne biztosítani, ezért fontos a paraméterek alapértékének mérése és rögzítése. A rendszerparaméterek mellett ki kell választania a reprezentatív lekérdezéseket vagy a legfontosabb lekérdezéseket a számítási feladatban, és mérnie kell a minimális/átlagos/maximális időtartamot, a kiválasztott lekérdezések CPU-kihasználtságát. Ezek az értékek lehetővé teszik a felügyelt példányon futó számítási feladatok teljesítményének összehasonlítását a forrás SQL Server eredeti értékeire.

A SQL Server-példány méréséhez szükséges paraméterek némelyike a következő: 
- [Figyelje a CPU-használatot a SQL Server példányon](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) , és jegyezze fel az átlagos és a maximális CPU-használatot.
- [Figyelje a memória használatát a SQL Server példányon](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) , és határozza meg a különböző összetevők, például a puffer készlet, a terv gyorsítótár, az erőforráskészlet-készlet, [a memórián belüli OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)stb. által használt memória mennyiségét. Emellett meg kell találnia az oldal élettartamának várható memória-teljesítményszámláló átlag-és csúcsérték-értékét.
- Figyelje a lemez i/o-használatát a forrás SQL Server példányon a [sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) vagy a [teljesítményszámlálók](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)használatával.
- A számítási feladatok és a lekérdezési teljesítmény, illetve a SQL Server példány figyelése a dinamikus felügyeleti nézetek és a lekérdezési tárolók vizsgálatával, ha SQL Server 2016 + verzióról végez áttelepítést. Azonosítsa a számítási feladat legfontosabb lekérdezéseinek átlagos időtartamát és CPU-felhasználását, hogy összehasonlítsa őket a felügyelt példányon futó lekérdezésekkel.

> [!Note]
> Ha a számítási feladattal kapcsolatos problémát észlel SQL Server például a CPU-használatot, az állandó memóriát, a tempdb vagy a parametrization kapcsolatos problémákat, próbálja meg feloldani azokat a forrás SQL Server példányán, mielőtt megkezdené az alapkonfigurációt és az áttelepítést. Ha bármilyen új rendszerre migh a know-how-t, váratlan eredményeket okoz, és érvényteleníti a teljesítmény összehasonlítását.

Ennek a tevékenységnek az eredménye a CPU, a memória és az i/o használatának átlagos és maximális értékeit dokumentálhatja a forrásrendszer esetében, valamint az átlagos és maximális időtartamot és a CPU-használatot, valamint a számítási feladatok legjelentősebb lekérdezéseit. Ezeket az értékeket később kell használnia a felügyelt példányon a számítási feladatok teljesítményének összehasonlításához a forrás SQL Server számítási feladatának alapteljesítményéhez képest.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Üzembe helyezés egy optimális méretű felügyelt példányon

A felügyelt példány a felhőbe áthelyezni kívánt helyszíni munkaterhelésekhez van szabva. Egy [új vásárlási modellt](sql-database-service-tiers-vcore.md) vezet be, amely nagyobb rugalmasságot biztosít a számítási feladatok megfelelő szintjének kiválasztásában. A helyszíni világban valószínűleg megszokta ezeket a számítási feladatokat fizikai magok és IO-sávszélesség használatával. A felügyelt példány beszerzési modellje a virtuális magokon vagy a "virtuális mag"-on alapul, és a további tárhely és az IO külön érhető el. A virtuális mag modell a Felhőbeli számítási követelmények megismerésének egyszerű módja, és a helyszíni környezetek használata. Ez az új modell lehetővé teszi, hogy a felhőben jobb méretű legyen a célként megadott környezet. Néhány általános irányelv, amely segíthet kiválasztani a megfelelő szolgáltatási szintet és jellemzőket:
- Az alapkonfiguráció CPU-használata alapján olyan felügyelt példányt építhet ki, amely megfelel a SQL Server használt magok számának, figyelembe véve, hogy a processzor jellemzőit úgy kell méretezni, hogy megfeleljenek a [felügyelt példány telepítési helyéül szolgáló virtuális gépek jellemzőinek. ](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Az alapterv memóriahasználat alapján válassza ki [a megfelelő memóriát tartalmazó szolgáltatási szintet](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). A memória mennyisége nem választható közvetlenül, ezért ki kell választania a felügyelt példányt a megfelelő memóriával rendelkező virtuális mag (például 5,1 GB/virtuális mag a Gen5-ben). 
- A fájl alrendszer alapértékének i/o-késése alapján a általános célú (a 5ms nagyobb késése) és üzletileg kritikus szolgáltatási szintek (3 MS-nál kisebb késés) közül választhat.
- Az alapteljesítményen alapuló átviteli sebesség előre kioszthatja az adatok vagy a naplófájlok méretét, hogy a várt IO-teljesítményt kapja.

Kiválaszthatja a számítási és tárolási erőforrásokat a telepítési időpontra, majd később módosíthatja azt anélkül, hogy bevezesse az alkalmazás leállását az [Azure Portal](sql-database-scale-resources.md)használatával:

![felügyelt példányok méretezése](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

A VNet-infrastruktúra és a felügyelt példány létrehozásával kapcsolatos további információkért lásd: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Fontos, hogy a célként megadott VNet és alhálózatot mindig a [felügyelt példányok VNet követelményeinek](sql-database-managed-instance-connectivity-architecture.md#network-requirements)megfelelően tartsa. Minden inkompatibilitás megakadályozhatja, hogy új példányokat hozzon létre, vagy a már létrehozott személyeket használja. További információ a meglévő hálózatok [létrehozásáról](sql-database-managed-instance-create-vnet-subnet.md) és [konfigurálásáról](sql-database-managed-instance-configure-vnet-subnet.md) .

## <a name="select-migration-method-and-migrate"></a>Áttelepítési módszer kiválasztása és migrálása

A felügyelt példány központi telepítésének lehetősége a helyszíni vagy IaaS adatbázis-implementációk tömeges adatbázis-áttelepítését igénylő felhasználói forgatókönyveket célozza meg. Ezek optimális választás, ha a példányok szintjének és/vagy az adatbázisok közötti funkcionalitást rendszeresen használó alkalmazások hátterének megszüntetésére és átváltására van szükség. Ha ez az Ön forgatókönyve, a teljes példányt áthelyezheti egy megfelelő Azure-környezetbe anélkül, hogy újra kellene terveznie az alkalmazásokat.

Az SQL-példányok áthelyezéséhez körültekintően kell megterveznie a következőket:

- Az összes olyan adatbázis áttelepítése, amelyet közös elhelyezésű kell (ugyanazon a példányon futnak)
- Az alkalmazástól függő példány-szintű objektumok áttelepítése, beleértve a bejelentkezéseket, a hitelesítő adatokat, az SQL-ügynök feladatait és a kezelőket, valamint a kiszolgálói szintű eseményindítókat.

A felügyelt példány egy felügyelt szolgáltatás, amely lehetővé teszi, hogy a szokásos DBA-tevékenységek egy részét a beépített platformra delegálja. Ezért nem kell áttelepíteni bizonyos példány-szintű adatokat, például a rendszeres biztonsági mentéshez vagy az Always On konfigurációhoz tartozó karbantartási feladatokat, mivel a [magas rendelkezésre állás](sql-database-high-availability.md) beépített.

A felügyelt példány a következő adatbázis-áttelepítési lehetőségeket támogatja (jelenleg az egyetlen támogatott áttelepítési módszer):

- Azure Database Migration Service – az áttelepítés közel nulla állásidővel,
- Natív `RESTORE DATABASE FROM URL` – natív biztonsági másolatokat használ a SQL Serverból, és némi állásidőt igényel.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

A [Azure Database Migration Service (DMS)](../dms/dms-overview.md) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. Ez a szolgáltatás egyszerűsíti a meglévő külső fél és SQL Server adatbázisok Azure-ba való áthelyezéséhez szükséges feladatokat. A nyilvános előzetes verzióban elérhető üzembe helyezési lehetőségek közé tartoznak az Azure-beli virtuális gépek Azure SQL Database és SQL Server adatbázisai. A DMS a vállalati munkaterhelések számára ajánlott áttelepítési módszer.

Ha SQL Server Integration Servicest (SSIS) használ a helyszíni SQL Server, a DMS még nem támogatja a SSIS-csomagokat tároló SSIS-katalógus (SSISDB) áttelepítését, de Azure-SSIS Integration Runtime (IR) is kiépíthető Azure Data Factory (ADF) hozzon létre egy új SSISDB egy felügyelt példányban, majd újra üzembe helyezheti a csomagokat, lásd: [Azure-SSIS IR létrehozása az ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Ha többet szeretne megtudni erről a forgatókönyvről és a DMS konfigurációs lépéseiről, tekintse [meg a helyszíni adatbázis migrálása felügyelt példányra a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md)című témakört.  

### <a name="native-restore-from-url"></a>Natív visszaállítás az URL-címről

Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban elérhető, SQL Server helyszíni vagy [SQL Server on Virtual Machinesról](https://azure.microsoft.com/services/virtual-machines/sql-server/)származó natív biztonsági másolatok (. bak fájlok) visszaállítása a felügyelt példányok üzembe helyezési lehetőségének egyik fő funkciója, amely lehetővé teszi a gyors és egyszerű offline adatbázis migrálása.

Az alábbi ábra a folyamat magas szintű áttekintését tartalmazza:

![áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-flow.png)

Az alábbi táblázat a forrás SQL Server a futtatott verziótól függően használható módszerekkel kapcsolatos további információkat tartalmazza:

|Lépés|SQL-motor és-verzió|Biztonsági mentési/visszaállítási módszer|
|---|---|---|
|Biztonsági mentés készítése az Azure Storage-ba|Korábbi SQL 2012 SP1 CU2|A. bak fájl feltöltése közvetlenül az Azure Storage-ba|
||2012 SP1 CU2 – 2016|A közvetlen biztonsági mentés [a hitelesítő adatok](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) szintaxisával elavult|
||2016 és újabb verziók|Közvetlen biztonsági mentés [sas-hitelesítő adat](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) használatával|
|Visszaállítás az Azure Storage-ból felügyelt példányra|[Visszaállítás az URL-címről SAS-HITELESÍTő ADATOKkal](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Ha [transzparens adattitkosítás](transparent-data-encryption-azure-sql.md) által védett adatbázist felügyelt példányra telepít át natív visszaállítási lehetőséggel, a helyszíni vagy SQL Server IaaS tartozó megfelelő tanúsítványt át kell telepíteni az adatbázis-visszaállítás előtt. A részletes lépésekért lásd: [TDE-tanúsítvány áttelepítése felügyelt példányra](sql-database-managed-instance-migrate-tde-certificate.md)
> - A rendszeradatbázisok visszaállítása nem támogatott. A példány szintű objektumok (Master vagy msdb-adatbázisokban tárolt) átmigrálása érdekében javasoljuk, hogy parancsfájlokat futtasson, és a T-SQL-szkripteket futtassa a cél példányon.

Az adatbázis biztonsági másolatának egy felügyelt példányra SAS-hitelesítő adatokkal történő visszaállítását bemutató rövid útmutató a [biztonsági másolatból egy felügyelt példányra történő visszaállítással](sql-database-managed-instance-get-started-restore.md)foglalkozó témakörben olvasható.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Alkalmazások figyelése

Miután végrehajtotta a felügyelt példányra való áttelepítést, nyomon követheti a számítási feladatok viselkedését és teljesítményét. Ez a folyamat a következő tevékenységeket tartalmazza:
- [Hasonlítsa össze a felügyelt példányon futó munkaterhelés teljesítményét](#compare-performance-with-the-baseline) a [forrás SQL Serveron létrehozott](#create-performance-baseline)alapkonfigurációval.
- [A számítási feladatok teljesítményének folyamatos figyelésével](#monitor-performance) azonosíthatja a lehetséges problémákat és a fejlesztést.

### <a name="compare-performance-with-the-baseline"></a>A teljesítmény összehasonlítása az alaptervvel

A sikeres áttelepítés után elvégzendő első tevékenység a számítási feladat teljesítményének összevetése a számítási feladatok teljesítményével. Ennek a tevékenységnek a célja annak ellenőrzése, hogy a felügyelt példány munkaterhelés-teljesítménye megfelel-e az igényeinek. 

Az adatbázisok felügyelt példányra történő áttelepítése a legtöbb esetben megőrzi az adatbázis-beállításokat és az eredeti kompatibilitási szintet. Ha lehetséges, az eredeti beállítások megmaradnak, hogy csökkentse a teljesítmény csökkenésének kockázatát a forrás-SQL Server képest. Ha a felhasználói adatbázis kompatibilitási szintje 100 vagy magasabb volt az áttelepítés előtt, akkor az áttelepítés után is változatlan marad. Ha a felhasználói adatbázis kompatibilitási szintje 90 volt az áttelepítés előtt, a frissített adatbázisban a kompatibilitási szint a 100 értékre van állítva, amely a felügyelt példányok legalacsonyabb támogatott kompatibilitási szintje. A rendszeradatbázisok kompatibilitási szintje 140. Mivel a felügyelt példányra való Migrálás ténylegesen át lett telepítve a SQL Server adatbázismotor legújabb verziójára, érdemes figyelembe vennie, hogy újra kell tesztelni a számítási feladatok teljesítményét, hogy elkerülje a problémák némelyikét.

Előfeltételként ellenőrizze, hogy végrehajtotta-e a következő tevékenységeket:
- A különböző példányok, adatbázisok, temdb-beállítások és konfigurációk vizsgálatával igazíthatja a felügyelt példány beállításait a forrás SQL Server példány beállításaival. Győződjön meg arról, hogy nem módosította a beállításokat, például a kompatibilitási szinteket vagy a titkosítást az első teljesítmény-összehasonlítás futtatása előtt, vagy fogadja el azt a kockázatot, hogy az Ön által engedélyezett új funkciók némelyike bizonyos lekérdezésekre is hatással lehet. Az áttelepítési kockázatok csökkentése érdekében csak a teljesítmény figyelése után módosítsa az adatbázis kompatibilitási szintjét.
- A [tárolási ajánlott eljárásokra vonatkozó irányelvek bevezetése általános célú](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) például a jobb teljesítmény érdekében a fájlok méretének előzetes lefoglalása.
- Ismerje meg azokat a [kulcsfontosságú környezeti különbségeket, amelyek a felügyelt példányok és a SQL Server közötti teljesítménybeli különbségeket okozhatják]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) , és azonosítják azokat a kockázatokat, amelyek befolyásolhatják a teljesítményt.
- Győződjön meg arról, hogy a felügyelt példányon megtartja az engedélyezett lekérdezési tárolót és az automatikus finomhangolást. Ezek a funkciók lehetővé teszik a számítási feladatok teljesítményének mérését és a lehetséges teljesítménybeli problémák automatikus kijavítását. Megtudhatja, hogyan használhatja a Query Store-t optimális eszközként a munkaterhelés teljesítményének az adatbázis-kompatibilitási szint változása előtt és után történő beolvasásához, ahogy [azt a teljesítmény stabilitásának fenntartása az újabb SQL Server verzióra való frissítés során](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)című témakör ismerteti.
Miután előkészítette a környezetet, amely a lehető legnagyobb mértékben összehasonlítható a helyszíni környezettel, megkezdheti a számítási feladatok futtatását és a teljesítmény mérését. A mérési folyamatnak meg kell egyeznie a megadott paraméterekkel, amikor a számítási [feladatok alapteljesítményét a forrás SQL Serveron hozza létre](#create-performance-baseline).
Ennek eredményeképpen össze kell hasonlítani a teljesítménnyel kapcsolatos paramétereket az alapkonfigurációval, és meg kell határoznia a kritikus különbségeket.

> [!NOTE]
> Sok esetben nem lehet pontosan egyező teljesítményt beolvasni a felügyelt példányon és SQL Server. A felügyelt példány egy SQL Server adatbázismotor, de az infrastruktúra és a magas rendelkezésre állási konfiguráció a felügyelt példányon némileg eltérő lehet. Előfordulhat, hogy néhány lekérdezés gyorsabb, míg más lehet lassabb. Az összehasonlítás célja annak ellenőrzése, hogy a felügyelt példány terhelési teljesítménye megegyezik-e a SQL Server teljesítményével (átlagosan), és hogy vannak-e olyan kritikus lekérdezések, amelyek az eredeti teljesítménnyel nem egyező teljesítménnyel rendelkeznek.

A teljesítmény-összehasonlítás eredménye a következőket teheti:
- A felügyelt példányon a számítási feladatok teljesítményének igazítása vagy jobb, ha a számítási feladatok teljesítménye SQL Server. Ebben az esetben sikeresen megerősítette, hogy az áttelepítés sikeres volt.
- A teljesítménnyel kapcsolatos paraméterek és a számítási feladatok nagy része megfelelően működik, és bizonyos kivételeket okoz a csökkentett teljesítmény. Ebben az esetben meg kell határoznia a különbségeket és azok fontosságát. Ha vannak olyan fontos lekérdezések, amelyek csökkentett teljesítményű teljesítménnyel rendelkeznek, érdemes megvizsgálni a mögöttes SQL-csomagokat, vagy a lekérdezések néhány erőforrás-korlátot találnak. Ebben az esetben a kritikus lekérdezésekre (például a módosított kompatibilitási szintre, az örökölt kardinális kalkulátorra) vonatkozó javaslatok alkalmazhatók közvetlenül vagy tervezési útmutatók használatával, újraépíteni vagy létrehozni azokat a statisztikákat és indexeket, amelyek hatással lehetnek a csomagokra. 
- A legtöbb lekérdezés lassabb a felügyelt példányon a forrás-SQL Server képest. Ebben az esetben meg kell határozni a különbség kiváltó okait, például az [egyes erőforrások korlátozásait]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) , például az i/o-korlátokat, a memória korlátját, a példányok naplózási sebességének korlátozását stb. Ha nincs olyan erőforrás-korlát, amely a különbséget okozhatja, próbálja meg módosítani az adatbázis kompatibilitási szintjét, vagy módosítsa az adatbázis beállításait, például a örökölt kardinális becslést, és indítsa újra a tesztet. Tekintse át a felügyelt példány vagy a lekérdezési tár nézetei által megadott ajánlásokat a romlott teljesítményű lekérdezések azonosításához.

> [!IMPORTANT]
> A felügyelt példány beépített automatikus csomag-javítási funkciója alapértelmezés szerint engedélyezve van. Ez a funkció biztosítja, hogy a beillesztés során kitöltött lekérdezések a jövőben ne legyenek csökkenhetve. Győződjön meg arról, hogy ez a funkció engedélyezve van, és hogy az új beállítások módosítása előtt a régi beállításokkal elvégezte a munkaterhelést, hogy a felügyelt példány az alapteljesítményről és a csomagokról tudjon tájékozódni.

Végezze el a paraméterek módosítását, vagy frissítse a szolgáltatási szinteket az optimális konfigurációhoz, amíg meg nem éri az igényeinek megfelelő számítási feladatok teljesítményét.

### <a name="monitor-performance"></a>Teljesítmény figyelése

A felügyelt példányok számos speciális eszközt biztosítanak a figyeléshez és a hibaelhárításhoz, és ezeket érdemes használni a példány teljesítményének figyeléséhez. A következő paraméterek közül néhányat figyelnie kell:
- A példány CPU-használata határozza meg, hogy az Ön által kiépített virtuális mag száma megfelelő-e a számítási feladatokhoz.
- Page – várható élettartam a felügyelt példányon, hogy meghatározza a [szükséges további memóriát](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Várjon olyan statisztikát, mint például a `INSTANCE_LOG_GOVERNOR` vagy `PAGEIOLATCH`, amelyekről megtudhatja, hogy van-e tárolási IO-probléma, különösen általános célú szinten, ahol szükség lehet a fájlok előzetes lefoglalására a jobb i/o-teljesítmény eléréséhez.

## <a name="leverage-advanced-paas-features"></a>Speciális Pásti funkciók kihasználása

Ha egy teljes körűen felügyelt platformon van, és ellenőrizte, hogy a számítási feladatok teljesítménye megfelel a munkaterhelések teljesítményének SQL Servernek, akkor a SQL Database szolgáltatás részeként automatikusan rendelkezésre álló előnyöket kell biztosítania. 

Még ha nem végez módosításokat a felügyelt példányon az áttelepítés során, nagy eséllyel előfordulhat, hogy bizonyos új funkciók bekapcsolására akkor van szükség, amikor a példányát használja, hogy kihasználhassa a legújabb adatbázismotor-fejlesztési funkciókat. Néhány módosítás csak az [adatbázis-kompatibilitási szint módosítása](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)után engedélyezett.


Például nem kell biztonsági másolatokat létrehoznia a felügyelt példányon – a szolgáltatás automatikusan készít biztonsági másolatokat. Többé nem kell aggódnia a biztonsági mentések ütemezésével, megkezdésével és kezelésével kapcsolatban. A felügyelt példány lehetőséget nyújt arra, hogy az [időponthoz tartozó helyreállítás (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)használatával bármely időpontra vissza tudja állítani a megőrzési időszakon belül. Emellett nem kell aggódnia, hogy a magas rendelkezésre állás [magas rendelkezésre](sql-database-high-availability.md) állású legyen.

A biztonság megerősítése érdekében érdemes lehet [Azure Active Directory hitelesítést](sql-database-security-overview.md), [naplózást](sql-database-managed-instance-auditing.md), [veszélyforrások észlelését](sql-database-advanced-data-security.md), [sor szintű biztonságot](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)és [dinamikus adatmaszkolást](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) használni.

A speciális felügyeleti és biztonsági funkciók mellett a felügyelt példány olyan speciális eszközöket biztosít, amelyek segítségével [figyelheti és hangolhatja a számítási feladatokat](sql-database-monitor-tune-overview.md). Az [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) lehetővé teszi a felügyelt példányok nagy készletének figyelését és a nagy számú példány és adatbázis figyelését. A felügyelt példány [automatikus hangolásával](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) folyamatosan figyelheti az SQL-terv végrehajtási statisztikáinak teljesítményét, és automatikusan kijavíthatja az azonosított teljesítménnyel kapcsolatos problémákat.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md).
- A biztonsági másolatból történő visszaállítást tartalmazó oktatóanyagért tekintse meg [a felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)című témakört.
- A DMS használatával történő áttelepítést bemutató oktatóanyagért lásd: [a helyszíni adatbázis migrálása felügyelt példányra a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  
