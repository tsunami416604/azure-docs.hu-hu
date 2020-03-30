---
title: Áttelepítés az SQL Server kiszolgálóról a felügyelt példányra
description: Ismerje meg, hogyan telepítheti át az adatbázist az SQL Server-példányból az Azure SQL Database – Felügyelt példányba.
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
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208946"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server-példány áttelepítése az Azure SQL Database által felügyelt példányba

Ebből a cikkből megtudhatja, hogy miként lehet átmigrálni egy SQL Server 2005-ös vagy újabb verziópéldányt [az Azure SQL Database által felügyelt példányba.](sql-database-managed-instance.md) Az egyetlen adatbázisba vagy rugalmas készletbe való áttelepítésről az [Áttelepítés egyetlen vagy készletezésű adatbázisra](sql-database-cloud-migrate.md)című témakörben talál további információt. A más platformokról való áttelepítéssel kapcsolatos áttelepítési információkért lásd: [Azure Database Migration Guide](https://datamigration.microsoft.com/).

> [!NOTE]
> Ha gyorsan el szeretné indítani és ki szeretné próbálni a Felügyelt példányt, érdemes lehet a [gyorsindítási útmutatót](sql-database-managed-instance-quickstart-guide.md) megkapni a lap helyett. 

Magas szinten az adatbázis áttelepítési folyamata a következőképpen néz ki:

![áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-process.png)

- [Mérje fel a felügyelt példánykompatibilitást,](#assess-managed-instance-compatibility) ahol győződjön meg arról, hogy nincsenek olyan blokkolási problémák, amelyek megakadályozhatják az áttelepítéseket.
  - Ez a lépés a [teljesítményalapkonfiguráció](#create-performance-baseline) létrehozását is tartalmazza a forrás SQL Server-példány erőforrás-használatának meghatározásához. Erre a lépésre akkor van szükség, ha megfelelő méretű felügyelt példányt szeretne telepíteni, és ellenőrizni szeretné, hogy az áttelepítés utáni teljesítményeket ez nem érinti-e.
- [Alkalmazáscsatlakozási beállítások megadása](sql-database-managed-instance-connect-app.md)
- [Üzembe helyezése egy optimális méretű felügyelt példány,](#deploy-to-an-optimally-sized-managed-instance) ahol a felügyelt példány műszaki jellemzőit (virtuális magok száma, memória mennyisége) és teljesítményszint (üzleti legkritikusabb, általános célú) lesz.
- [Válassza ki az áttelepítési módszert, és telepítse át](#select-migration-method-and-migrate) az adatbázisokat kapcsolat nélküli áttelepítés (natív biztonsági másolat/visszaállítás, adatbázis-importálás/exportálás) vagy online áttelepítés (adatáttelepítési szolgáltatás, tranzakciós replikáció) használatával.
- [Figyelje az alkalmazásokat,](#monitor-applications) hogy megbizonyosodjon arról, hogy a várt teljesítmény.

> [!NOTE]
> Ha egy adatbázist egyetlen adatbázisba vagy rugalmas készletbe szeretne áttelepíteni, olvassa el az [SQL Server-adatbázis áttelepítése az Azure SQL Database rendszerbe című témakört.](sql-database-single-database-migrate.md)

## <a name="assess-managed-instance-compatibility"></a>Felügyelt példánykompatibilitás felmérése

Először határozza meg, hogy a felügyelt példány kompatibilis-e az alkalmazás adatbázis-követelményeivel. A felügyelt példányok üzembe helyezésének lehetősége egyszerű feloldó- és átcsoportosítási áttelepítést biztosít a meglévő alkalmazások többségének, amelyek a helyszínen vagy a virtuális gépeken használják az SQL Server t. Előfordulhat azonban, hogy olyan funkciókra vagy képességekre van szükség, amelyek még nem támogatottak, és a megoldás megvalósításának költsége túl magas.

[Az Adatáttelepítési segéd (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) segítségével észlelheti az Azure SQL Database adatbázis-funkcióit befolyásoló lehetséges kompatibilitási problémákat. A DMA még nem támogatja a felügyelt példányt áttelepítési célként, de ajánlott az Azure SQL Database-en végzett felmérést futtatni, és alaposan áttekinteni a jelentett szolgáltatásparitási és kompatibilitási problémák listáját a termékdokumentációval szemben. Lásd: [Az Azure SQL Database-szolgáltatások,](sql-database-features.md) hogy ellenőrizze vannak-e néhány jelentett blokkolási problémák, amelyek nem blokkolók a felügyelt példányban, mert a legtöbb blokkoló problémák megakadályozása az Azure SQL Database-re eltávolított felügyelt példány. Például olyan szolgáltatások, mint az adatbázisközi lekérdezések, az adatbázisközi tranzakciók ugyanazon a példányon belül, összekapcsolt kiszolgáló más SQL-forrásokhoz, CLR, globális ideiglenes táblák, példányszintű nézetek, Service Broker és hasonlók érhetők el felügyelt példányokban.

Ha vannak olyan jelentett blokkolási problémák, amelyek nem törlődnek a felügyelt példány telepítési lehetőség, előfordulhat, hogy fontolja meg egy másik lehetőség, például [az SQL Server az Azure virtuális gépeken.](https://azure.microsoft.com/services/virtual-machines/sql-server/) Néhány példa:

- Ha közvetlen hozzáférésre van szüksége az operációs rendszerhez vagy a fájlrendszerhez, például harmadik féltől származó vagy egyéni ügynökök et kell telepítenie ugyanarra a virtuális gépre az SQL Server kiszolgálóval.
- Ha szigorú függőséget gyakorol a még mindig nem támogatott szolgáltatásoktól, például a FileStream / FileTable, a PolyBase és a keresztpéldányok tranzakcióitól.
- Ha feltétlenül meg kell maradni egy adott verzióját az SQL Server (2012, például).
- Ha a számítási követelmények sokkal alacsonyabbak, hogy a felügyelt példány kínál (például egy virtuális mag) és az adatbázis-konszolidáció nem elfogadható lehetőség.

Ha feloldotta az összes azonosított áttelepítési blokkolót, és folytatja az áttelepítést a Felügyelt példányra, vegye figyelembe, hogy a módosítások némelyike hatással lehet a munkaterhelés teljesítményére:
- A kötelező teljes helyreállítási modell és a rendszeres automatikus biztonsági mentési ütemezés hatással lehet a számítási feladatok vagy a karbantartási/ETL-műveletek teljesítményére, ha rendszeresen egyszerű/tömegesen naplózott modellt vagy igény szerint leállította a biztonsági mentéseket.
- Különböző kiszolgáló- vagy adatbázisszintű konfigurációk, például nyomkövetési jelzők vagy kompatibilitási szintek
- A használt új szolgáltatások, például az átlátszó adatbázis-titkosítás (TDE) vagy az automatikus feladatátvételi csoportok hatással lehetnek a processzor- és i/o-használatra.

A felügyelt példány még a kritikus esetekben is 99,99%-os rendelkezésre állást garantál, így a szolgáltatások által okozott többletterhelés nem tiltható le. További információt [az SQL Server és a Felügyelt példány eltérő teljesítményét okozó alapvető okok](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)című témakörben talál.

### <a name="create-performance-baseline"></a>Teljesítményalapterv létrehozása

Ha össze kell hasonlítania a felügyelt példány számítási feladatának teljesítményét az SQL Serveren futó eredeti számítási feladattal, létre kell hoznia egy teljesítmény-alapkonfigurációt, amelyet az összehasonlításhoz használni fog. 

A teljesítményalapterv olyan paraméterek halmaza, mint az átlagos/maximális CPU-használat, az átlagos/maximális lemez IO késése, az átviteli sebesség, az IOPS, az átlagos/maximális oldal várható élettartama, a tempdb átlagos maximális mérete. Hasonló vagy még jobb paramétereket szeretne az áttelepítés után, ezért fontos, hogy ezekhez a paraméterekhez mérjék és rögzítsék az alapértékeket. A rendszerparaméterek mellett ki kell választania a reprezentatív lekérdezések egy készletét vagy a legfontosabb lekérdezéseket a számítási feladatokban, és mérnie kell a min/average/max időtartamot, a cpu-használatot a kiválasztott lekérdezésekhez. Ezek az értékek lehetővé tennék a felügyelt példányon futó számítási feladatok teljesítményének összehasonlítását a forrás SQL Server eredeti értékeivel.

Az SQL Server-példányon mérendő paraméterek közül néhány a következő: 
- [Figyelje a PROCESSZOR-használatot az SQL Server-példányon,](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) és rögzítse az átlagos és a csúcsprocesszor-használatot.
- [Figyelje az SQL Server-példány memóriahasználatát,](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) és határozza meg a különböző összetevők, például a pufferkészlet, a tervgyorsítótár, az oszloptároló készlet, [a memórián belüli OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)stb. Ezenkívül meg kell találnia az oldal várható memória teljesítményszámlálójának átlagos és csúcsértékeit.
- A lemez io-használatának figyelése a forrás SQL Server-példányon a [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) nézet vagy [teljesítményszámlálók](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)használatával.
- Figyelje a munkaterhelést és a lekérdezési teljesítményt vagy az SQL Server-példányt a dinamikus felügyeleti nézetek vagy a Query Store vizsgálatával, ha sql Server 2016+ verzióról telepít át. Azonosítsa a számítási feladatok legfontosabb lekérdezéseinek átlagos időtartamát és processzorhasználatát, hogy összehasonlíthassa őket a felügyelt példányon futó lekérdezésekkel.

> [!Note]
> Ha bármilyen problémát észlel az SQL Server munkaterhelésével kapcsolatban, például magas processzorhasználatot, állandó memórianyomást, tempdb-t vagy paraméterezési problémákat, az alapkonfiguráció és az áttelepítés előtt próbálja meg megoldani őket a forrás SQL Server-példányon. A know-problémák áttelepítése bármely új rendszerirányítóba váratlan eredményeket eredményez, és érvényteleníti a teljesítmény-összehasonlítást.

Ennek a tevékenységnek az eredményeként dokumentálnia kell a processzor-, memória- és i/o-használat átlagos és csúcsértékeit a forrásrendszeren, valamint a számítási feladatok domináns és legkritikusabb lekérdezéseinek átlagos és maximális időtartamát és processzorhasználatát. Ezeket az értékeket később a felügyelt példány számítási feladatának és a forrás SQL Server számítási feladatának alapteljesítményének összehasonlításához használja.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Üzembe helyezés egy optimális méretű felügyelt példányra

A felügyelt példány a helyszíni számítási feladatokhoz van szabva, amelyek a felhőbe kívánnak áthelyezni. Bevezet egy [új vásárlási modellt,](sql-database-service-tiers-vcore.md) amely nagyobb rugalmasságot biztosít a számítási feladatokmegfelelő erőforrásainak kiválasztásában. A helyszíni világban valószínűleg megszokta, hogy ezeket a számítási feladatokat fizikai magok és i/o-sávszélesség használatával méretezheti. A felügyelt példány vásárlási modellje virtuális magokon vagy "virtuális magokon" alapul, további tárhellyel és i/o-val külön érhető el. A virtuálismag-modell egy egyszerűbb módja annak, hogy a számítási követelmények a felhőben, szemben azzal, amit a helyszíni ma használ. Ez az új modell lehetővé teszi, hogy megfelelő méretű a célkörnyezet a felhőben. Néhány általános irányelv, amely segíthet kiválasztani a megfelelő szolgáltatási szintet és jellemzőket, itt ismertet:
- Az alapprocesszor-használat alapján olyan felügyelt példányt hozhat ki, amely megegyezik az SQL Server en használt magok számával, és szem előtt kell tartania, hogy előfordulhat, hogy a PROCESSZOR-jellemzőket úgy kell méretezni, hogy megfeleljenek [a felügyelt példány telepítésének helyében lévő virtuális gép jellemzőinek.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)
- Az alapmemória-használat alapján válassza ki [a megfelelő memóriával rendelkező szolgáltatási szintet.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics) A memória mennyisége nem választható ki közvetlenül, ezért ki kell választania a felügyelt példányt az egyező memóriával rendelkező virtuális magok mennyiségével (például 5,1 GB/virtuális mag a Gen5-ben). 
- A fájlalrendszer eredeti IO-késése alapján válasszon az általános cél (5 ms-nál nagyobb késleltetés) és az üzleti legkritikusabb szolgáltatásszintek (3 ms-nál kisebb késés) között.
- Az alapkonfigurációi átviteli teljesítmény alapján az adatok vagy a naplófájlok mérete a várt I/O-teljesítmény lefoglalása.

Kiválaszthatja a számítási és tárolási erőforrásokat a telepítés időpontjában, majd később módosíthatja anélkül, hogy az [Azure Portalon](sql-database-scale-resources.md)állásidőt vezetne be az alkalmazáshoz:

![felügyelt példány méretezése](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

A virtuális hálózat infrastruktúrájának és egy felügyelt példánynak a létrehozásáról a [Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben olvashat.

> [!IMPORTANT]
> Fontos, hogy a célvirtuális hálózat és az alhálózat mindig a [felügyelt példány virtuális hálózat követelményeinek](sql-database-managed-instance-connectivity-architecture.md#network-requirements)megfelelően maradjon. Bármilyen inkompatibilitás megakadályozhatja, hogy új példányokat hozzon létre, vagy a már létrehozott példányokat használja. További információ az [új hálózatok létrehozásáról](sql-database-managed-instance-create-vnet-subnet.md) és [konfigurálásáról.](sql-database-managed-instance-configure-vnet-subnet.md)

## <a name="select-migration-method-and-migrate"></a>Az áttelepítési módszer kiválasztása és áttelepítése

A felügyelt példány telepítési beállítás olyan felhasználói forgatókönyveket céloz meg, amelyek tömeges adatbázis-áttelepítést igényelnek a helyszíni vagy az IaaS-adatbázis-megvalósításokból. Ezek az optimális választás, ha meg kell emelni, és shift a hátsó végén az alkalmazások, amelyek rendszeresen használják példány szint és / vagy adatbázisközi funkciókat. Ha ez a forgatókönyv, áthelyezhet egy teljes példányt egy megfelelő környezetbe az Azure-ban anélkül, hogy újra kell dolgoznia az alkalmazásokat.

Az SQL-példányok áthelyezéséhez gondosan meg kell terveznie a következőket:

- Az összes olyan adatbázis áttelepítése, amelyet össze kell helyezni (ugyanazon a példányon futó adatbázisok)
- Az alkalmazás által függő példányszintű objektumok áttelepítése, beleértve a bejelentkezéseket, a hitelesítő adatokat, az SQL-ügynök-feladatokat és -operátorokat, valamint a kiszolgálószintű eseményindítókat.

A felügyelt példány egy felügyelt szolgáltatás, amely lehetővé teszi, hogy a rendszeres DBA-tevékenységek egy részét a beépített platformra delegálja. Ezért néhány példányszintű adatok nem kell áttelepíteni, például a karbantartási feladatok rendszeres biztonsági mentések vagy mindig a konfiguráció, a [magas rendelkezésre állás](sql-database-high-availability.md) be van építve.

A felügyelt példány a következő adatbázis-áttelepítési lehetőségeket támogatja (jelenleg ezek az egyetlen támogatott áttelepítési módszerek):

- Azure-adatbázis-áttelepítési szolgáltatás – áttelepítés közel nulla állásidővel,
- Natív `RESTORE DATABASE FROM URL` – natív biztonsági mentést használ az SQL Serverből, és némi állásidőt igényel.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Az [Azure Database Migration Service (DMS)](../dms/dms-overview.md) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes áttelepítést több adatbázis-forrásból az Azure Data-platformokra minimális állásidővel. Ez a szolgáltatás egyszerűsíti a meglévő külső és SQL Server-adatbázisok Azure-ba való áthelyezéséhez szükséges feladatokat. A nyilvános előzetes verzióban elérhető telepítési lehetőségek közé tartoznak az Azure SQL Database és az Azure virtuális gépekSQL Server-adatbázisai. A DMS az ajánlott áttelepítési módszer a vállalati munkaterhelésekhez.

Ha az SQL Server Integration Services (SSIS) szolgáltatást a helyszínen használja az SQL Server kiszolgálón, a DMS még nem támogatja az SSIS-csomagokat tároló SSIS-katalógus (SSISDB) áttelepítését, de az Azure-SSIS Integration Runtime (IR) alkalmazást az Azure Data Factoryban (ADF) építheti ki, amely egy felügyelt példányban új SSISDB-t hoz létre, majd újratelepítheti a csomagokat, [lásd: Create Azure-SSIS IRIS IR ADF in ADF.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

Ha többet szeretne tudni erről a forgatókönyvről és a DMS [konfigurációs lépéseiről, olvassa el A helyszíni adatbázis áttelepítése a felügyelt példányba a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natív VISSZAÁLLÍTÁS URL-címből

Az SQL Server helyszíni vagy az [SQL Server virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/)lévő natív biztonsági mentések (.bak fájlok) visszaállítása az Azure [Storage](https://azure.microsoft.com/services/storage/)szolgáltatásban elérhető, a felügyelt példány telepítési beállításának egyik kulcsfontosságú képessége, amely lehetővé teszi a gyors és egyszerű offline adatbázis-áttelepítést.

Az alábbi ábra magas szintű áttekintést nyújt a folyamatról:

![migrációs áramlás](./media/sql-database-managed-instance-migration/migration-flow.png)

Az alábbi táblázat a futtatott SQL Server forrásverziójától függően használható módszerekkel kapcsolatos további információkat tartalmaz:

|Lépés|SQL Motor és verzió|Biztonsági mentés/ visszaállítás módszer|
|---|---|---|
|Biztonsági mentés az Azure Storage-ba|Előzetes SQL 2012 SP1 CU2|.bak fájl feltöltése közvetlenül az Azure storage-ba|
||2012 SP1 CU2 - 2016|Közvetlen biztonsági mentés elavult [hitelesítő adatokkal](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) szintaxissal|
||2016 és újabb|Közvetlen biztonsági mentés [A SAS HITELESÍTŐ ADATOKKAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Visszaállítás az Azure storage-ból a felügyelt példányra|[VISSZAÁLLÍTÁS URL-CÍMBŐL SAS-hitelesítő adatokkal](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Az [Átlátszó adattitkosítással](transparent-data-encryption-azure-sql.md) védett adatbázis natív visszaállítási beállítással felügyelt példányba történő áttelepítésekor a helyszíni vagy Az IaaS SQL Server megfelelő tanúsítványát át kell telepíteni az adatbázis-visszaállítás előtt. Részletes lépések: [TDE tanúsítvány áttelepítése felügyelt példányba](sql-database-managed-instance-migrate-tde-certificate.md)
> - A rendszeradatbázisok visszaállítása nem támogatott. A példányszintű objektumok (fő- vagy msdb-adatbázisokban tárolt) áttelepítéséhez javasoljuk, hogy parancsfájlálja ki őket, és futtassa a T-SQL-parancsfájlokat a célpéldányon.

Az adatbázis biztonsági mentésének SAS-hitelesítő adatokkal történő visszaállításáról a [Visszaállítás felügyelt példányba történő visszaállításáról a Visszaállítás felügyelt példányra témakörben](sql-database-managed-instance-get-started-restore.md)található.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Alkalmazások figyelése

Miután befejezte az áttelepítésfelügyelt példányra, nyomon kell követnie az alkalmazás viselkedését és teljesítményét a számítási feladatok. Ez a folyamat a következő tevékenységeket foglalja magában:
- [Hasonlítsa össze a felügyelt példányon futó számítási feladatok teljesítményét](#compare-performance-with-the-baseline) a forrás SQL Server [kiszolgálón létrehozott teljesítményalapkonfigurációval.](#create-performance-baseline)
- Folyamatosan [figyelje a munkaterhelés teljesítményét a](#monitor-performance) lehetséges problémák és fejlesztés okainak azonosítása érdekében.

### <a name="compare-performance-with-the-baseline"></a>Teljesítmény összehasonlítása az alapértékkel

Az első tevékenység, amelyet a sikeres áttelepítés után azonnal el kell vinnie, a számítási feladat és az alapszintű számítási feladatok teljesítményének összehasonlítása. A tevékenység célja annak ellenőrzése, hogy a felügyelt példány számítási feladatának teljesítménye megfelel-e az igényeinek. 

A felügyelt példányba való adatbázis-áttelepítés az esetek többségében megőrzi az adatbázis-beállításokat és annak eredeti kompatibilitási szintjét. Az eredeti beállítások lehetőség szerint megmaradnak, hogy csökkentsék a teljesítményromlás kockázatát a forrás SQL Serverkiszolgálóhoz képest. Ha egy felhasználói adatbázis kompatibilitási szintje az áttelepítés előtt 100 vagy magasabb volt, akkor az áttelepítés után is változatlan marad. Ha egy felhasználói adatbázis kompatibilitási szintje 90 volt az áttelepítés előtt, a frissített adatbázisban a kompatibilitási szint 100-ra van állítva, ami a felügyelt példány legalacsonyabb támogatott kompatibilitási szintje. A rendszeradatbázisok kompatibilitási szintje 140. Mivel a felügyelt példányra való áttelepítés valójában az SQL Server Database Engine legújabb verziójára való áttérést eredményezi, vegye figyelembe, hogy a meglepő teljesítményproblémák elkerülése érdekében újra meg kell vizsgálnia a számítási feladatok teljesítményét.

Előfeltételként győződjön meg arról, hogy elvégezte a következő tevékenységeket:
- Igazítsa a felügyelt példány beállításait a forrás SQL Server-példány beállításaihoz különböző példányok, adatbázis, temdb beállítások és konfigurációk vizsgálatával. Győződjön meg arról, hogy az első teljesítmény-összehasonlítás futtatása előtt nem módosította a beállításokat, például a kompatibilitási szinteket vagy a titkosítást, vagy fogadja el annak kockázatát, hogy az engedélyezett új szolgáltatások némelyike hatással lehet bizonyos lekérdezésekre. Az áttelepítési kockázatok csökkentése érdekében csak a teljesítményfigyelés után módosítsa az adatbázis kompatibilitási szintjét.
- Valósítsa meg [a tárolási gyakorlati tanácsok általános célú,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) például a fájlok méretének előzetes lefoglalása a jobb teljesítmény érdekében.
- Ismerje meg azokat a [legfontosabb környezeti különbségeket, amelyek a felügyelt példány és az SQL Server közötti teljesítménybeli különbségeket okozhatják,]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) és azonosíthatja a teljesítményt befolyásoló kockázatokat.
- Győződjön meg arról, hogy a felügyelt példányon megtartja az engedélyezett Lekérdezéstárolót és az Automatikus hangolást. Ezek a funkciók lehetővé teszik a számítási feladatok teljesítményének mérését és a lehetséges teljesítményproblémák automatikus javítását. Megtudhatja, hogy miként használhatja a Query Store-t optimális eszközként az adatbázis-kompatibilitási szint módosítása előtti és utáni számítási feladatok teljesítményével kapcsolatos információk megszerzéséhez, [ahogy azt az Újabb SQL Server verzióra való frissítés során a teljesítmény stabilitásának megtartása](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)című részben ismerteti.
Miután előkészítette a környezetet, amely a lehető legnagyobb mértékben összehasonlítható a helyszíni környezetben, megkezdheti a számítási feladatok futtatását és a teljesítmény mérését. A mérési folyamatnak ugyanazokat a paramétereket kell tartalmaznia, amelyeket a [számítási feladatok alapszintű teljesítményének létrehozásakor mért a forrás SQL Server kiszolgálón.](#create-performance-baseline)
Ennek eredményeképpen össze kell hasonlítania a teljesítményparamétereket az alaptervvel, és azonosítania kell a kritikus különbségeket.

> [!NOTE]
> Sok esetben nem lenne képes pontosan egyező teljesítményt kapni a felügyelt példány és az SQL Server. A felügyelt példány egy SQL Server adatbázis-motor, de az infrastruktúra és a felügyelt példány magas rendelkezésre állású konfigurációja némi különbséget eredményezhet. Előfordulhat, hogy egyes lekérdezések gyorsabbak, míg mások lassabbak lehetnek. Az összehasonlítás célja annak ellenőrzése, hogy a felügyelt példány ban a számítási feladatok teljesítménye megegyezik-e az SQL Server teljesítményével (átlagosan), és azonosítsa, hogy vannak-e olyan kritikus lekérdezések a teljesítményhez, amelyek nem felelnek meg az eredeti teljesítménynek.

A teljesítmény-összehasonlítás eredménye a következő lehet:
- A felügyelt példány számítási feladatának teljesítménye igazodik, vagy jobb, hogy a számítási feladatok teljesítménye az SQL Server. Ebben az esetben sikeresen megerősítette, hogy az áttelepítés sikeres.
- A teljesítményparaméterek és a számítási feladatokban lévő lekérdezések többsége jól működik, néhány kivételtől eltekintve a leromlott teljesítménytől. Ebben az esetben meg kell határoznia a különbségeket és azok fontosságát. Ha vannak olyan fontos lekérdezések, amelyek teljesítménye lecsökkent, meg kell vizsgálnia az alapul szolgáló SQL-tervek megváltozott, vagy a lekérdezések elérik az egyes erőforráskorlátokat. Ebben az esetben a kockázatcsökkentés lehet, hogy a kritikus lekérdezésekre (például a módosított kompatibilitási szintre, az örökölt számosságbecslésre) néhány tippet alkalmaz, akár közvetlenül, akár tervsegédvonalak használatával, újraépítheti vagy létrehozhat ja a terveket befolyásoló statisztikákat és indexeket. 
- A legtöbb lekérdezés lassabb a felügyelt példányon a forrás SQL Serverhez képest. Ebben az esetben próbálja meg azonosítani a különbség kiváltó okait, például [bizonyos erőforrás-korlátok elérését,]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) például az I/o-korlátokat, a memóriakorlátot, a példány naplósebesség-korlátját stb. Ha nincsenek olyan erőforráskorlátok, amelyek a különbséget okozhatnák, próbálja meg módosítani az adatbázis kompatibilitási szintjét, vagy módosítsa az adatbázis beállításait, például az örökölt számosság becslését, és indítsa újra a tesztet. Tekintse át a felügyelt példány vagy a lekérdezéstároló nézetek által biztosított javaslatokat a teljesítményre visszalépést okozó lekérdezések azonosításához.

> [!IMPORTANT]
> A felügyelt példány beépített automatikus tervjavítási szolgáltatással rendelkezik, amely alapértelmezés szerint engedélyezve van. Ez a funkció biztosítja, hogy a beillesztésben jól működött lekérdezések a jövőben ne romoljanak. Győződjön meg arról, hogy ez a szolgáltatás engedélyezve van, és hogy a számítási feladatok végrehajtása elég hosszú a régi beállításokat, mielőtt az új beállításokat annak érdekében, hogy a felügyelt példány megismerheti az alapteljesítmény és a tervek.

Módosítsa a paramétereket, vagy frissítse a szolgáltatási szinteket, hogy az optimális konfigurációhoz közelítsen, amíg meg nem kapja az igényeinek megfelelő számítási feladatok teljesítményét.

### <a name="monitor-performance"></a>Teljesítmény figyelése

A Felügyelt példány számos speciális eszközt biztosít a figyeléshez és a hibaelhárításhoz, és ezeket kell használnia a példány teljesítményének figyelésére. Néhány paraméter, amelyet a következő paramétereket kell figyelnie:
- A processzorhasználat a példányon annak meghatározására, hogy a kiosztott virtuális magok száma a megfelelő a számítási feladatoknak.
- A felügyelt példány várható élettartama alapján meg [állapíthatja meg, hogy szüksége van-e további memóriára.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)
- Várjon statisztikák, vagy `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` hogy meg fogja mondani, hogy van-e tárolási IO-problémák, különösen az általános célú réteg, ahol szükség lehet előre lefoglalni a fájlokat, hogy jobb I/O-teljesítmény.

## <a name="leverage-advanced-paas-features"></a>A Fejlett PaaS-funkciók kihasználása

Miután egy teljes körűen felügyelt platformon, és ellenőrizte, hogy a számítási feladatok teljesítménye megfelel önnek SQL Server számítási feladatok teljesítményét, kihasználni az okat, amelyek automatikusan az SQL Database szolgáltatás részeként biztosított. 

Még akkor is, ha nem hajt végre néhány módosítást a felügyelt példányban az áttelepítés során, nagy az esélye annak, hogy a példány működtetése közben bekapcsolja az új funkciók némelyikét, hogy kihasználja az adatbázismotor legújabb fejlesztéseit. Egyes módosítások csak akkor engedélyezettek, ha az [adatbázis kompatibilitási szintje megváltozott](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Például nem kell biztonsági másolatokat létrehoznia a felügyelt példányon – a szolgáltatás automatikusan biztonsági mentéseket hajt végre. Többé nem kell aggódnia a biztonsági mentések ütemezése, elvétele és kezelése miatt. A felügyelt példány lehetővé teszi, hogy a Point [in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)használatával a megőrzési időszakon belül bármely időpontra visszaállítson. Emellett nem kell aggódnia a magas rendelkezésre állás beállítása miatt, mivel a [magas rendelkezésre állás](sql-database-high-availability.md) be van építve.

A biztonság megerősítése érdekében fontolja meg [az Azure Active Directory hitelesítés,](sql-database-security-overview.md) [naplózás,](sql-database-managed-instance-auditing.md) [fenyegetésészlelés,](sql-database-advanced-data-security.md) [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)és dinamikus [adatmaszkolás használatát.](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

A speciális felügyeleti és biztonsági funkciók mellett a Felügyelt példány speciális eszközöket is biztosít, amelyek segítségével [figyelheti és finomhangolhatja a munkaterhelést.](sql-database-monitor-tune-overview.md) [Az Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) lehetővé teszi a felügyelt példányok nagy készletének figyelését, és számos példány és adatbázis figyelésének központosítását. A felügyelt példány [automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) folyamatosan figyeli az SQL-terv végrehajtási statisztikáinak teljesítményét, és automatikusan kijavítja az azonosított teljesítményproblémákat.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokról a [Mi a felügyelt példány?](sql-database-managed-instance.md)
- A biztonsági másolatból származó visszaállítást tartalmazó oktatóanyagról a [Felügyelt példány létrehozása című](sql-database-managed-instance-get-started.md)témakörben található.
- Az áttelepítésT A DMS használatával bemutató oktatóanyagról [a Helyszíni adatbázis áttelepítése a Felügyelt példányba a DMS használatával (Áttelepítés) témakörben](../dms/tutorial-sql-server-to-managed-instance.md)található.  
