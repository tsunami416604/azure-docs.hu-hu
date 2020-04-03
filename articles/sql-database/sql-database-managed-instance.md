---
title: SQL felügyelt példány – áttekintés
description: Ez a cikk az Azure SQL Database felügyelt példányát ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 06242af6cb00e3adebbc80da722898fb8e348e36
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585358"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Mi az Azure SQL Database felügyelt példánya?

A felügyelt példány az Azure SQL Database új telepítési lehetősége, amely közel 100%-os kompatibilitást biztosít a legújabb SQL Server helyszíni (Enterprise Edition) adatbázis-motorral, natív [virtuális hálózati (VNet)](../virtual-network/virtual-networks-overview.md) megvalósítást biztosítva, amely a gyakori biztonsági problémákat kezeli, és egy [olyan üzleti modellt,](https://azure.microsoft.com/pricing/details/sql-database/) amely kedvező a helyszíni SQL Server-ügyfelek számára. A felügyelt példány telepítési modellje lehetővé teszi a meglévő SQL Server-ügyfelek számára, hogy minimális alkalmazás- és adatbázis-módosításokkal emeljék fel és helyezzék át a helyszíni alkalmazásaikat a felhőbe. Ugyanakkor a felügyelt példány telepítési lehetősége megőrzi az összes PaaS-képességet (automatikus javítás és verziófrissítések, [automatikus biztonsági mentések,](sql-database-automated-backups.md) [magas rendelkezésre állás),](sql-database-high-availability.md) ami drasztikusan csökkenti a felügyeleti terhelést és a tco-t.

> [!IMPORTANT]
> Azon régiók listáját, ahol a felügyelt példány telepítési lehetősége jelenleg elérhető, tekintse meg a [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions).

Az alábbi ábra a felügyelt példányok fő jellemzőit ismerteti:

![legfontosabb funkciók](./media/sql-database-managed-instance/key-features.png)

A felügyelt példány üzembe helyezési modellje olyan ügyfelek számára készült, akik nagyszámú alkalmazást szeretnének áttelepíteni a helyszíni vagy IaaS, saját tervezésű vagy ISV által biztosított környezetből a teljes körűen felügyelt PaaS felhőkörnyezetbe, a lehető legalacsonyabb áttelepítési erőfeszítéssel. Az Azure-ban teljesen automatizált [adatáttelepítési szolgáltatást (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) használva az ügyfelek felemelhetik és áthelyezhetik a helyszíni SQL Server kiszolgálót egy olyan felügyelt példányra, amely kompatibilitást biztosít az SQL Server helyszíni használatával, és teljes elkülönítést biztosít az ügyfélpéldányok natív virtuális hálózat támogatással.  A Frissítési Garanciával a meglévő licenceket kedvezményes díjakra cserélheti egy felügyelt példányon az [Azure Hybrid Benefit for SQL Server használatával.](https://azure.microsoft.com/pricing/hybrid-benefit/)  A felügyelt példány a legjobb áttelepítési cél a felhőben a magas biztonságot és gazdag programozhatósági felületet igénylő SQL Server-példányok számára.

A felügyelt példánytelepítési beállítás célja közel 100%-os kompatibilitást biztosít a felület legújabb helyszíni SQL Server verziójával egy szakaszos kiadási terven keresztül.

Az Azure SQL Database telepítési beállításai közül: egyetlen adatbázis, készletezésű adatbázis és felügyelt példány, valamint a virtuális gépen üzemeltetett SQL Server közötti döntésről [megtudhatja, hogyan választhatja ki az SQL Server megfelelő verzióját az Azure-ban.](sql-database-paas-vs-sql-server-iaas.md)

## <a name="key-features-and-capabilities"></a>Főbb jellemzők és képességek

A felügyelt példány egyesíti az Azure SQL Database és az SQL Server Database Engine rendszerben elérhető legjobb szolgáltatásokat.

> [!IMPORTANT]
> A felügyelt példány az SQL Server legújabb verziójának összes szolgáltatásával fut, beleértve az online műveleteket, az automatikus tervjavításokat és a vállalati teljesítmény egyéb javításait. A rendelkezésre álló szolgáltatások összehasonlítása a [Szolgáltatás összehasonlítása: Azure SQL Database versus SQL Server](sql-database-features.md).

| **PaaS előnyök** | **Az üzletmenet folytonossága** |
| --- | --- |
|Nincs hardverbeszerzés és -kezelés <br>Nincs felügyeleti terhelés az alapul szolgáló infrastruktúra kezeléséhez <br>Gyors kiépítés és szolgáltatásméretezés <br>Automatikus javítás és verziófrissítés <br>Integráció más PaaS-adatszolgáltatásokkal |99,99%-os előidők SLA  <br>Beépített, [magas rendelkezésre állású](sql-database-high-availability.md) <br>Automatikus biztonsági [másolatokkal](sql-database-automated-backups.md) védett adatok <br>Ügyfél által konfigurálható biztonsági mentési megőrzési időszak <br>Felhasználó által kezdeményezett [biztonsági mentések](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időbeli adatbázis-visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Felügyelet**|
|Elkülönített környezet ([virtuális hálózat integrációja](sql-database-managed-instance-connectivity-architecture.md), egybérlős szolgáltatás, dedikált számítási és tárolási) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-hitelesítés](sql-database-aad-authentication.md), egyszeri bejelentkezés támogatása <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-kiszolgálói egyszerű (bejelentkezési adatok)</a>  <br>Az Azure SQL-adatbázissal megegyező megfelelőségi szabványoknak felel meg <br>[SQL naplózás](sql-database-managed-instance-auditing.md) <br>[Fejlett fenyegetésvédelem](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager API a szolgáltatáskiépítés és -méretezés automatizálásához <br>Az Azure Portal funkciói a manuális szolgáltatáskiépítéshez és -méretezéshez <br>Data Migration Service

> [!IMPORTANT]
> Az Azure SQL Database (az összes üzembe helyezési lehetőség) számos megfelelőségi szabványnak megfelelően lett hitelesítve. További információt a [Microsoft Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol megtalálhatja az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját.

A felügyelt példányok főbb jellemzőit az alábbi táblázat mutatja:

|Szolgáltatás | Leírás|
|---|---|
| SQL Server verzió /build | SQL Server adatbázis-kezelő motor (legújabb stabil) |
| Felügyelt automatikus biztonsági mentések | Igen |
| Beépített példány- és adatbázis-figyelési és metrikák | Igen |
| Automatikus szoftverjavítás | Igen |
| A Database Engine legújabb funkciói | Igen |
| Adatfájlok (ROWS) száma az adatbázisonként | Többszörös |
| Naplófájlok (LOG) száma adatbázisonként | 1 |
| VNet – Az Azure Resource Manager központi telepítése | Igen |
| VNet – Klasszikus telepítési modell | Nem |
| Portál támogatása | Igen|
| Beépített integrációs szolgáltatás (SSIS) | Nem - Az SSIS az [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) része |
| Beépített elemző szolgáltatás (SSAS) | Nem - Az SSAS különálló [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Beépített jelentéskészítő szolgáltatás (SSRS) | Nem – használja a [Power BI-s lapszámozott jelentéseket,](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) vagy az SSRS-t az Azure virtuális gépen. Bár a felügyelt példány nem tudja futtatni az SSRS szolgáltatást, sql server hitelesítéssel üzemeltethet SSRS 2019 katalógus-adatbázisokat egy külső jelentéskészítő kiszolgálóhoz. |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A felügyelt példányok [virtuálismag-alapú vásárlási modellje](sql-database-service-tiers-vcore.md) rugalmasságot, vezérlést, átláthatóságot és egyszerű módot biztosít a helyszíni számítási feladatok követelményeinek a felhőbe történő lefordítására. Ez a modell lehetővé teszi a számítási, memória és tárhely módosítását a számítási feladatok igényei alapján. A virtuálismag-modell akár 55 százalékos megtakarítást is elhahoz az [Azure Hybrid Benefit for SQL Server szolgáltatással.](https://azure.microsoft.com/pricing/hybrid-benefit/)

A virtuálismag-modellben a hardvergenerációk közül választhat.

- **Gen4 (1988)** A logikai cpu-k Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokon, csatlakoztatott SSD-n, fizikai magokon, magonként 7 GB RAM RAM-on és 8 és 24 virtuális mag közötti számítási méreteken alapulnak.
- **Gen5 (1998)** A logikai cpu-k Intel E5-2673 v4 (Broadwell) 2,3 GHz-es és Intel SP-8160 (Skylake) processzorokon, gyors NVMe SSD-n, hiperszálas logikai magon és 4 és 80 mag közötti számítási méreteken alapulnak.

További információ a hardvergenerációk közötti különbségről a [felügyelt példány erőforráskorlátaiban.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)

## <a name="managed-instance-service-tiers"></a>Felügyelt példány szolgáltatási szintjei

A felügyelt példány két szolgáltatási szinten érhető el:

- **Általános célú:** Tipikus teljesítmény- és I/O-késési követelményekkel rendelkező alkalmazásokhoz készült.
- **Üzleti legkritikusabb:** Alacsony i/o-késési követelményekkel rendelkező alkalmazásokhoz és az alapul szolgáló karbantartási műveletek minimális hatással a munkaterhelésre.

Mindkét szolgáltatási szint 99,99%-os rendelkezésre állást garantál, és lehetővé teszi a tárolóméret és a számítási kapacitás egymástól független kiválasztását. Az Azure SQL Database magas rendelkezésre állású architektúrájáról a Magas rendelkezésre állás és az [Azure SQL Database című témakörben](sql-database-high-availability.md)talál további információt.

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatási szint

Az alábbi lista az Általános célú szolgáltatási szint legfontosabb jellemzőit tartalmazza:

- Tervezés a tipikus teljesítménykövetelményekkel rendelkező üzleti alkalmazások többségéhez
- Nagy teljesítményű Azure Blob-tároló (8 TB)
- Beépített, [magas rendelkezésre állás](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a megbízható Azure Blob-tárhely és az [Azure Service Fabric](../service-fabric/service-fabric-overview.md) alapján

További információkért lásd a [tárolási réteg általános célú réteg és](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) a tárolási teljesítmény ajánlott eljárások és szempontok a felügyelt [példányok (általános célú)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információ a szolgáltatásszintek közötti különbségről a [felügyelt példány erőforráskorlátaiban.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)

### <a name="business-critical-service-tier"></a>Üzleti legkritikusabb szolgáltatási szint

Az üzleti legkritikusabb szolgáltatási szint magas i/o-igényű alkalmazásokhoz készült. A hibák maximális rugalmasságát kínálja több elkülönített replikák használatával.

Az alábbi lista az üzleti legkritikusabb szolgáltatási szint főbb jellemzőit ismerteti:

- A legnagyobb teljesítményű és HA-követelményekkel rendelkező üzleti alkalmazásokhoz tervezve
- Szupergyors helyi SSD tárolóval érkezik (gen4-en akár 1 TB-ig, Gen5-ön pedig 4 TB-ig)
- Beépített [magas rendelkezésre állás](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) az Always On Availability Groups és [az](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) Azure [Service Fabric](../service-fabric/service-fabric-overview.md)alapján.
- Beépített, további [írásvédett adatbázis-replika,](sql-database-read-scale-out.md) amely jelentéskészítésre és egyéb írásvédett számítási feladatokra használható
- [Memóriaen belüli OLTP,](sql-database-in-memory.md) amely nagy teljesítményű munkaterheléshez használható  

További információ a szolgáltatásszintek közötti különbségről a [felügyelt példány erőforráskorlátaiban.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)


## <a name="managed-instance-management-operations"></a>Felügyelt példánykezelési műveletek

Az Azure SQL Database olyan felügyeleti műveleteket biztosít, amelyekkel automatikusan üzembe helyezhet új felügyelt példányokat, frissítheti a példány tulajdonságait és törölheti a szükségtelenné vált példányokat. Ez a szakasz a felügyeleti műveletekről és azok jellemző időtartamáról nyújt tájékoztatást.

Az [Azure virtuális hálózatokon (VNets) belüli telepítések](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) támogatása, valamint az elkülönítés és a biztonság biztosítása az ügyfelek számára, a felügyelt példány virtuális fürtökre támaszkodik, amelyek az ügyfél virtuális hálózati alhálózatában telepített elkülönített virtuális gépek dedikált készletét [képviselik.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) Lényegében minden felügyelt példány központi telepítése egy üres alhálózatban egy új virtuális fürt kiépítést eredményez.

Az üzembe helyezett felügyelt példányokon végzett további műveletek szintén hatással lehetnek az alapul szolgáló virtuális fürtre. Ez hatással van a felügyeleti műveletek időtartamára, mivel a további virtuális gépek üzembe helyezése olyan többletterheléssel jár, amelyet figyelembe kell venni az új központi telepítések vagy a meglévő felügyelt példányok frissítéseinek tervezésekor.

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány telepítése (új példány létrehozása). 
- Példányfrissítés (példánytulajdonságok, például virtuális magok vagy fenntartott tároló módosítása.
- Példány törlése.

A virtuális fürtökön végzett műveletek általában a leghosszabbideig. A virtuális fürtökön végzett műveletek időtartama változó – az alábbiakban találhatók azok az értékek, amelyekáltalában a meglévő szolgáltatástelemetriai adatok alapján várhatók:

- Virtuális fürt létrehozása. Ez egy szinkron lépés a példánykezelési műveletekben. **A műveletek 90%-a 4 óra alatt befejeződik.**
- Virtuális fürt átméretezése (bővítés vagy zsugorítás). A bővítés egy szinkron lépés, míg a zsugorítás aszinkron módon történik (a példánykezelési műveletek időtartamára gyakorolt hatás nélkül). **A fürtbővítések 90%-a kevesebb mint 2,5 óra alatt befejeződik.**
- Virtuális fürt törlése. A törlés egy aszinkron lépés, de manuálisan is [kezdeményezhető](sql-database-managed-instance-delete-virtual-cluster.md) egy üres virtuális fürtön, amely esetben szinkron módon hajtvégre. **A virtuális fürt törlések 90%-a 1,5 óra alatt befejeződik.**

Emellett a példányok kezelése a tárolt adatbázisok egyik műveletét is magában foglalhatja, ami hosszabb időtartamokat eredményez:

- Adatbázisfájlok csatolása az Azure Storage-ból. Ez egy szinkron lépés, például a számítási (virtuális mag), vagy a tárolási felskálázás vagy le az általános célú szolgáltatási szint. **Ezeknek a műveleteknek a 90%-a 5 perc alatt befejeződik.**
- Mindig a rendelkezésre állás csoport vetés. Ez egy szinkron lépés, például a számítási (virtuális mag), vagy a tárolási skálázás az üzleti legkritikusabb szolgáltatási szinten, valamint a szolgáltatási szint általános célról üzleti legkritikusabb (vagy fordítva) módosítása. A művelet időtartama arányos az adatbázis teljes méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók számával). Az adatbázis-tevékenység egy példány frissítésekor jelentős eltérést okozhat a teljes időtartamhoz képest. **Ezeknek a műveleteknek 90%-a 220 GB/óra vagy annál nagyobb sebességgel hajtható végre.**

Az alábbi táblázat a műveleteket és a tipikus teljes időtartamokat foglalja össze:

|Kategória  |Művelet  |Hosszú távú szegmens  |Becsült időtartam  |
|---------|---------|---------|---------|
|**Környezet** |Első példány üres alhálózatban|Virtuális fürt létrehozása|A műveletek 90%-a 4 óra alatt befejeződik|
|Környezet |Egy másik hardvergenerálás első példánya egy nem üres alhálózatban (például első Gen 5 példány egy gen 4-es példányokkal rendelkező alhálózatban)|Virtuális fürt létrehozása*|A műveletek 90%-a 4 óra alatt befejeződik|
|Környezet |4 virtuális mag első példánylétrehozása üres vagy nem üres alhálózatban|Virtuális fürt létrehozása**|A műveletek 90%-a 4 óra alatt befejeződik|
|Környezet |További példány létrehozása a nem üres alhálózaton belül (2., 3., stb. példány)|Virtuális fürt átméretezése|A műveletek 90%-a 2,5 óra alatt befejeződik|
|**Frissítés** |Példánytulajdonságának módosítása (rendszergazdai jelszó, AAD-bejelentkezés, Azure Hybrid Benefit-jelző)|N/A|Akár 1 perc|
|Frissítés |Példánytárolási felskálázás fel-/leskálázása (Általános célú szolgáltatási szint)|Adatbázisfájlok csatolása|A műveletek 90%-a 5 perc alatt befejeződik|
|Frissítés |Példánytárolási felskálázás fel-le (üzleti legkritikusabb szolgáltatási szint)|- Virtuális fürt átméretezése<br>- Mindig a rendelkezésre állás csoport vetés|90%-a műveletek befejezni 2,5 óra + idő vetőmag minden adatbázis (220 GB / óra)|
|Frissítés |Példány számítási (virtuális magok) fel-le skálázás (Általános célú)|- Virtuális fürt átméretezése<br>- Adatbázisfájlok csatolása|A műveletek 90%-a 2,5 óra alatt befejeződik|
|Frissítés |Példány számítási (virtuális magok) felskálázása és leskálázása (üzleti legkritikusabb)|- Virtuális fürt átméretezése<br>- Mindig a rendelkezésre állás csoport vetés|90%-a műveletek befejezni 2,5 óra + idő vetőmag minden adatbázis (220 GB / óra)|
|Frissítés |Példány leskálázása 4 virtuális magra (általános célú)|- Virtuális fürt átméretezése (ha ez az első alkalom, akkor szükség lehet a virtuális fürt létrehozása **)<br>- Adatbázisfájlok csatolása|A műveletek 90%-a 4 óra 5 min**|
|Frissítés |Példány skálázható 4 virtuális magra (üzleti legkritikusabb)|- Virtuális fürt átméretezése (ha ez az első alkalom, akkor szükség lehet a virtuális fürt létrehozása **)<br>- Mindig a rendelkezésre állás csoport vetés|90%-a műveletek befejezni 4 óra + idő vetőmag minden adatbázis (220 GB / óra)|
|Frissítés |Példányszolgáltatási szint módosítása (Általános cél az üzleti szempontból kritikussá és fordítva)|- Virtuális fürt átméretezése<br>- Mindig a rendelkezésre állás csoport vetés|90%-a műveletek befejezni 2,5 óra + idő vetőmag minden adatbázis (220 GB / óra)|
|**Törlés**|Példány törlése|Log tail biztonsági mentés az összes adatbázishoz|A 90%-os műveletek akár 1 perc alatt befejeződhetnek.<br>Megjegyzés: ha az alhálózat utolsó példánya törlődik, ez a művelet 12 óra elteltével ütemezi a virtuális fürt törlését***|
|Törlés|Virtuális fürt törlése (felhasználó által kezdeményezett műveletként)|Virtuális fürt törlése|A műveletek 90%-a akár 1,5 óra alatt befejeződik|

\*A virtuális fürt hardvergenerálásonként épül fel.

\*\*A 4 virtuális mag telepítési lehetősége 2019 júniusában jelent meg, és új virtuális fürtverzióra van szükség. Ha a célalhálózatban június 12.-e előtt létrehozott példányokkal volt megszokva, a rendszer automatikusan telepíti az új virtuális fürtöt 4 virtuálismagos példány üzemeltetésére.

\*\*\*12 óra az aktuális konfiguráció, de ez változhat a jövőben, ezért ne vegyen egy kemény függőség rajta. Ha korábban törölnie kell egy virtuális fürtöt (például az alhálózat felszabadításához), olvassa el az [Alhálózat törlése az Azure SQL Database felügyelt példányának törlése után című témakört.](sql-database-managed-instance-delete-virtual-cluster.md)

### <a name="instance-availability-during-management"></a>A példány elérhetősége a felügyelet során

A felügyelt példányok nem érhetők el az ügyfélalkalmazások számára a telepítési és törlési műveletek során.

A felügyelt példányok a frissítési műveletek során érhetők el, de a frissítések végén, általában 10 másodpercig tartó feladatátvétel miatt rövid állásidő áll fenn. Ez alól kivételt képez az általános célú szolgáltatásszinten fenntartott tárhely frissítése, amely nem merül fel feladatátvételsel, és nem befolyásolja a példány okait.

> [!IMPORTANT]
> A feladatátvétel időtartama jelentősen változhat az adatbázisokon a [hosszú helyreállítási idő](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)miatt lebonyolított hosszú ideig futó tranzakciók esetén . Ezért nem ajánlott az Azure SQL Database felügyelt példányának számítási vagy tárolási méretezése, vagy a szolgáltatásszint módosítása a hosszú ideig futó tranzakciókkal (adatimportálás, adatfeldolgozási feladatok, index-újraépítés stb.). A művelet végén végrehajtandó adatbázis-feladatátvétel megszakítja a folyamatban lévő tranzakciókat, és hosszabb helyreállítási időt eredményez.

> [!TIP]
> Az általános célú szolgáltatásszinten fenntartott tárhely frissítése nem jár feladatátvételsel, és nem befolyásolja a példányok rendelkezésre állását.

[Gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) jelenleg nem érhető el az Azure SQL Database felügyelt példányai. Ha engedélyezve van, ez a funkció jelentősen csökkenti a feladatátvételi idő változékonyságát, még hosszú ideig futó tranzakciók esetén is.

### <a name="canceling-management-operations"></a>Felügyeleti műveletek megszakítása

Az alábbi táblázat összefoglalja az adott irányítási műveletek és a tipikus teljes időtartamok megszakításának képességét:

Kategória  |Művelet  |Megszakítható  |Becsült megszakítási időtartam  |
|---------|---------|---------|---------|
|Környezet |Példány létrehozása |Nem |  |
|Frissítés |Példánytároló fel-le méretezése/leskálázása (Általános cél) |Nem |  |
|Frissítés |Példánytárolási felskálázás fel-/leskálázása (üzleti legkritikusabb) |Igen |A műveletek 90%-a 5 perc alatt befejeződik |
|Frissítés |Példány számítási (virtuális magok) fel-le skálázás (Általános célú) |Igen |A műveletek 90%-a 5 perc alatt befejeződik |
|Frissítés |Példány számítási (virtuális magok) felskálázása és leskálázása (üzleti legkritikusabb) |Igen |A műveletek 90%-a 5 perc alatt befejeződik |
|Frissítés |Példányszolgáltatási szint módosítása (Általános cél az üzleti szempontból kritikussá és fordítva) |Igen |A műveletek 90%-a 5 perc alatt befejeződik |
|Törlés |Példány törlése |Nem |  |
|Törlés |Virtuális fürt törlése (felhasználó által kezdeményezett műveletként) |Nem |  |

A felügyeleti művelet megszakításához lépjen az áttekintő panelre, és kattintson a folyamatban lévő működés értesítési mezőjére. A jobb oldalon egy folyamatos működést tartalmazó képernyő jelenik meg, és gomb lesz a művelet megszakítására. Az első kattintás után a rendszer megkéri, hogy kattintson újra, és erősítse meg, hogy meg szeretné szakítani a műveletet.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

A kérelem visszavonása és feldolgozása után értesítést kap, ha a beküldés visszavonása sikeres volt vagy sem. 

Sikeres lemondás esetén a felügyeleti művelet néhány percen belül megszakad, ami sikertelenséggel jár.

![művelet eredménynek megszakítása](./media/sql-database-managed-instance/canceling-operation-result.png)

Ha a kérés visszavonása sikertelen, vagy a Mégse gomb nem aktív, az azt jelenti, hogy a felügyeleti művelet nem érvényteleníthető állapotba került, és néhány perc alatt befejeződik. A kezelési művelet a végrehajtást a befejezéséig folytatja.

> [!IMPORTANT]
> A megszakítási művelet jelenleg csak a Portalban támogatott.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

A felügyelt példányok üzembe helyezésének lehetősége az Azure cloud és az SQL Server Database Engine által biztosított speciális biztonsági funkciókat kombinálja.

### <a name="managed-instance-security-isolation"></a>Felügyelt példány biztonsági elkülönítése

A felügyelt példány további biztonsági elkülönítést biztosít az Azure-felhő többi bérlőjétől. A biztonsági elkülönítés a következőket foglalja magában:

- [Natív virtuális hálózati megvalósítás](sql-database-managed-instance-connectivity-architecture.md) és a helyszíni környezethez való kapcsolódás az Azure Express Route vagy vpn átjáró használatával.
- Az alapértelmezett központi telepítésben az SQL-végpont csak egy privát IP-címen keresztül érhető el, lehetővé téve a biztonságos kapcsolatot a privát Azure-ból vagy hibrid hálózatokból.
- Egybérlős dedikált mögöttes infrastruktúrával (számítási, tárolási).

Az alábbi ábra az alkalmazások különböző csatlakozási lehetőségeit ismerteti:

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Ha többet szeretne tudni a virtuális hálózatok integrációjáról és a hálózati házirendek alhálózati szintű kényszerítéséről, olvassa el a [felügyelt példányok virtuális hálózati architektúráját](sql-database-managed-instance-connectivity-architecture.md) és [az alkalmazás csatlakoztatása felügyelt példányhoz című témakört.](sql-database-managed-instance-connect-app.md)

> [!IMPORTANT]
> Helyezzen több felügyelt példányt ugyanabba az alhálózatba, ahol ezt a biztonsági követelmények megengedik, mivel ez további előnyökkel jár. A példányok ugyanazon alhálózatban történő lefoglalása jelentősen leegyszerűsíti a hálózati infrastruktúra karbantartását, és csökkenti a példánykiépítési időt, mivel a hosszú kiépítési időtartam az első felügyelt példány alhálózatban történő üzembe helyezésének költségéhez kapcsolódik.

### <a name="azure-sql-database-security-features"></a>Az Azure SQL Database biztonsági szolgáltatásai

Az Azure SQL Database fejlett biztonsági funkciókat biztosít, amelyek az adatok védelmére használhatók.

- [A felügyelt példány naplózása](sql-database-managed-instance-auditing.md) nyomon követi az adatbázis-eseményeket, és az Okat az Azure storage-fiókjába helyezett naplófájlba írja. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenységek megértésében, valamint az olyan eltérésekben és anomáliákban, amelyek üzleti problémákra vagy a biztonság feltételezett megsértésére utalhatnak.
- Az adatok titkosítása mozgásban - egy felügyelt példány védi az adatokat azáltal, hogy titkosítást biztosít a mozgásban lévő adatokhoz a Transport Layer Security használatával. A szállítási réteg biztonsága mellett a felügyelt példánytelepítési lehetőség védelmet nyújt a bizalmas adatok repülés közben, nyugalmi állapotban és lekérdezésfeldolgozás közben [az Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használatával. Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például az Always Encrypted (Mindig titkosított) eszköz esetén a hitelkártyaszámok mindig titkosítva tárolódnak az adatbázisban, még a lekérdezés feldolgozása során is, lehetővé téve a visszafejtést a felhasználási helyen az arra jogosult személyzet vagy az adatok feldolgozásához szükséges alkalmazások számára.
- [A komplex veszélyforrások elleni védelem](sql-database-managed-instance-threat-detection.md) kiegészíti [a naplózást](sql-database-managed-instance-auditing.md) azáltal, hogy a szolgáltatásba épített biztonsági intelligencia egy további rétegét biztosítja, amely észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Figyelmeztetést kap a gyanús tevékenységekről, a potenciális biztonsági résekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A komplex veszélyforrások elleni riasztások megtekinthetők az [Azure Security Centerből,](https://azure.microsoft.com/services/security-center/) és részletesen ismertetik a gyanús tevékenységeket, és javaslatot tesznek a fenyegetés kivizsgálására és csökkentésére vonatkozó műveletekre.  
- [A dinamikus adatok maszkolása](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatexpozíciót azáltal, hogy nem emelt szintű jogosultsággal rendelkező felhasználóknak maszkolja. A dinamikus adatmaszkolás segít megakadályozni a bizalmas adatokhoz való jogosulatlan hozzáférést azáltal, hogy lehetővé teszi annak beállítását, hogy a bizalmas adatok közül mennyi kerüljön nyilvánosságra, minimális hatással az alkalmazásrétegre. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.
- [A sorszintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi az adatbázistábla soraihoz való hozzáférés szabályozását a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet szerint). A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Például annak biztosítása, hogy a dolgozók csak a részlegükhöz tartozó adatsorokhoz férhessenek hozzá, vagy az adatokhoz való hozzáférést csak a megfelelő adatokra korlátozhatják.
- [Az átlátszó adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) titkosítja a felügyelt példány adatfájljait, más néven az inaktív adatok titkosítását. A TDE valós idejű I/O titkosítást és az adatok és naplófájlok visszafejtését hajtja végre. A titkosítás egy adatbázis-titkosítási kulcsot (DEK) használ, amely az adatbázis rendszerindítási rekordjában tárolódik a helyreállítás során való rendelkezésre állás hoz. A felügyelt példányok összes adatbázisát transzparens adattitkosítással védheti. A TDE az SQL Server bevált inaktív titkosítási technológiája, amelyet számos megfelelőségi szabvány megkövetel a tárolóeszközök ellopása elleni védelemhez.

A titkosított adatbázis felügyelt példányba való áttelepítése az Azure Database Migration Service (DMS) vagy a natív visszaállítás révén támogatott. Ha egy titkosított adatbázisnatív visszaállítással kíván áttelepíteni, a meglévő TDE-tanúsítvány áttelepítése a helyszíni SQL Server kiszolgálóról vagy az SQL Server ről egy felügyelt példányba szükséges lépés. Az áttelepítési beállításokról az [SQL Server példányok felügyelt példányba való áttelepítése](sql-database-managed-instance-migrate.md)című témakörben talál további információt.

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

A felügyelt példány telepítési beállítás támogatja a hagyományos SQL server database motor bejelentkezések és bejelentkezések integrált Azure Active Directory (AAD). Az Azure AD-kiszolgálói egyszerű (logins) (**nyilvános előzetes verzió**) a helyszíni adatbázis-bejelentkezések helyszíni adatbázis-bejelentkezések Azure-felhőbeli verziója, amelyet a helyszíni környezetben használ. Az Azure AD-kiszolgálói egyszerű (bejelentkezési adatok) lehetővé teszik, hogy az Azure Active Directory-bérlőből származó felhasználókat és csoportokat valódi példányhatókör-rendszernévként adja meg, amely képes bármilyen példányszintű műveletet végrehajtani, beleértve az adatbázisközi lekérdezéseket ugyanazon felügyelt példányon belül.

Új szintaxis kerül bevezetésre az Azure AD-kiszolgálói egyszerű staféta-rendszerének létrehozásához **külső szolgáltatótól.** A szintaxisról további információt a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN című</a>témakörben talál, és tekintse át az [Azure Active Directory rendszergazdájának kiépítése a felügyelt példánycikkhez](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) című témakört.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

A felügyelt példányok üzembe helyezésének lehetővé teszi az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését az [Azure Active Directory-integrációval.](sql-database-aad-authentication.md) Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="authentication"></a>Hitelesítés

A felügyelt példányok hitelesítése azt a módját utalja, hogy a felhasználók hogyan bizonyítják identitásukat az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:  

- **SQL-hitelesítés:**

  Ez a hitelesítési módszer felhasználónevet és jelszót használ.
- **Azure Active Directory-hitelesítés:**

  Ez a hitelesítési módszer az Azure Active Directory által kezelt identitásokat használja, és felügyelt és integrált tartományok számára támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználó mit tehet az Azure SQL-adatbázisban, és a felhasználói fiók adatbázis-szerepkör-tagsága és objektumszintű engedélyei szabályozzák. A felügyelt példányok ugyanolyan engedélyezési képességekkel rendelkeznek, mint az SQL Server 2017.

## <a name="database-migration"></a>Adatbázis-áttelepítés

A felügyelt példány telepítési beállítás a helyszíni vagy IaaS-adatbázis-megvalósításokból tömeges adatbázis-áttelepítéssel rendelkező felhasználói forgatókönyveket célozza meg. A felügyelt példány számos adatbázis-áttelepítési lehetőséget támogat:

### <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás  

Az áttelepítési megközelítés az SQL biztonsági mentések az Azure Blob storage.The migration approach leverages SQL backups to Azure Blob storage. Az Azure storage blobban tárolt biztonsági mentések közvetlenül visszaállíthatók egy felügyelt példányba a [T-SQL RESTORE paranccsal.](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)

- A Wide World Importers - Standard adatbázis-biztonsági másolat fájl visszaállításának rövid útmutatóját a [Biztonsági másolat visszaállítása felügyelt példányra című témakörben ismerteti.](sql-database-managed-instance-get-started-restore.md) Ez a rövid útmutató azt mutatja, hogy fel kell töltenie egy biztonsági mentési fájlt az Azure blob storage-ba, és egy megosztott hozzáférésű aláírás (SAS) kulccsal kell biztonságossá tenni.
- Az URL-címről történő visszaállításról a [Natív visszaállítás az URL-címről](sql-database-managed-instance-migrate.md#native-restore-from-url)című témakörben talál további információt.

> [!IMPORTANT]
> A felügyelt példány biztonsági másolatai csak egy másik felügyelt példányra állíthatók vissza. Nem állíthatók vissza egy helyszíni SQL Server vagy egyetlen adatbázis/rugalmas készlet.

### <a name="data-migration-service"></a>Data Migration Service

Az Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a zökkenőmentes áttelepítést több adatbázis-forrásból az Azure Data-platformokra minimális állásidővel. Ez a szolgáltatás egyszerűsíti a meglévő harmadik féltől származó és SQL Server-adatbázisok Azure SQL Database (egyetlen adatbázisok, rugalmas készletekben lévő készletes adatbázisok és egy felügyelt példány példány-adatbázisai) és az SQL Server Azure VM-be való áthelyezéséhez szükséges feladatokat. Lásd: [A helyszíni adatbázis áttelepítése a felügyelt példányba a DMS használatával.](https://aka.ms/migratetoMIusingDMS)

## <a name="sql-features-supported"></a>Támogatott SQL-szolgáltatások

A felügyelt példány telepítési beállításának célja, hogy közel 100%-os felületi kompatibilitást biztosítson a helyszíni SQL Server-kiszolgálóval, amely a szolgáltatás általános rendelkezésre állásáig szakaszokban érkezik. A szolgáltatások és az összehasonlító lista az [SQL Database szolgáltatás összehasonlítása](sql-database-features.md)című témakörben, valamint a felügyelt példányok és az SQL Server t-SQL-beli különbségeinek listáját az [SQL Server felügyelt példányt tartalmazó T-SQL-különbségei](sql-database-managed-instance-transact-sql-information.md)című témakörben található.

A felügyelt példány telepítési lehetősége támogatja az SQL 2008-as adatbázisokkal való visszamenőleges kompatibilitást. Az SQL 2005 adatbázis-kiszolgálókról történő közvetlen áttelepítés támogatott, az áttelepített SQL 2005 adatbázisok kompatibilitási szintje SQL 2008-ra frissül.
  
Az alábbi ábra ismerteti a felületkompatibilitást a felügyelt példányban:  

![Áttelepítési](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>A helyszíni ÉS a felügyelt példányban lévő SQL Server közötti főbb különbségek

A felügyelt példány üzembe helyezésének előnyeit élvezi, hogy mindig naprakész a felhőben, ami azt jelenti, hogy a helyszíni SQL Server egyes szolgáltatásai elavultak, kivannak vonva, vagy alternatívákkal rendelkeznek. Vannak olyan konkrét esetek, amikor az eszközöknek fel kell ismerniük, hogy egy adott szolgáltatás kissé eltérő módon működik, vagy hogy a szolgáltatás olyan környezetben fut, amelyet nem teljesen irányít:

- A magas rendelkezésre állás az [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)technológiához hasonló technológia segítségével van beépítve és előre konfigurálva.
- Automatikus biztonsági mentések és időpont-visszaállítás. Az ügyfél `copy-only` olyan biztonsági mentéseket kezdeményezhet, amelyek nem zavarják az automatikus biztonsági mentési láncot.
- A felügyelt példány nem teszi lehetővé a teljes fizikai elérési utak megadását, így az összes megfelelő forgatókönyvet másképp kell támogatni: a RESTORE DB nem támogatja a MOVE-t, a CREATE DB nem engedélyezi a fizikai elérési utakat, a BULK INSERT csak az Azure Blobs-szal működik stb.
- A felügyelt példány támogatja az [Azure AD-hitelesítést](sql-database-aad-authentication.md) a Windows-hitelesítés felhőbeli alternatívájaként.
- A felügyelt példány automatikusan kezeli az XTP fájlcsoportot és a memórián belüli OLTP-objektumokat tartalmazó adatbázisok fájljait
- A felügyelt példány támogatja az SQL Server Integration Services (SSIS) szolgáltatást, és ssis-csomagokat tároló SSIS-katalógust (SSISDB) üzemeltethet, de azok az Azure Data Factory (ADF) felügyelt Azure-SSIS-integrációs futásidejű (IR) szolgáltatásán hajtják végre őket, [lásd: Azure-SSIS-ir létrehozása az ADF-ben.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) Az SQL Database SSIS-szolgáltatásainak összehasonlításához olvassa [el az Azure SQL Database egyetlen adatbázis, a rugalmas készlet és a felügyelt példány összehasonlítása című témakört.](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)

### <a name="managed-instance-administration-features"></a>Felügyelt példányfelügyeleti szolgáltatások

A felügyelt példány telepítési lehetősége lehetővé teszi a rendszergazda számára, hogy kevesebb időt töltsön a felügyeleti feladatokra, mert az SQL Database szolgáltatás vagy elvégzi azokat, vagy jelentősen leegyszerűsíti ezeket a feladatokat. Például [az operációs rendszer / RDBMS telepítése és javítása,](sql-database-high-availability.md) [a dinamikus példányátméretezés és -konfigurálás,](sql-database-single-database-scale.md) [a biztonsági mentések](sql-database-automated-backups.md), [az adatbázis-replikáció](replication-with-sql-database-managed-instance.md) (beleértve a rendszeradatbázisokat is), [a magas rendelkezésre állású konfiguráció,](sql-database-high-availability.md)valamint az állapot- és [teljesítményfigyelési](../azure-monitor/insights/azure-sql.md) adatfolyamok konfigurálása.

> [!IMPORTANT]
> A támogatott, részben támogatott és nem támogatott szolgáltatások listáját az [SQL Database szolgáltatásai című témakörben tetszetős.](sql-database-features.md) A felügyelt példányok és az SQL Server t-SQL-különbségeinek listáját az [SQL Server felügyelt példányt tartalmazó T-SQL-különbségei című](sql-database-managed-instance-transact-sql-information.md) témakörben

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Felügyelt példány programozott azonosítása

Az alábbi táblázat több, a Transact SQL-en keresztül elérhető tulajdonságot mutat be, amelyek segítségével észlelheti, hogy az alkalmazás felügyelt példányokkal dolgozik, és fontos tulajdonságokat kér le.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 2018.018.03.07.|Ez az érték megegyezik az SQL Database értékével. Ez nem jelenti **az** SQL motor 12-es verzióját (SQL Server 2014). A felügyelt példány mindig a legújabb stabil SQL motorverziót futtatja, amely egyenlő vagy magasabb, mint az SQL Server legújabb elérhető RTM verziója.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez az érték megegyezik az SQL Database értékével.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték egyedileg azonosítja a felügyelt példányt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Teljes példány DNS-neve a`<instanceName>`következő formátumban: . `<dnsPrefix>`.database.windows.net, `<instanceName>` ahol az ügyfél által `<dnsPrefix>` megadott név, míg a név automatikusan generált része garantálja a globális DNS-név egyediségét (például wcus17662feb9ce98)|Példa: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>További lépések

- Az első felügyelt példány létrehozásáról a Rövid útmutató című [témakörben olvashat.](sql-database-managed-instance-get-started.md)
- A szolgáltatások és az összehasonlító lista az SQL általános szolgáltatásai című témakörben [található.](sql-database-features.md)
- A virtuális hálózat konfigurációjáról további információt a [felügyelt példány virtuális hálózatának konfigurálása](sql-database-managed-instance-connectivity-architecture.md)című témakörben talál.
- Felügyelt példányt létrehozó és biztonsági másolatból adatbázist visszaállító rövidútmutatóról a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md)című témakörben található.
- Az Azure Database Migration Service (DMS) áttelepítési szolgáltatását használó oktatóanyagról a [Felügyelt példányáttelepítés DMS használatával című](../dms/tutorial-sql-server-to-managed-instance.md)témakörben található.
- A felügyelt példányok adatbázis-teljesítményének speciális figyelése beépített hibaelhárítási intelligenciával az [Azure SQL Database monitorozása az Azure SQL Analytics használatával című](../azure-monitor/insights/azure-sql.md)témakörben található.
- A díjszabásról az [SQL Database által felügyelt példányok díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/)című témakörben talál.
