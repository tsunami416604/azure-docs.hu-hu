---
title: Mi az az Azure SQL felügyelt példánya?
description: Ismerje meg az Azure SQL felügyelt példányát, amely közel 100%-os kompatibilitást biztosít a legújabb SQL Server (Enterprise Edition) adatbázismotor
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 886653bcfa255c7929f7e23082b74f171c97c7a1
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195438"
---
# <a name="what-is-azure-sql-managed-instance"></a>Mi az az Azure SQL felügyelt példánya?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL termékcsalád részét képező Azure SQL felügyelt példány az intelligens, skálázható, felhőalapú adatbázis-szolgáltatás, amely a legszélesebb körben felügyelt és örökzöld platform szolgáltatásként nyújtott előnyökkel ötvözi a legteljesebb SQL Server az adatbázismotor kompatibilitását. Az SQL felügyelt példány közel 100%-os kompatibilitást biztosít a legújabb SQL Server (Enterprise Edition) adatbázis-motorral, amely egy natív [virtuális hálózat (VNet)](../../virtual-network/virtual-networks-overview.md) megvalósítását biztosítja, amely az általános biztonsági kérdésekre és a meglévő SQL Server ügyfelek számára kedvező [üzleti modellre](https://azure.microsoft.com/pricing/details/sql-database/) épül. Az SQL felügyelt példánya lehetővé teszi a meglévő SQL Server ügyfelek számára, hogy a felhőbe a helyszíni alkalmazásaikat minimális alkalmazás-és adatbázis-módosításokkal emelje át. Ugyanakkor az SQL felügyelt példánya megőrzi az összes olyan Péter-képességet (az automatikus javításokat és a verziószámokat, az [automatizált biztonsági mentéseket](../database/automated-backups-overview.md)és a [magas rendelkezésre állást](../database/high-availability-sla.md) ), amelyek drasztikusan csökkentik a felügyeleti terhelést és a TCO-t.

