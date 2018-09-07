---
title: SQL Server-példány migrálása az Azure SQL Database felügyelt példányába történő |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet át egy SQL Server-példányt az Azure SQL Database felügyelt példányába történő.
keywords: adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: bonova
ms.openlocfilehash: 276d36562aece7101465752528f59008e1ee7607
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050323"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Az SQL Server-példány migrálása az Azure SQL Database felügyelt példánya

Ebben a cikkben megismerkedhet az áttelepítés egy SQL Server 2005 vagy újabb verzió-példány módszerek [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md) (előzetes verzió).

Magas szintű az adatbázis-áttelepítési folyamat hasonlóan néz ki:

![Áttelepítési folyamat](./media/sql-database-managed-instance-migration/migration-process.png)

- [Felügyelt példány kompatibilitási felmérése](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Válassza ki a kapcsolat lehetőséget](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Az optimálisan méretezett felügyelt példány üzembe helyezése](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Áttelepítési módszer kiválasztása és áttelepítése](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Alkalmazások figyelése](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Önálló adatbázis egy önálló adatbázis vagy a rugalmas készlet áttelepítéséhez lásd: [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Felügyelt példány kompatibilitási felmérése

Először határozza meg, hogy-e az adatbázis-követelmények az alkalmazás kompatibilis felügyelt példány. Adja meg az egyszerű átemeléses migrálásra, a legtöbb a helyszíni SQL Servert használó meglévő alkalmazások vagy a virtuális gépek felügyelt példány szolgál. Azonban néha funkciók vagy képességeket, amelyek még nem támogatottak és a egy megkerülő megoldás megvalósítása túl magas. 

Használat [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) észleli a potenciális kompatibilitási problémák az Azure SQL Database érintő funkció. DMA még nem támogatja a felügyelt példány áttelepítési céljaként, de javasoljuk, hogy az értékelés futtatása az Azure SQL-adatbázison, és gondosan tekintse át a listában, jelentett funkció és kompatibilitási hibáit, szemben a termék dokumentációját. Tekintse meg a [Azure SQL Database egyedülálló és a felügyelt példány közötti különbségek](sql-database-features.md) ellenőrzéséhez bizonyos jelentett blokkoló problémák, amelyeket nem a felügyelt példány blockers, mert a blokkolás a legtöbb problémák való áttelepítés megakadályozza Az Azure SQL Database felügyelt példány használatával el lettek távolítva. Példány, szolgáltatások, mint az adatbázisközi lekérdezések, adatbázisközi tranzakciók belül ugyanazon, más SQL forrásokból, CLR, globális ideiglenes táblák, csatolt kiszolgáló példány szintű nézetek, a Service Broker és a hasonló érhetők el a felügyelt példányok. 

Ha egy jelentett problémák elhárítását, amelyek a rendszer nem távolítja el az Azure SQL felügyelt példánya, szüksége lehet fontolja meg egy másik lehetőség, mint például [az Azure-beli virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/). Néhány példa:

- Ha az operációs rendszer vagy a fájlrendszerben, például telepítés harmadik féltől származó és egyéni ugyanahhoz a virtuális géphez, az SQL Server-ügynökök közvetlen elérésére van szüksége.
- Ha továbbra is nem támogatott funkciók, például a FileStream a szigorú függőségi / filetable objektum, a PolyBase és a kereszt-példány tranzakció.
- Ha feltétlenül szükség maradjon az SQL Server egy adott verzióját (2012-példányhoz).
- Ha a számítási követelményeket sokkal alacsonyabb, a felügyelt példány kínál a nyilvános előzetes verzióban érhető el (1 virtuális magot, példányhoz) és adatbázis-konszolidáció nem elfogadható megoldás.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Az optimálisan méretezett felügyelt példány üzembe helyezése

Felügyelt példány személyre szabott tervezi, hogy a felhőbe helyezheti át a helyszíni számítási feladatokhoz. Azt mutatja be egy [új vásárlási modell](sql-database-service-tiers-vcore.md) , amely nagyobb rugalmasságot biztosít a számítási feladatokat az erőforrások a megfelelő szint kiválasztásával. A helyszíni világ Ön szokták méretezési ezeket a feladatokat a fizikai Processzormagok és i/o-sávszélességet. A felügyelt példány új vásárlási modell típusán alapuló virtuális maggal, vagy a "virtuális maggal," további tárterületet és i/o elérhető külön-külön. A Virtuálismag-modell egy egyszerűbb módszert és a használt felhőalapú számítási követelmények megértésére helyszíni még ma. Az új modell lehetővé teszi méretezése a cél-környezetet a felhőben.

Választhat a számítási és tárolási erőforrások üzembe helyezéskor idő, majd az alkalmazást a állásidejét, anélkül ezt követően módosítsa azt a [az Azure portal](sql-database-scale-resources.md):

![felügyelt példány méretezése](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Ismerje meg, hogyan hozhat létre a virtuális hálózati infrastruktúra és a egy felügyelt példányt, lásd: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Fontos, hogy a cél virtuális hálózatot és alhálózatot mindig összhangban [felügyelt példány a virtuális hálózat követelményei](sql-database-managed-instance-vnet-configuration.md#requirements). Bármilyen inkompatibilitás megakadályozhatja, új példányok jöjjenek létre, vagy azokkal, amelyek már létrehozott használatával.

## <a name="select-migration-method-and-migrate"></a>Áttelepítési módszer kiválasztása és áttelepítése

A felügyelt példány céljainak felhasználói esetek igénylő tömeges adatbázis áttelepítése a helyszíni vagy IaaS adatbázis megvalósításokhoz. Ha a háttérrendszere által rendszeresen használja a példányszintű és / vagy adatbázisközi funkcióit alkalmazások átemelése kell azok optimális választás. Ha ez a forgatókönyv, áthelyezheti egy teljes példánya rearchitecture nélkül az Azure-ban a megfelelő környezet az alkalmazások. 

Helyezze át az SQL Server-példányok, végezzen alapos tervezést kell:

-   Az áttelepítés összes adatbázis, kell lennie a elhelyezésű (azokat az azonos példányon futnak)
-   Az alkalmazása függ, többek között a bejelentkezések, hitelesítő adatok, SQL-ügynök feladatait és a kezelők és kiszolgálói szintű eseményindítók példányszintű objektumok áttelepítése. 

Felügyelt példány egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy delegálása, a platform rendszeres DBA tevékenységekről, azok be vannak építve. Ezért bizonyos szintű példányadatok nem kell áttelepíteni kívánt, például a karbantartási feladatok rendszeres biztonsági mentést vagy mindig a konfigurációhoz, mint [magas rendelkezésre állású](sql-database-high-availability.md) jön létre.

Felügyelt példány támogatja a következő adatbázis áttelepítési lehetőségek (ezek jelenleg az egyetlen támogatott áttelepítési módszereinek):

- Azure Database Migration Service – üzemkimaradás csaknem teljes áttelepítés
- Natív `RESTORE DATABASE FROM URL` – használja az SQL Server natív biztonsági mentések, és némi állásidővel igényel.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

A [Azure Database Migration Service (DMS)](../dms/dms-overview.md) egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes migrálást a több adatbázis-forrásokhoz, az Azure-adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és az SQL Server-adatbázisok áthelyezése az Azure-bA szükséges feladatok. A nyilvános előzetes verzióban telepítési lehetőségek között az Azure SQL Database felügyelt példány és az SQL Server egy Azure virtuális gépet. A DMS az ajánlott módszer az áttelepítés a vállalati számítási feladatokat. 

Ha az SQL Server, a helyszíni SQL Server Integration Services (SSIS) használ, a DMS áttelepítése SSIS-katalógus (SSISDB), amely tárolja az SSIS-csomagok jelenleg nem támogatja, de is kioszthatja az Azure-SSIS integrációs modul (IR) az Azure Data Factory (ADF), amely lesz Hozzon létre egy új SSISDB az Azure SQL Database/Managed Instance, majd is ismételt üzembe helyezése a csomagok, lásd: [Azure-SSIS integrációs modul létrehozása az ADF-ben](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

Ez a forgatókönyv és a konfigurációs lépéseket DMS-bővebben lásd: [Migrate a helyszíni adatbázis egy felügyelt példányra DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natív VISSZAÁLLÍTÁSI URL-címről

Natív biztonsági másolatokat (.bak-fájlokat) a helyszíni SQL Server helyreállítása vagy [virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), elérhető [Azure Storage](https://azure.microsoft.com/services/storage/), az egyik legfontosabb képességei a SQL DB felügyelt példányainak, lehetővé teszi, hogy gyorsan és könnyen offline adatbázis-migrálás. 

Az alábbi ábra magas szintű áttekintést nyújt az a folyamat:

![migrálási-folyamat](./media/sql-database-managed-instance-migration/migration-flow.png)

Az alábbi táblázatban bővebb információt a forrás SQL Server verziójától függően használható módszereket futtatja:

|Lépés|SQL-kezelő és verzió|Biztonsági mentése / visszaállítása metódus|
|---|---|---|
|Helyezze az Azure Storage biztonsági mentés|Előzetes SQL 2012 SP1 CU2|Közvetlenül az Azure storage-bA a .bak-fájl feltöltése|
||2012 SP1 CU2 – 2016|A közvetlen biztonsági mentési használata elavult [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) szintaxis|
||2016 vagy újabb verzió|A közvetlen biztonsági másolat használatával [az SAS-hitelesítő adat](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Felügyelt példány visszaállítása az Azure Storage-ból|[Állítsa vissza az URL-címet az SAS-hitelesítő adatot](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Amikor [transzparens adattitkosítással](transparent-data-encryption-azure-sql.md) védett adatbázist migrál egy felügyelt Azure SQL-példányra a natív visszaállítási megoldással, az adatbázis visszaállítása előtt migrálni kell a helyszíni vagy az IaaS SQL Server-példányról a megfelelő tanúsítványt. Részletes lépéseiért lásd: [áttelepítése TDE cert egy felügyelt példányra](sql-database-managed-instance-migrate-tde-certificate.md)
> - Rendszer-adatbázisok visszaállítása nem támogatott. Példány szolgáltatásszint-objektumokhoz (master vagy msdb-adatbázisokban tárolt) áttelepítéséhez, javasoljuk, hogy parancsfájl, és T-SQL-szkriptek a cél-példányon futnak.

A rövid útmutató, amely egy adatbázis biztonsági másolatának visszaállítása egy felügyelt példányra, SAS-hitelesítő adatok használatával, lásd: [visszaállítása biztonsági másolatból egy felügyelt példányra](sql-database-managed-instance-get-started-restore.md).

## <a name="monitor-applications"></a>Alkalmazások figyelése

Nyomon követheti az alkalmazások működését és teljesítményét az áttelepítés után. A felügyelt példány néhány módosítást csak akkor engedélyezett, ha a [adatbázis kompatibilitási szintje módosítva lett](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Az Azure SQL Database-adatbázis-migrálás eredeti kompatibilitási szintje tartja a legtöbb esetben. Ha egy felhasználói adatbázis kompatibilitási szintje 100 vagy magasabb az áttelepítés előtt, akkor változatlan marad az áttelepítés után. Ha egy felhasználói adatbázis kompatibilitási szintje 90 a frissített adatbázis, az áttelepítés előtt volt a kompatibilitási szint értéke 100, amely a felügyelt példány a legalacsonyabb támogatott kompatibilitási szint. Rendszer-adatbázisok kompatibilitási szintje 140.

A migrálás kockázatainak csökkentése érdekében módosítsa az adatbázis-kompatibilitási szint csak azután alkalmazásteljesítmény-figyelő. Használja Query Store előtt és után az adatbázis kompatibilitási szint módosítása, a számítási feladatok teljesítményére kapcsolatos információk lekérése, amint azt az optimális eszközként [teljesítmény stabilitását tartsa a frissítés során az SQL Server újabb verzióra](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Miután egy teljes körűen felügyelt platformon, igénybe az SQL Database szolgáltatás részeként automatikusan biztosított előnyöket. Például a biztonsági mentések létrehozását a felügyelt példány nincs – a szolgáltatás hajtja végre biztonsági mentés meg automatikusan. Ön már nem kell aggódnia a ütemezheti, véve, és felügyelheti a biztonsági mentések. Felügyelt példány lehetővé teszi a belül a megőrzési időszak segítségével bármely időpontra visszaállítása [mutasson az idő Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). A nyilvános előzetes verzióban rögzítik a megőrzési időtartam hét nap.
Ezenkívül nem kell aggódnia, magas rendelkezésre állás kialakítása [magas rendelkezésre állású](sql-database-high-availability.md) jön létre.

A biztonság megszilárdítása, fontolja meg néhány elérhető funkcióját:
- Az Azure Active Directory-hitelesítést az adatbázis szintjén
- Használat [speciális biztonsági funkciói](sql-database-security-overview.md) például [naplózási](sql-database-managed-instance-auditing.md), [Fenyegetésészlelés](sql-advanced-threat-protection.md), [sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), és [dinamikus Adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ) biztonságossá tétele a-példány.

## <a name="next-steps"></a>További lépések

- További információ a felügyelt példányok: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Biztonsági másolat visszaállítása tartalmazó oktatóanyagért lásd: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).
- A DMS használatával bemutató oktatóanyag áttelepítéséhez lásd: [Migrate a helyszíni adatbázis egy felügyelt példányra DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).  