> [!IMPORTANT]
> Azoknak a régióknak a listájáért, amelyeken jelenleg elérhető az SQL felügyelt példány, tekintse meg a [támogatott régiók](resource-limits.md#supported-regions)című témakört.

Az alábbi ábra az SQL felügyelt példányainak főbb funkcióit vázolja fel:

![főbb funkciók](./media/sql-managed-instance-paas-overview/key-features.png)

Az Azure SQL felügyelt példánya olyan ügyfelek számára készült, akik nagy mennyiségű alkalmazást kívánnak áttelepíteni a helyszíni vagy IaaS, a saját készítésű vagy az ISV által biztosított környezetből a teljes körűen felügyelt, kihasználatlan Felhőbeli környezetbe, a lehető legkevesebb migrációs megoldással. Az Azure-ban a teljes mértékben automatizált [adatáttelepítési szolgáltatás (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) használatával az ügyfelek megemelik és áthelyezhetik meglévő SQL Server példányaikat egy olyan SQL-alapú felügyelt példányra, amely kompatibilis a SQL Server és a natív VNet-támogatással rendelkező vásárlói példányok elkülönítésével.  A frissítési garanciával lehetősége van arra, hogy a meglévő licenceket a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával a felügyelt SQL-példányok kedvezményes díjszabására cserélje le. Az SQL felügyelt példány a felhőben a legjobb áttelepítési cél SQL Server olyan példányok esetében, amelyeknek magas a biztonságuk, valamint egy sokoldalú programozható felület szükséges.

## <a name="key-features-and-capabilities"></a>Főbb funkciók és képességek

A felügyelt SQL-példányok a Azure SQL Database és SQL Server adatbázismotor által elérhető legjobb funkciókat ötvözik.

> [!IMPORTANT]
> Az SQL felügyelt példánya a SQL Server legújabb verziójának összes szolgáltatásával fut, beleértve az online műveleteket, az automatikus terv javításait és az egyéb vállalati teljesítmény-fejlesztéseket. Az elérhető funkciók összehasonlítása a [funkció-összehasonlításban: az Azure SQL felügyelt példánya](../database/features-comparison.md)és a SQL Server.

| **Pásti előnyei** | **Az üzletmenet folytonossága** |
| --- | --- |
|Nincs hardveres vásárlás és felügyelet <br>Nincs felügyeleti terhelés a mögöttes infrastruktúra kezeléséhez <br>Gyors kiépítés és a szolgáltatás skálázása <br>Automatikus javítás és verziófrissítés <br>Integráció más Pásti adatszolgáltatásokkal |99,99%-os rendelkezésre állási SLA  <br>Beépített [magas rendelkezésre állás](../database/high-availability-sla.md) <br>[Automatizált biztonsági mentéssel](../database/automated-backups-overview.md) védett adatvédelem <br>Ügyfél által konfigurálható biztonsági másolatok megőrzési ideje <br>Felhasználó által kezdeményezett [biztonsági másolatok](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időponthoz tartozó adatbázis-visszaállítási](../database/recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Felügyelet**|
|Elszigetelt környezet ([VNet-integráció](connectivity-architecture-overview.md), egybérlős szolgáltatás, dedikált számítás és tárolás) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure ad-hitelesítés](../database/authentication-aad-overview.md), egyszeri bejelentkezés támogatása <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a>  <br>Megfelel a megfelelőségi szabványoknak, mint a Azure SQL Database <br>[SQL-naplózás](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager API a szolgáltatások kiosztásának és méretezésének automatizálásához <br>A szolgáltatás kézi üzembe helyezéséhez és méretezéséhez Azure Portal funkció <br>Data Migration Service

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure megfelelőségi ajánlatait](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers) , ahol megtalálhatja az SQL által felügyelt példányok megfelelőségi minősítésének legfrissebb listáját a **SQL Database**alatt.

Az SQL felügyelt példány legfontosabb funkciói az alábbi táblázatban láthatók:

|Szolgáltatás | Leírás|
|---|---|
| SQL Server verzió/Build | SQL Server adatbázismotor (legújabb stabil) |
| Felügyelt automatizált biztonsági másolatok | Igen |
| Beépített példány-és adatbázis-figyelés és-metrikák | Igen |
| Szoftver automatikus javítása | Igen |
| Az adatbázismotor legújabb funkciói | Igen |
| Adatfájlok (sorok) száma az adatbázisban | Többszörös |
| Naplófájlok (napló) száma adatbázisban | 1 |
| VNet – Azure Resource Manager üzemelő példány | Igen |
| VNet – klasszikus üzembe helyezési modell | Nem |
| Portál támogatása | Igen|
| Beépített integrációs szolgáltatás (SSIS) | Nem – a SSIS a [Azure Data Factory Péter](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) részét képezi |
| Beépített Analysis Service (SSAS) | Nem – a SSAS külön [Péter](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Beépített jelentéskészítési szolgáltatás (SSRS) | Nem – használjon [Power bi](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) többoldalas jelentéseket, vagy az SSRS-t az Azure-beli virtuális gépen. Míg a felügyelt példányok nem futtathatják az SSRS-t szolgáltatásként, SQL Server hitelesítést használó külső jelentéskészítő kiszolgáló SSRS 2019 Catalog-adatbázisait üzemeltetheti. |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

Az SQL felügyelt példány [virtuális mag-alapú vásárlási modellje](../database/service-tiers-vcore.md) rugalmasságot, irányítást, átláthatóságot és egyszerű módszert biztosít a helyszíni számítási feladatok követelményeinek a felhőbe való lefordításához. Ez a modell lehetővé teszi a számítási feladatok, a memória és a tárterület módosítását a munkaterhelés igényei alapján. A virtuális mag modell akár 55%-os megtakarítást is igénybe vehet, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server.

A virtuális mag modellben a hardver generációi közül választhat.

- **Gen4** A logikai CPU-k Intel E5-2673 v3 (Haswell) 2,4-GHz processzorok, csatlakoztatott SSD, fizikai magok, 7 GB RAM/mag, valamint 8 és 24 virtuális mag közötti számítási méretek.
- **Gen5** A logikai CPU-k Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok, a gyors NVMe SSD-k, a Hyper-threaded logikai mag és a 4 és 80 közötti számítási méretek alapján működnek.

További információk a hardveres generációk közötti különbségről az [SQL felügyelt példányának erőforrás-korlátaiban](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Szolgáltatásszintek

A felügyelt SQL-példány két szolgáltatási rétegben érhető el:

- **Általános célú**: olyan alkalmazásokhoz tervezve, amelyek jellemző teljesítménybeli és IO-késési követelményekkel rendelkeznek.
- **Üzleti szempontból kritikus**: az alacsony i/o-késési követelményekkel rendelkező alkalmazások számára lett kialakítva, és a számítási feladatok mögöttes karbantartási műveletek minimális hatása.

Mindkét szolgáltatási szinten 99,99%-os rendelkezésre állást garantálunk, és lehetővé teszi a tárolási méret és a számítási kapacitás egymástól független kiválasztását. További információ az Azure SQL felügyelt példány magas rendelkezésre állású architektúrával kapcsolatban: [magas rendelkezésre állás és Azure SQL felügyelt példány](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatási szintek

Az alábbi lista ismerteti a általános célú szolgáltatási szinten szereplő legfontosabb jellemzőket:

- Az üzleti alkalmazások többségének kialakítása jellemző teljesítménnyel kapcsolatos követelményekkel
- Nagy teljesítményű Azure Blob Storage (8 TB)
- Beépített [magas rendelkezésre állás](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) a megbízható Azure Blob Storage és az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) alapján

További információ: [tárolási réteg általános célú rétegben](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) és [tárolási teljesítmény – ajánlott eljárások és szempontok az SQL felügyelt példányaihoz (általános célú)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információ a szolgáltatási szintek közötti különbségről az [SQL felügyelt példányának erőforrás-korlátaiban](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>üzletileg kritikus szolgáltatási szintet

Üzletileg kritikus szolgáltatási szintet a magas i/o-követelményeknek megfelelő alkalmazások számára készült. Több elszigetelt replika használatával a hibák legmagasabb rugalmasságát kínálja.

Az alábbi lista a üzletileg kritikus szolgáltatási szintje főbb jellemzőit ismerteti:

- A legnagyobb teljesítménnyel és HA követelményekkel rendelkező üzleti alkalmazásokhoz tervezve
- A szolgáltatás villámgyors helyi SSD-tárolóval (1 TB-ig Gen4 és akár 4 TB-ig Gen5) is rendelkezik
- Beépített [magas rendelkezésre](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) állás az [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) és az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) alapján
- Beépített további [írásvédett adatbázis-replikák](../database/read-scale-out.md) , amelyek jelentéskészítésre és más írásvédett számítási feladatokra is használhatók
- [Memóriabeli OLTP](../in-memory-oltp-overview.md) , amely nagy teljesítményű követelményekkel használható munkaterheléshez  

További információ a szolgáltatási szintek közötti különbségről az [SQL felügyelt példányának erőforrás-korlátaiban](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Felügyeleti műveletek

Az Azure SQL felügyelt példánya olyan felügyeleti műveleteket biztosít, amelyek segítségével automatikusan telepítheti az új felügyelt példányokat, frissítheti a példány tulajdonságait, és törölheti a példányokat, ha már nincs rá szükség. Ez a szakasz a felügyeleti műveletekkel és azok jellemző időtartamával kapcsolatos információkat tartalmaz.

Az [Azure Virtual Networks (virtuális hálózatok) szolgáltatáson belüli központi telepítések](../../virtual-network/virtual-network-for-azure-services.md) támogatásához, valamint az ügyfelek elkülönítésének és biztonságának biztosításához az SQL felügyelt példánya [virtuális fürtökre](connectivity-architecture-overview.md#high-level-connectivity-architecture)támaszkodik, amelyek az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét jelölik. Az üres alhálózat minden felügyelt példányának telepítése lényegében egy új virtuális fürt buildout eredményez.

A központilag telepített felügyelt példányok további műveletei hatással lehetnek a mögöttes virtuális fürtre is. Ez befolyásolja a felügyeleti műveletek időtartamát, mivel a további virtuális gépek üzembe helyezése olyan terheléssel jár, amelyet figyelembe kell venni, amikor új központi telepítéseket vagy frissítéseket tervez a meglévő felügyelt példányokra.

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása).
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló.
- Példány törlése.

Általában a virtuális fürtökön végrehajtott műveletek a leghosszabb időt jelentik. A virtuális fürtök műveleteinek időtartama eltérő lehet – az alábbi értékek általában a meglévő telemetria-adatokon alapulnak:

- **Virtuális fürt létrehozása**: ez egy szinkron lépés a példányok kezelési műveleteihez. **a műveletek 90%-a 4 órán belül befejeződik**.
- **Virtuális fürtök átméretezése (bővítés vagy zsugorodás)**: a bővítés egy szinkron lépés, míg a zsugorodás aszinkron módon történik (a példányok kezelési műveleteinek időtartamára való hatás nélkül). a **fürt bővítésének 90%-a kevesebb, mint 2,5 óra**.
- **Virtuális fürt törlése**: a törlés egy aszinkron lépés, de [manuálisan is elindítható](virtual-cluster-delete.md) egy üres virtuális fürtön, ebben az esetben a rendszer szinkron módon hajtja végre. a **virtuális fürtök törlésének 90%-a 1,5 órában fejeződik be**.

Emellett a példányok kezelése az üzemeltetett adatbázisokra vonatkozó egyik műveletet is tartalmazhatja, ami hosszabb időtartamot eredményez:

- **Adatbázisfájlok csatolása az Azure Storage-ból**: ez egy szinkron lépés, például számítás (virtuális mag) vagy a általános célú szolgáltatási szinten felfelé vagy lefelé történő méretezés. a **műveletek 90%-a 5 percen belül befejeződik**.
- **Always On rendelkezésre állási csoport**beültetése: ez egy szinkron lépés, például a számítási (virtuális mag) vagy a tárolási skálázás a üzletileg kritikus szolgáltatási rétegben, valamint a szolgáltatási rétegnek a általános célúról a üzletileg kritikusra (vagy fordítva) való megváltoztatására. A művelet időtartama a teljes adatbázis méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók száma) arányos. Az adatbázis-tevékenység egy példány frissítésekor jelentős variancia bevezetését jelentheti a teljes időtartamra. a **műveletek 90%-a 220 GB/óra vagy újabb**.

A következő táblázat összefoglalja a műveleteket és a jellemző általános időtartamokat:

|Kategória  |Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|---------|
|**Üzembe helyezés** |Első példány egy üres alhálózaton|Virtuális fürt létrehozása|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |Egy másik hardver generációjának első példánya egy nem üres alhálózatban (például az első gen 5 példány egy 4. generációs példánnyal rendelkező alhálózatban)|Virtuális fürt létrehozása *|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |4 virtuális mag első példányának létrehozása üres vagy nem üres alhálózatban|Virtuális fürt létrehozása * *|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |A következő példány létrehozása a nem üres alhálózaton belül (2., 3. stb. példány)|Virtuális fürtök átméretezése|a műveletek 90%-a befejezve 2,5 órában|
|**Frissítés** |Példány tulajdonságainak módosítása (rendszergazdai jelszó, Azure AD-bejelentkezés, Azure Hybrid Benefit jelző)|N/A|Legfeljebb 1 perc|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú szolgáltatási szintet)|Adatbázisfájlok csatolása|a műveletek 90%-a befejeződik 5 percen belül|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a befejezve 2,5 órában|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|Frissítés |Példány leskálázása 4 virtuális mag (általános célú)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 4 óra 5 percben * *|
|Frissítés |Példány leskálázása 4 virtuális mag (üzletileg kritikus)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>-Always On rendelkezésre állási csoport bevetése|a műveletek 90%-a 4 órán belül befejeződik + az összes adatbázis kivetéséhez szükséges idő (220 GB/óra)|
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|**Törlés**|Példány törlése|Az összes adatbázis biztonsági másolatának naplózása|90%-os művelet akár 1 percenként is befejeződik.<br>Megjegyzés: Ha az alhálózat utolsó példánya törölve lett, a művelet a virtuális fürt törlését 12 óra elteltével * * * fogja ütemezni. * * *|
|Törlés|Virtuális fürt törlése (felhasználó által kezdeményezett művelet)|Virtuális fürt törlése|a műveletek 90%-a befejezve legfeljebb 1,5 óra|

\*A virtuális fürt hardveres generálásra épül.

\*\*A 4 virtuális mag beállítás a 2019-es verzióban jelent meg, és egy új virtuális fürt verzióját igényli. Ha a cél alhálózatban olyan példányok voltak, amelyek mind a 12. június előtt jöttek létre, a rendszer automatikusan telepíti az új virtuális fürtöt a 4 virtuális mag-példányok üzemeltetésére.

\*\*\*12 óra az aktuális konfiguráció, de a jövőben változhat, így nem kell nehéz függőséget okozni. Ha korábban törölni kell egy virtuális fürtöt (például az alhálózat felszabadításához), tekintse meg az [alhálózat törlése az Azure SQL felügyelt példányának törlése után](virtual-cluster-delete.md)című témakört.

### <a name="instance-availability-during-management-operations"></a>Példány rendelkezésre állása a felügyeleti műveletek során

Az SQL felügyelt példánya nem érhető el az ügyfélalkalmazások számára az üzembe helyezési és törlési műveletek során.

A frissítési műveletek során a felügyelt SQL-példány elérhető, kivéve a frissítés végén előforduló feladatátvétel által okozott rövid állásidőt. A [gyorsított adatbázis-helyreállításnak](../accelerated-database-recovery.md)köszönhetően általában akár 10 másodpercig is eltarthat.

> [!IMPORTANT]
> Nem ajánlott az Azure SQL felügyelt példányának számítási vagy tárolási méretezése, illetve a szolgáltatási réteg egyidejű módosítása a hosszan futó tranzakciók (adatimportálás, adatfeldolgozási feladatok, indexek újraépítése stb.) alapján. A művelet végén elvégezhető adatbázis-feladatátvétel megszakítja az összes folyamatban lévő tranzakciót.

### <a name="management-operations-cross-impact"></a>A kezelési műveletek hatásainak következményei

Az SQL felügyelt példányok kezelési műveletei befolyásolhatják az ugyanazon a virtuális fürtön belül elhelyezett példányok más felügyeleti műveleteit is. Ide tartoznak a következők:

- A virtuális fürtben a **hosszú ideig futó visszaállítási műveletek** más példány-létrehozási vagy skálázási műveletet fognak tartani ugyanabban az alhálózatban.<br/>**Példa:** ha hosszú ideig futó visszaállítási művelettel rendelkezik, és a kérelem létrehozása vagy méretezése ugyanabban az alhálózatban történik, akkor ez a kérés hosszabb ideig tart, mert a folytatás előtt megvárja, amíg a visszaállítási művelet befejeződik.
    
- A **következő példány-létrehozási vagy skálázási** művelet a virtuális fürt átméretezését kezdeményező, korábban kezdeményezett példány-létrehozási vagy-példány-méretezési művelettel rendelkezik.<br/>**Példa:** ha ugyanazon az alhálózaton több létrehozási és/vagy méretezési kérelem van ugyanabban a virtuális fürtben, és az egyikük a virtuális fürt átméretezését kezdeményezi, az összes olyan kérelem, amely 5 + perccel azután lett elküldve, hogy a virtuális fürt átméretezése meghaladhatja a várt időtartamot

- Az **5 perces ablakban elküldött műveletek létrehozása/méretezése** párhuzamosan történik.<br/>**Példa:** A rendszer csak egy virtuális fürt átméretezését hajtja végre az 5 perces ablakban elküldött összes művelethez (az első műveleti kérelem végrehajtásának pillanatától számítva). Abban az esetben, ha az első elküldés után több mint 5 perccel több kérelem van elküldve, a rendszer a végrehajtás megkezdése előtt megvárja, hogy a virtuális fürt átméretezése befejeződjön.

> [!IMPORTANT]
> A folyamatban lévő másik művelet miatt megtartott felügyeleti műveletek automatikusan folytatódnak, amikor a feltételek teljesülnek. Nincs szükség felhasználói beavatkozásra az átmenetileg szüneteltetett felügyeleti művelet folytatásához.

### <a name="canceling-management-operations"></a>Felügyeleti műveletek megszakítása

Az alábbi táblázat összefoglalja az adott felügyeleti műveletek és a jellemző általános időtartamok törlésének képességét:

Kategória  |Művelet  |Kampány  |Becsült megszakítási időtartam  |
|---------|---------|---------|---------|
|Üzembe helyezés |Példány létrehozása |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú) |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a befejeződik 5 percen belül |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú) |Igen |a műveletek 90%-a befejeződik 5 percen belül |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a befejeződik 5 percen belül |
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva) |Igen |a műveletek 90%-a befejeződik 5 percen belül |
|Törlés |Példány törlése |Nem |  |
|Törlés |Virtuális fürt törlése (felhasználó által kezdeményezett művelet) |Nem |  |

A kezelési művelet megszakításához lépjen az Áttekintés panelre, és kattintson a folyamatban lévő értesítési mezőre. A jobb oldalon megjelenik a folyamatban lévő művelettel rendelkező képernyő, és a művelet megszakítására szolgáló gomb jelenik meg. Az első kattintás után a rendszer kérni fogja, hogy kattintson újra, és erősítse meg, hogy meg kívánja szüntetni a műveletet.

[![Művelet megszakítása](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

A megszakítási kérelem elküldését és feldolgozását követően értesítést kap, ha a megszakítás beküldése sikeres volt.

A sikeres megszakítás esetén a felügyeleti művelet néhány percen belül megszakad, ami hibát eredményez.

![művelet eredményének megszakítása](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Ha a megszakítási kérelem meghiúsul, vagy a Mégse gomb nem aktív, az azt jelenti, hogy a felügyeleti művelet nem megszakítható állapotba került, és néhány percen belül befejeződik. A felügyeleti művelet addig folytatja a végrehajtást, amíg be nem fejeződik.

> [!IMPORTANT]
> A megszakítási művelet jelenleg csak a portálon támogatott.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQL felügyelt példánya az Azure-felhő és a SQL Server adatbázismotor fejlett biztonsági funkcióit tartalmazza.

### <a name="security-isolation"></a>Biztonság elkülönítése

Az SQL felügyelt példánya további biztonsági elkülönítést biztosít az Azure-felhőben található többi bérlőtől. A biztonsági elkülönítés magában foglalja a következőket:

- [Natív virtuális hálózat megvalósítása](connectivity-architecture-overview.md) és a helyszíni környezethez való kapcsolódás az Azure Express Route vagy a VPN Gateway használatával.
- Alapértelmezett telepítés esetén az SQL-végpont csak egy magánhálózati IP-címen keresztül érhető el, ami lehetővé teszi a biztonságos kapcsolódást a privát Azure-vagy hibrid hálózatokból.
- Önálló bérlő dedikált mögöttes infrastruktúrával (számítás, tárolás).

Az alábbi ábra az alkalmazások különböző csatlakozási lehetőségeit ismerteti:

![magas rendelkezésre állás](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Ha többet szeretne megtudni a VNet-integrációval és a hálózatkezelési házirendek kényszerítésével kapcsolatban az alhálózat szintjén, tekintse meg a [felügyelt példányok VNet-architektúráját](connectivity-architecture-overview.md) és [az alkalmazás felügyelt példányhoz való csatlakozását](connect-application-instance.md)

> [!IMPORTANT]
> Helyezzen több felügyelt példányt ugyanabba az alhálózatba, bárhol is legyen a biztonsági követelményei, mivel ez további előnyöket biztosít. Az ugyanabban az alhálózatban lévő Collocating-példányok jelentősen leegyszerűsítik a hálózati infrastruktúra karbantartását, és csökkentik a példányok kiépítésének idejét, mivel a hosszú kiépítési időtartam az első felügyelt példánynak az alhálózaton való üzembe helyezésének költségeit veszi alapul.

### <a name="security-features"></a>Biztonsági funkciók

Az Azure SQL felügyelt példánya olyan speciális biztonsági funkciókat kínál, amelyek az adatai védelmére használhatók.

- A [felügyelt SQL-példányok naplózása](auditing-configure.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba helyezett naplófájlba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenység megértésében, valamint az üzleti problémákat vagy a feltételezett biztonsági szabálysértéseket jelezhető eltérések és rendellenességek megismerésében.
- Adattitkosítás mozgás közben – az SQL által felügyelt példányok a Transport Layer Security használatával titkosítják a mozgásban lévő adatvédelmet. A Transport Layer Security mellett az SQL felügyelt példánya a bizalmas adatok repülés közbeni védelmét, a REST-t és a lekérdezések feldolgozását [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használatával biztosítja. Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például a Always Encrypted a hitelkártyák számát a rendszer az adatbázisban mindig titkosítja, még a lekérdezés feldolgozása során is, amely lehetővé teszi az olyan jogosult munkatársak vagy alkalmazások számára, amelyek az adatok feldolgozását végzik.
- A komplex [veszélyforrások elleni védelem](threat-detection-configure.md) kiegészíti a [naplózást](auditing-configure.md) azáltal, hogy egy további, a szolgáltatásba épített biztonsági információs réteget biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az adatbázisok eléréséhez vagy kiaknázásához. A rendszer riasztást küld a gyanús tevékenységekről, a lehetséges sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A komplex veszélyforrások elleni védelemre vonatkozó riasztások a [Azure Security Centerról](https://azure.microsoft.com/services/security-center/) tekinthetők meg, és a gyanús tevékenységek részletes ismertetését és a fenyegetés kivizsgálására és elhárítására vonatkozó javaslatokat nyújtanak.  
- A [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. A dinamikus adatmaszkolás segít megakadályozni a bizalmas adatok jogosulatlan elérését azáltal, hogy kijelöli, hogy a bizalmas adatok mekkora részét mutatják ki az alkalmazási rétegre gyakorolt minimális hatás. Ez egy olyan házirend-alapú biztonsági funkció, amely elrejti a bizalmas adatokat a kijelölt adatbázis-mezőkön lévő lekérdezés eredmény-készletében, míg az adatbázisban lévő adatok nem változnak.
- A [sor szintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy az adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzőinek (például csoporttagság vagy végrehajtási környezet) alapján szabályozza. A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Tegyük fel például, hogy a dolgozók csak a saját részlegéhez tartozó adatsorokhoz férhessenek hozzá, vagy csak a releváns adatokat korlátozzák.
- Az [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a felügyelt SQL-példányok adatfájljait titkosítja, más néven az inaktív adatok titkosítását. A TDE az adatok és naplófájlok valós idejű I/O-titkosítását és visszafejtését hajtja végre. A titkosítás egy adatbázis-titkosítási kulcsot (ADATTITKOSÍTÁSI kulcsot) használ, amely az adatbázis-rendszerindítási rekordban van tárolva a helyreállítás során. A felügyelt példányokban lévő összes adatbázist transzparens adattitkosítással lehet védelemmel ellátni. A TDE SQL Server bevált titkosítási funkciója, amelyre számos megfelelőségi szabványnak szüksége van a tárolási adathordozók lopása elleni védelemhez.

A titkosított adatbázisok SQL felügyelt példányra történő áttelepítése a Azure Database Migration Service (DMS) vagy a natív visszaállítás használatával támogatott. Ha egy titkosított adatbázist natív visszaállítással szeretne áttelepíteni, a meglévő TDE-tanúsítvány áttelepítése a SQL Server példányból egy SQL-felügyelt példányra szükséges lépés. További információ az áttelepítési lehetőségekről: [SQL Server Migrálás az SQL felügyelt példányára](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory integráció

Az SQL felügyelt példánya támogatja a hagyományos SQL Server adatbázismotor-bejelentkezéseket és a Azure Active Directory (Azure AD) szolgáltatással integrált bejelentkezéseket. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) (**nyilvános előzetes**verzió) a helyszíni adatbázis-bejelentkezések Azure Cloud-verziója, amelyet a helyszíni környezetben használ. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) lehetővé teszik, hogy az Azure Active Directory-bérlőtől származó felhasználókat és csoportokat az igaz példányokra vonatkozó hatókörű rendszerbiztonsági tagként adja meg, amely képes bármely példány szintű művelet végrehajtására, beleértve az azonos felügyelt példányon belüli több adatbázisra kiterjedő lekérdezéseket is.

Új szintaxist vezetünk be az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozásához **a külső szolgáltatótól**. A szintaxissal kapcsolatos további információkért tekintse meg a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Bejelentkezés létrehozása</a>című cikket, és tekintse át az [SQL felügyelt példányának Azure Active Directory-rendszergazdájának kiépítése](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) című cikkét.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL felügyelt példányai lehetővé teszik az adatbázis-felhasználó és más Microsoft-szolgáltatások identitásának központilag kezelését [Azure Active Directory integrációval](../database/authentication-aad-overview.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](../database/authentication-mfa-ssms-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="authentication"></a>Hitelesítés

Az SQL felügyelt példányának hitelesítése arra utal, hogy a felhasználók hogyan igazolják identitásukat az adatbázishoz való csatlakozáskor. A felügyelt SQL-példány két típusú hitelesítést támogat:  

- **SQL-hitelesítés**:

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ.
- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ, és felügyelt és integrált tartományok esetén támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználók mit tehetnek egy adatbázisban az Azure SQL felügyelt példányain, és a felhasználói fiók adatbázis-szerepkör-tagsága és az objektum-szintű engedélyek vezérlik. A felügyelt SQL-példányok ugyanazokkal az engedélyezési képességekkel rendelkeznek, mint a SQL Server 2017.

## <a name="database-migration"></a>Adatbázis-áttelepítés

Az SQL felügyelt példánya a helyszíni vagy IaaS adatbázis-megvalósításokból származó tömeges adatbázis-áttelepítés felhasználói forgatókönyveit célozza meg. Az SQL felügyelt példánya több adatbázis-áttelepítési lehetőséget is támogat:

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás  

Az áttelepítési módszer az SQL-alapú biztonsági mentéseket használja az Azure Blob Storage-ba. Az Azure Storage-blobban tárolt biztonsági másolatok közvetlenül visszaállíthatók egy SQL felügyelt példányra a [T-SQL Restore parancs](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)használatával.

- A Wide World Importers – standard adatbázis biztonságimásolat-fájljának visszaállítását bemutató rövid útmutató: [biztonságimásolat-fájl visszaállítása felügyelt példányra](restore-sample-database-quickstart.md). Ez a rövid útmutató azt mutatja be, hogy fel kell töltenie egy biztonságimásolat-fájlt az Azure Blob Storage-ba, és biztonságossá kell tennie a közös hozzáférésű aláírás (SAS) kulcsával.
- További információ az URL-címről való visszaállításról: [NATÍV visszaállítás az URL-](migrate-to-instance-from-sql-server.md#native-restore-from-url)címről.

> [!IMPORTANT]
> A felügyelt SQL-példányokról csak egy másik SQL felügyelt példányra lehet visszaállítani a biztonsági másolatokat. Nem állíthatók vissza SQL Server példányra vagy Azure SQL Database.

### <a name="data-migration-service"></a>Data Migration Service

A Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti azokat a feladatokat, amelyek szükségesek a meglévő harmadik fél áttelepítéséhez, valamint SQL Server adatbázisok Azure SQL Database, Azure SQL felügyelt példányhoz és SQL Server az Azure-beli virtuális gépen. Lásd: a helyszíni [adatbázis migrálása SQL felügyelt példányra a DMS használatával](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

Az SQL felügyelt példányának célja, hogy a szakaszos kiadási csomagon keresztül 100%-os felülettel kompatibilis legyen a legújabb SQL Server verziójával. A szolgáltatások és az összehasonlítások listájáért lásd: az [SQL felügyelt példány funkcióinak összehasonlítása](../database/features-comparison.md), valamint az SQL felügyelt példányok és a SQL Server közötti t-SQL-különbségek listája: az [SQL felügyelt példányok t-SQL-különbözetei SQL Server](transact-sql-tsql-differences-sql-server.md).

Az SQL felügyelt példánya támogatja az SQL 2008-adatbázisok visszamenőleges kompatibilitását. Az SQL 2005 adatbázis-kiszolgálókról történő közvetlen áttelepítés támogatott, az áttelepített SQL 2005-adatbázisok kompatibilitási szintje pedig az SQL 2008-ra frissül.
  
Az alábbi ábra az SQL felügyelt példányának felületi kompatibilitását ismerteti:  

![áttelepítési](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-on-premises-and-sql-managed-instance"></a>A legfontosabb különbségek a helyszíni és az SQL felügyelt példányon

Az SQL felügyelt példányának előnye, hogy mindig naprakészek a felhőben, ami azt jelenti, hogy SQL Server egyes funkciói elavultak, kivonhatók vagy alternatívák lehetnek. Bizonyos esetekben szükség van arra, hogy az eszközök tisztában legyenek azzal, hogy egy adott funkció némileg eltérő módon működik, vagy a szolgáltatás olyan környezetben fut, amely nem teljes mértékben szabályozható.

Néhány kulcsfontosságú különbség:

- A magas rendelkezésre állás az [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)hasonló technológiával beépített és előre konfigurált.
- Csak automatikus biztonsági mentések és időpontok visszaállítása lehetséges. Az ügyfél olyan `copy-only` biztonsági mentéseket kezdeményezhet, amelyek nem akadályozzák az automatikus biztonsági mentési láncot.
- A teljes fizikai elérési utak megadása nem támogatott, így az összes vonatkozó forgatókönyvet másképp kell támogatni: az adatbázis visszaállítása nem támogatja az ÁTHELYEZÉSt, az adatbázis létrehozása nem teszi lehetővé a fizikai elérési utak használatát, BULK INSERT csak az Azure-Blobokkal működik, stb.
- Az SQL felügyelt példánya támogatja az [Azure ad-hitelesítést](../database/authentication-aad-overview.md) a Windows-hitelesítéshez a felhő alternatívájaként.
- Az SQL felügyelt példánya automatikusan kezeli a memóriában tárolt OLTP objektumokat tartalmazó adatbázisok XTP-fájlcsoportja és-fájljait.
- Az SQL felügyelt példánya támogatja a SQL Server Integration Services (SSIS) szolgáltatást, és képes tárolni a SSIS-csomagokat tároló SSIS-katalógust (SSISDB), de a Azure Data Factory (ADF) felügyelt Azure-SSIS Integration Runtime (IR) hajtja végre. lásd: [Azure-SSIS IR létrehozása ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). A SSIS funkcióinak összehasonlítását lásd: [SQL Database összehasonlítása az SQL felügyelt példányával](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Felügyeleti funkciók

A felügyelt SQL-példány lehetővé teszi, hogy a rendszergazda kevesebb időt fordítson a felügyeleti feladatokra, mert az SQL felügyelt példány szolgáltatás vagy elvégzi azokat az Ön számára, vagy nagy mértékben leegyszerűsíti ezeket Például az [operációs rendszer/RDBMS telepítése és javítása](../database/high-availability-sla.md), a [dinamikus példányok átméretezése és konfigurálása](../database/single-database-scale.md), a [biztonsági másolatok](../database/automated-backups-overview.md), az [adatbázis-replikáció](replication-between-two-instances-configure-tutorial.md) (beleértve a rendszeradatbázisokat is), a [magas rendelkezésre állás konfigurálása](../database/high-availability-sla.md), valamint az állapot-és [teljesítmény-figyelési](../../azure-monitor/insights/azure-sql.md) adatfolyamok konfigurálása.

További információkért tekintse meg [a támogatott és nem támogatott SQL felügyelt példányok funkcióinak listáját](../database/features-comparison.md), valamint a [T-SQL-különbségeket az SQL felügyelt példánya és a SQL Server között](transact-sql-tsql-differences-sql-server.md)

### <a name="programmatically-identify-a-managed-instance"></a>Felügyelt példány programozott azonosítása

A következő táblázat több, a Transact-SQL szolgáltatáson keresztül elérhető tulajdonságot mutat be, amelyek segítségével észlelhető, hogy az alkalmazás működik-e az SQL felügyelt példányával, és hogy fontos tulajdonságokat kér le.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez az érték ugyanaz, mint a SQL Databaseban. Ez **nem** jelzi az SQL Engine 12-es verzióját (SQL Server 2014). A felügyelt SQL-példányok mindig a legújabb, stabil SQL Engine-verziót futtatják, ami egyenlő vagy magasabb, mint a legújabb elérhető RTM-verzió SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez az érték ugyanaz, mint a SQL Databaseban.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték egyedileg azonosítja a felügyelt példányt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Teljes példány DNS-neve a következő formátumban: `<instanceName>` . `<dnsPrefix>` . a database.windows.net, ahol az az `<instanceName>` ügyfél által megadott név, míg a `<dnsPrefix>` neve automatikusan létrejön a globális DNS-név egyediségét garantáló név ("wcus17662feb9ce98"), például:.|Példa: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>További lépések

- Az első SQL-beli felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy SQL felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból történő visszaállítása: [SQL felügyelt példány létrehozása](instance-create-quickstart.md).
- Az áttelepítésre szolgáló Azure Database Migration Service (DMS) használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése a DMS használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
- Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
