---
title: Mi az az Azure SQL felügyelt példánya?
description: Ismerje meg, hogy az Azure SQL felügyelt példányai közel 100%-os kompatibilitást biztosítanak a legújabb SQL Server (Enterprise Edition) adatbázis-motorral
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
ms.openlocfilehash: 2f7422d01b2058cafed33c9d10118f78d35727df
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337784"
---
# <a name="what-is-azure-sql-managed-instance"></a>Mi az az Azure SQL felügyelt példánya?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL termékcsalád részét képező Azure SQL felügyelt példány az intelligens, méretezhető felhőalapú adatbázis-szolgáltatás, amely a legszélesebb körben SQL Server adatbázismotor kompatibilitását ötvözi a teljes körűen felügyelt és örökzöld platform szolgáltatásként nyújtott előnyökkel. Az SQL felügyelt példánya közel 100%-os kompatibilitást biztosít a legújabb SQL Server (Enterprise Edition) adatbázis-motorral, amely egy natív [virtuális hálózat (VNet)](../../virtual-network/virtual-networks-overview.md) megvalósítását biztosítja, amely az általános biztonsági kérdésekre és a meglévő SQL Server ügyfeleknek kedvező [üzleti modellre](https://azure.microsoft.com/pricing/details/sql-database/) épül. Az SQL felügyelt példánya lehetővé teszi a meglévő SQL Server ügyfelek számára, hogy a felhőbe a helyszíni alkalmazásaikat minimális alkalmazás-és adatbázis-módosításokkal emelje át. Ugyanakkor a felügyelt SQL-példányok megőrzik az összes, a felügyeleti terhelést [és a TCO](../database/high-availability-sla.md)-t drasztikusan csökkentő (automatikus javítás és verziófrissítés, [automatikus biztonsági mentés](../database/automated-backups-overview.md)) lehetőséget.

> [!IMPORTANT]
> Azoknak a régióknak a listájáért, amelyeken jelenleg elérhető az SQL felügyelt példány, tekintse meg a [támogatott régiók](resource-limits.md#supported-regions)című témakört.

Az alábbi ábra az SQL felügyelt példányának főbb funkcióit vázolja fel:

![A legfontosabb jellemzők](./media/sql-managed-instance-paas-overview/key-features.png)

Az Azure SQL felügyelt példánya olyan ügyfelek számára készült, akik nagy mennyiségű alkalmazást kívánnak áttelepíteni egy helyszíni vagy IaaS, önálló vagy független szoftvergyártó által biztosított környezetből egy teljes körűen felügyelt, kihelyezett Felhőbeli környezetbe, a lehető legkevesebb migrációs erőfeszítéssel. A teljes mértékben automatizált [Azure-adatáttelepítési szolgáltatással](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)az ügyfelek megemelik és áthelyezhetik meglévő SQL Server-példányát az SQL felügyelt példányára, amely a SQL Serverekkel való kompatibilitást és a natív VNet-támogatással rendelkező vásárlói példányok elkülönítését biztosítja  A frissítési garanciával lehetősége van arra, hogy az SQL felügyelt példányain a meglévő licenceket a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával, kedvezményes díjszabással cserélje le. A felügyelt SQL-példány a legjobb áttelepítési cél a felhőben olyan SQL Server-példányok esetén, amelyek magas biztonságot és sokoldalú programozható felületet igényelnek.

## <a name="key-features-and-capabilities"></a>Főbb funkciók és képességek

Az SQL felügyelt példánya a Azure SQL Database és az SQL Server adatbázismotor egyik legjobb funkcióját ötvözi.

> [!IMPORTANT]
> Az SQL felügyelt példánya a SQL Server legújabb verziójának összes szolgáltatásával fut, beleértve az online műveleteket, az automatikus terv javításait és az egyéb vállalati teljesítmény-fejlesztéseket. Az elérhető funkciók összehasonlítása a [funkció-összehasonlításban: az Azure SQL felügyelt példánya](../database/features-comparison.md)és a SQL Server.

| **Pásti előnyei** | **Üzletmenet folytonossága** |
| --- | --- |
|Nincs hardveres vásárlás és felügyelet <br>Nincs felügyeleti terhelés a mögöttes infrastruktúra kezeléséhez <br>Gyors kiépítés és a szolgáltatás skálázása <br>Automatikus javítás és verziófrissítés <br>Integráció más Pásti adatszolgáltatásokkal |99,99%-os rendelkezésre állási SLA  <br>Beépített [magas rendelkezésre állás](../database/high-availability-sla.md) <br>[Automatizált biztonsági mentéssel](../database/automated-backups-overview.md) védett adatvédelem <br>Ügyfél által konfigurálható biztonsági másolatok megőrzési ideje <br>Felhasználó által kezdeményezett [biztonsági másolatok](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időponthoz tartozó adatbázis-visszaállítási](../database/recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Felügyelet**|
|Elszigetelt környezet ([VNet-integráció](connectivity-architecture-overview.md), egybérlős szolgáltatás, dedikált számítás és tárolás) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure ad) hitelesítés](../database/authentication-aad-overview.md), egyszeri bejelentkezés támogatása <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a>  <br>Megfelel a megfelelőségi szabványoknak, mint a Azure SQL Database <br>[SQL-naplózás](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager API a szolgáltatások kiosztásának és méretezésének automatizálásához <br>A szolgáltatás kézi üzembe helyezéséhez és méretezéséhez Azure Portal funkció <br>Data Migration Service

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure megfelelőségi ajánlatait](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), ahol megtalálhatja az SQL felügyelt példányok megfelelőségi tanúsítások legfrissebb listáját, amely a **SQL Database**alatt található.

Az SQL felügyelt példány legfontosabb funkciói az alábbi táblázatban láthatók:

|Funkció | Leírás|
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
| Beépített jelentéskészítési szolgáltatás (SSRS) | Nem – használjon [Power bi](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) többoldalas jelentéseket, vagy az SSRS-t egy Azure-beli virtuális gépen üzemelteti. A felügyelt SQL-példányok nem futtathatják az SSRS szolgáltatást, hanem az SSRS 2019 Catalog-adatbázisokat a külső jelentéskészítő kiszolgálók számára SQL Server hitelesítés használatával. |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

Az SQL felügyelt példány [virtuális mag-alapú vásárlási modellje](../database/service-tiers-vcore.md) rugalmasságot, irányítást, átláthatóságot és egyszerű módszert biztosít a helyszíni számítási feladatok követelményeinek a felhőbe való lefordításához. Ez a modell lehetővé teszi a számítási feladatok, a memória és a tárterület módosítását a munkaterhelés igényei alapján. A virtuális mag modell akár 55%-os megtakarítást is igénybe vehet, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server.

A virtuális mag modellben a hardver generációi közül választhat.

- A **Gen4** logikai CPU-k Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokkal, csatlakoztatott SSD-k, fizikai magok, 7 GB RAM/mag, valamint 8 és 24 virtuális mag közötti számítási méreteken alapulnak.
- A **Gen5** logikai CPU-k Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok, gyors NVMe SSD, Hyper-threaded logikai mag és a 4 és 80 közötti számítási méretek alapján működnek.

További információk a hardveres generációk közötti különbségről az [SQL felügyelt példányának erőforrás-korlátaiban](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Szolgáltatásszintek

A felügyelt SQL-példány két szolgáltatási rétegben érhető el:

- **Általános célú**: olyan alkalmazásokhoz tervezve, amelyek jellemzően teljesítménybeli és I/O-késleltetési követelményekkel rendelkeznek.
- **Üzleti szempontból kritikus**: az alacsony I/O-késési követelményekkel rendelkező alkalmazások számára lett kialakítva, és minimális hatással van a mögöttes karbantartási műveletekre a munkaterhelés esetében.

Mindkét szolgáltatási szinten 99,99%-os rendelkezésre állást garantálunk, és lehetővé teszi a tárolási méret és a számítási kapacitás egymástól független kiválasztását. További információ az Azure SQL felügyelt példány magas rendelkezésre állású architektúrával kapcsolatban: [magas rendelkezésre állás és Azure SQL felügyelt példány](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>általános célú szolgáltatási szintet

Az alábbi lista ismerteti a általános célú szolgáltatási szintje főbb jellemzőit:

- Az üzleti alkalmazások többségét jellemző teljesítménybeli követelményekkel tervezték
- Nagy teljesítményű Azure Blob Storage (8 TB)
- Beépített [magas rendelkezésre állás](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) a megbízható Azure Blob Storage és az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) alapján

További információ: [tárolási réteg a általános célú rétegben és a](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) [tárolási teljesítményre vonatkozó ajánlott eljárások és szempontok az SQL felügyelt példányaihoz (általános célú)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információ a szolgáltatási szintek közötti különbségről az [SQL felügyelt példányának erőforrás-korlátaiban](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>üzletileg kritikus szolgáltatási szintet

A üzletileg kritikus szolgáltatási szintet a nagy I/O-követelményekkel rendelkező alkalmazások számára építették. Ez a legnagyobb rugalmasságot nyújtja a hibáknak több elkülönített replika használatával.

Az alábbi lista a üzletileg kritikus szolgáltatási szintje főbb jellemzőit ismerteti:

- A legnagyobb teljesítménnyel és HA követelményekkel rendelkező üzleti alkalmazásokhoz tervezve
- A szolgáltatás villámgyors helyi SSD-tárolóval (1 TB-ig Gen4 és akár 4 TB-ig Gen5) is rendelkezik
- Beépített [magas rendelkezésre](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) állás az [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) és az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) alapján
- Beépített további [írásvédett adatbázis-replikák](../database/read-scale-out.md) , amelyek jelentéskészítésre és más írásvédett számítási feladatokra is használhatók
- [Memóriabeli OLTP](../in-memory-oltp-overview.md) , amely nagy teljesítményű követelményekkel használható munkaterheléshez  

További információ a szolgáltatási szintek közötti különbségekről az [SQL felügyelt példányok erőforrás-korlátaiban](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Felügyeleti műveletek

Az Azure SQL felügyelt példánya olyan felügyeleti műveleteket biztosít, amelyek segítségével automatikusan telepítheti az új felügyelt példányokat, frissítheti a példány tulajdonságait, és törölheti a példányokat, ha már nincs rá szükség. Ez a szakasz a felügyeleti műveletekkel és azok jellemző időtartamával kapcsolatos információkat tartalmaz.

Az Azure-beli [virtuális hálózatokon belüli központi telepítések](../../virtual-network/virtual-network-for-azure-services.md) támogatásához és az ügyfelek elkülönítésének és biztonságának biztosításához az SQL felügyelt példánya [virtuális fürtökre](connectivity-architecture-overview.md#high-level-connectivity-architecture)támaszkodik, amelyek az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét képviselik. Az üres alhálózat minden felügyelt példányának telepítése lényegében egy új virtuális fürt buildout eredményez.

A központilag telepített felügyelt példányok további műveletei hatással lehetnek a mögöttes virtuális fürtre is. Ez befolyásolja a felügyeleti műveletek időtartamát, mivel a további virtuális gépek üzembe helyezése olyan terheléssel jár, amelyet figyelembe kell venni, amikor új központi telepítéseket vagy frissítéseket tervez a meglévő felügyelt példányokra.

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása).
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló.
- Példány törlése.

Általában a virtuális fürtökön végrehajtott műveletek a leghosszabb időt jelentik. A virtuális fürtök műveleteinek időtartama változik – az alábbi értékek általában a meglévő telemetria-adatokon alapulnak:

- **Virtuális fürt létrehozása**: ez egy szinkron lépés a példányok kezelési műveleteihez. **a műveletek 90%-a 4 órán belül befejeződik**.
- **Virtuális fürtök átméretezése (bővítés vagy zsugorodás)**: a bővítés egy szinkron lépés, míg a zsugorodás aszinkron módon történik (a példányok kezelési műveleteinek időtartamára való hatás nélkül). a **fürt bővítésének 90%-a kevesebb, mint 2,5 óra**.
- **Virtuális fürt törlése**: a törlés egy aszinkron lépés, de [manuálisan is elindítható](virtual-cluster-delete.md) egy üres virtuális fürtön, ebben az esetben a rendszer szinkron módon hajtja végre. a **virtuális fürtök törlésének 90%-a 1,5 órában fejeződik be**.

Emellett a példányok kezelése az üzemeltetett adatbázisokra vonatkozó egyik műveletet is tartalmazhatja, ami hosszabb időtartamot eredményez:

- **Adatbázisfájlok csatolása az Azure Storage-ból**: ez egy szinkron lépés, például számítás (virtuális mag) vagy a általános célú szolgáltatási szinten felfelé vagy lefelé történő méretezés. a **műveletek 90%-a 5 percen belül befejeződik**.
- **Always On rendelkezésre állási csoport**beültetése: ez egy szinkron lépés, például a számítási (virtuális mag) vagy a tárolási skálázás a üzletileg kritikus szolgáltatási rétegben, valamint a szolgáltatási rétegnek a általános célúról a üzletileg kritikusra (vagy fordítva) való megváltoztatására. A művelet időtartama a teljes adatbázis méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók száma) arányos. Az adatbázis-tevékenység egy példány frissítésekor jelentős variancia bevezetését jelentheti a teljes időtartamra. a **műveletek 90%-a 220 GB/óra vagy újabb**.

A következő táblázat összefoglalja a műveleteket és a jellemző általános időtartamokat:

|Kategória  |Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|---------|
|**Üzembe helyezés** |Első példány egy üres alhálózaton|Virtuális fürt létrehozása|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |Egy másik hardver generációjának első példánya egy nem üres alhálózatban (például az első gen 5 példány egy 4. generációs példánnyal rendelkező alhálózatban)|Virtuális fürt létrehozása *|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |4 virtuális mag első példányának létrehozása üres vagy nem üres alhálózatban|Virtuális fürt létrehozása * *|a műveletek 90%-a 4 órán belül befejeződik.|
|Üzembe helyezés |A következő példány létrehozása a nem üres alhálózaton belül (2., 3. stb. példány)|Virtuális fürtök átméretezése|a műveletek 90%-a 2,5 órában fejeződik be.|
|**Frissítés** |Példány tulajdonságainak módosítása (rendszergazdai jelszó, Azure AD-bejelentkezés, Azure Hybrid Benefit jelző)|N/A|Legfeljebb 1 perc.|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú szolgáltatási szintet)|Adatbázisfájlok csatolása|a műveletek 90%-a 5 percen belül befejeződik.|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 2,5 órában fejeződik be.|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|Frissítés |Példány leskálázása 4 virtuális mag (általános célú)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 4 óra 5 perc alatt fejeződik be. * *|
|Frissítés |Példány leskálázása 4 virtuális mag (üzletileg kritikus)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>-Always On rendelkezésre állási csoport bevetése|a műveletek 90%-a 4 órán belül befejeződik + az összes adatbázis kivetéséhez szükséges idő (220 GB/óra).|
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-a, 2,5 óra + idő alatt az összes adatbázist (220 GB/óra) kell befejeznie.|
|**Törlés**|Példány törlése|Az összes adatbázis biztonsági másolatának naplózása|90%-os művelet akár 1 percenként is befejeződik.<br>Megjegyzés: Ha az alhálózat utolsó példánya törölve lett, a művelet 12 óra elteltével a virtuális fürt törlését fogja ütemezni. * * *|
|Törlés|Virtuális fürt törlése (felhasználó által kezdeményezett művelet)|Virtuális fürt törlése|a műveletek 90%-a akár 1,5 óráig is befejeződik.|

\*A virtuális fürt hardveres generálásra épül.

\*\*A 4 virtuális mag beállítás a 2019-es verzióban jelent meg, és egy új virtuális fürt verzióját igényli. Ha a cél alhálózatban olyan példányok voltak, amelyek mind a 12. június előtt jöttek létre, a rendszer automatikusan telepíti az új virtuális fürtöt a 4 virtuális mag-példányok üzemeltetésére.

\*\*\*12 óra az aktuális konfiguráció, de a jövőben változhat, így nem kell nehéz függőséget okozni. Ha korábban törölni kell egy virtuális fürtöt (például az alhálózat felszabadításához), tekintse meg az [alhálózat törlése a felügyelt példány törlése után](virtual-cluster-delete.md)című témakört.

### <a name="instance-availability-during-management-operations"></a>Példány rendelkezésre állása a felügyeleti műveletek során

Az SQL felügyelt példánya nem érhető el az ügyfélalkalmazások számára az üzembe helyezési és törlési műveletek során.

Az SQL felügyelt példánya a frissítési műveletek során elérhető, kivéve a frissítés végén előforduló feladatátvétel által okozott rövid állásidőt. A [gyorsított adatbázis-helyreállításnak](../accelerated-database-recovery.md)köszönhetően általában akár 10 másodpercig is eltarthat.

> [!IMPORTANT]
> A felügyelt Azure SQL-példányok számításának vagy tárolásának méretezése nem ajánlott, vagy a szolgáltatási réteg nem módosítható egyszerre a hosszan futó tranzakciók (adatimportálás, adatfeldolgozási feladatok, indexek újraépítése stb.) alapján. A művelet végén elvégezhető adatbázis-feladatátvétel megszakítja az összes folyamatban lévő tranzakciót.

### <a name="management-operations-cross-impact"></a>A kezelési műveletek hatásainak következményei

A felügyelt példányokon a felügyeleti műveletek befolyásolhatják az azonos virtuális fürtön belül elhelyezett példányok más felügyeleti műveleteit. Ez az alábbiakat foglalja magában:

- A virtuális fürtben a **hosszan futó visszaállítási műveletek** más példány-létrehozási vagy skálázási műveleteket fognak tartani ugyanabban az alhálózatban.<br/>**Példa:** Ha van egy hosszú ideig futó visszaállítási művelet, és létezik egy létrehozási vagy méretezési kérelem ugyanabban az alhálózatban, akkor ez a kérés hosszabb ideig tart, mert a folytatás előtt megvárja, amíg a visszaállítási művelet befejeződik.
    
- A rendszer **egy későbbi példány-létrehozási vagy skálázási** műveletet tart fenn egy korábban kezdeményezett példány-létrehozási vagy-példány-méretezéssel, amely a virtuális fürt átméretezését kezdeményezte.<br/>**Példa:** Ha ugyanazon az alhálózaton több létrehozási és/vagy méretezési kérelem is található ugyanazon a virtuális fürtön belül, és az egyikük egy virtuális fürt átméretezését kezdeményezi, akkor a virtuális fürt átméretezését kérő összes kérelem 5 + perccel azután lesz elküldve, hogy a rendszer a folytatás előtt megvárja az átméretezés befejezését.

- Az **5 perces ablakban elküldött műveletek létrehozása/méretezése** párhuzamosan történik.<br/>**Példa:** A rendszer csak egy virtuális fürt átméretezését hajtja végre az 5 perces ablakban elküldött összes művelethez (az első művelet kérésének végrehajtásának pillanatától számítva). Ha egy másik kérelmet több mint 5 perccel az első elküldése után küld el, a rendszer megvárja, amíg a virtuális fürt átméretezése megkezdődik a végrehajtás megkezdése előtt.

> [!IMPORTANT]
> A folyamatban lévő másik művelet miatt megtartott felügyeleti műveletek automatikusan folytatódnak, amikor a feltételek teljesülnek. Nincs szükség felhasználói beavatkozásra az átmenetileg szüneteltetett felügyeleti műveletek folytatásához.

### <a name="canceling-management-operations"></a>Felügyeleti műveletek megszakítása

Az alábbi táblázat összefoglalja, hogyan lehet visszavonni bizonyos felügyeleti műveleteket és a jellemző általános időtartamokat:

Kategória  |Művelet  |Kampány  |Becsült megszakítási időtartam  |
|---------|---------|---------|---------|
|Üzembe helyezés |Példány létrehozása |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú) |Nem |  |
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva) |Igen |a műveletek 90%-a 5 percen belül befejeződik. |
|Törlés |Példány törlése |Nem |  |
|Törlés |Virtuális fürt törlése (felhasználó által kezdeményezett művelet) |Nem |  |

A kezelési művelet megszakításához lépjen az Áttekintés panelre, és kattintson a folyamatban lévő művelet értesítési mezőjére. A jobb oldalon megjelenik egy képernyő, amelyen a folyamatban lévő művelet megjelenik, és a művelet megszakítására szolgáló gomb jelenik meg. Az első kattintás után a rendszer kérni fogja, hogy kattintson újra, és erősítse meg, hogy meg kívánja szüntetni a műveletet.

[![Művelet megszakítása](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

A megszakítási kérelem elküldését és feldolgozását követően értesítést kap, ha a megszakítás beküldése sikeres volt.

Ha a művelet megszakítása sikeres, a rendszer néhány perc múlva megszakítja a kezelési műveletet, ami hibát jelez.

![Művelet eredményének megszakítása](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Ha a megszakítási kérelem meghiúsul, vagy a Mégse gomb nem aktív, az azt jelenti, hogy a felügyeleti művelet nem megszakítható állapotba került, és néhány percen belül befejeződik. A felügyeleti művelet addig folytatja a végrehajtást, amíg be nem fejeződik.

> [!IMPORTANT]
> A megszakítási műveletek jelenleg csak a portálon támogatottak.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQL felügyelt példánya az Azure platform és a SQL Server adatbázismotor fejlett biztonsági funkcióival rendelkezik.

### <a name="security-isolation"></a>Biztonság elkülönítése

Az SQL felügyelt példánya további biztonsági elkülönítést biztosít a többi bérlőtől az Azure platformon. A biztonsági elkülönítés magában foglalja a következőket:

- [Natív virtuális hálózat megvalósítása](connectivity-architecture-overview.md) és kapcsolata a helyszíni környezettel az Azure ExpressRoute vagy a VPN Gateway használatával.
- Alapértelmezett telepítés esetén az SQL-végpont csak egy magánhálózati IP-címen keresztül érhető el, ami lehetővé teszi a biztonságos kapcsolódást a privát Azure-vagy hibrid hálózatokból.
- Önálló bérlő dedikált mögöttes infrastruktúrával (számítás, tárolás).

Az alábbi ábra az alkalmazások különböző csatlakozási lehetőségeit ismerteti:

![Magas rendelkezésre állás](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Ha többet szeretne megtudni a VNet-integrációval és a hálózatkezelési házirendek kényszerítésével kapcsolatban az alhálózat szintjén, tekintse meg a [felügyelt példányok VNet-architektúráját](connectivity-architecture-overview.md) és [az alkalmazás felügyelt példányhoz való csatlakozását](connect-application-instance.md)

> [!IMPORTANT]
> Helyezzen több felügyelt példányt ugyanabban az alhálózatba, bárhol is legyenek a biztonsági követelmények, mivel ez további előnyöket biztosít. Az ugyanabban az alhálózatban található példányok jelentős mértékben leegyszerűsítik a hálózati infrastruktúra karbantartását, és csökkentik a példányok kiépítési idejét, mivel a hosszú kiépítési időtartam az első felügyelt példánynak az alhálózaton való üzembe helyezésének a költségeit veszi alapul.

### <a name="security-features"></a>Biztonsági funkciók

Az Azure SQL felügyelt példánya olyan speciális biztonsági funkciókat kínál, amelyek az adatai védelmére használhatók.

- A [felügyelt SQL-példányok naplózása](auditing-configure.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba helyezett naplófájlba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.
- Az adattitkosítás mozgás közben – az SQL felügyelt példánya a Transport Layer Security használatával biztosítja a mozgásban lévő adattitkosítást. A Transport Layer Securityon kívül az SQL felügyelt példánya a bizalmas adatok védelmét is biztosítja a repülésben, a nyugalmi állapotban, valamint a lekérdezések feldolgozásakor a [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használatával. Always Encrypted adatbiztonságot biztosít a kritikus fontosságú adatok ellopásával kapcsolatos szabálysértések ellen. Például a Always Encrypted a hitelkártyák számát a rendszer az adatbázisban mindig titkosítja, még a lekérdezés feldolgozása során is, amely lehetővé teszi az olyan jogosult munkatársak vagy alkalmazások számára, amelyek az adatok feldolgozását végzik.
- A komplex [veszélyforrások elleni védelem](threat-detection-configure.md) kiegészíti a [naplózást](auditing-configure.md) azáltal, hogy egy további, a szolgáltatásba épített biztonsági információs réteget biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az adatbázisok eléréséhez vagy kiaknázásához. A rendszer riasztást küld a gyanús tevékenységekről, a lehetséges sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A komplex veszélyforrások elleni védelem riasztásait [Azure Security Center](https://azure.microsoft.com/services/security-center/)lehet megtekinteni. A gyanús tevékenységek részleteit és a fenyegetés kivizsgálására és enyhítésére vonatkozó javaslatokat nyújtanak.  
- A [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. A dinamikus adatmaszkolás segít megakadályozni a bizalmas adatok jogosulatlan elérését azáltal, hogy kijelöli, hogy a bizalmas adatok mekkora részét mutatják ki az alkalmazási rétegre gyakorolt minimális hatás. Ez egy olyan házirend-alapú biztonsági funkció, amely elrejti a bizalmas adatokat a kijelölt adatbázis-mezőkön lévő lekérdezés eredmény-készletében, míg az adatbázisban lévő adatok nem változnak.
- A [sor szintű biztonság](/sql/relational-databases/security/row-level-security) (RLS) lehetővé teszi az adatbázistábla soraihoz való hozzáférés szabályozását a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet). Az RLS leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Tegyük fel például, hogy a dolgozók csak a saját részlegéhez tartozó adatsorokhoz férhessenek hozzá, vagy csak a releváns adatokat korlátozzák.
- Az [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a felügyelt SQL-példányok adatfájljait titkosítja, más néven az inaktív adatok titkosítását. A TDE az adatok és naplófájlok valós idejű I/O-titkosítását és visszafejtését hajtja végre. A titkosítás egy adatbázis-titkosítási kulcsot (ADATTITKOSÍTÁSI kulcsot) használ, amely az adatbázis-rendszerindítási rekordban van tárolva a helyreállítás során. A felügyelt példányokban lévő összes adatbázist transzparens adattitkosítással lehet védelemmel ellátni. A TDE olyan inaktív titkosítási technológia, SQL Server, amelyet számos megfelelőségi szabvány igényel a tárolóeszközök ellopása elleni védelemhez.

A titkosított adatbázisok SQL felügyelt példányra történő áttelepítése Azure Database Migration Service vagy natív visszaállítás útján támogatott. Ha egy titkosított adatbázist natív visszaállítással kíván áttelepíteni, a meglévő TDE-tanúsítvány áttelepítése a SQL Server példányról az SQL felügyelt példányra művelet szükséges. További információ az áttelepítési lehetőségekről: [SQL Server Migrálás az SQL felügyelt példányára](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az SQL felügyelt példánya támogatja a hagyományos SQL Server adatbázismotor-bejelentkezéseket és az Azure AD-vel integrált bejelentkezéseket. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) (**nyilvános előzetes**verzió) a helyszíni adatbázis-bejelentkezések Azure-beli felhőalapú verziója, amelyet a helyszíni környezetben használ. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) lehetővé teszik, hogy az Azure AD-bérlőből származó felhasználókat és csoportokat az igaz példányra kiterjedő rendszerbiztonsági tagként adja meg, amely képes bármely példány szintű művelet végrehajtására, beleértve az azonos felügyelt példányon belüli adatbázis-lekérdezéseket is.

Új szintaxist vezetünk be az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozásához **a külső szolgáltatótól**. A szintaxissal kapcsolatos további információkért tekintse meg a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Bejelentkezés létrehozása</a>című témakört, és tekintse át a [Azure Active Directory-rendszergazda létesítése az SQL felügyelt példányhoz](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) című cikket.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL felügyelt példányai lehetővé teszik az adatbázis-felhasználók és más Microsoft-szolgáltatások identitásának központilag kezelését [Azure Active Directory integrációval](../database/authentication-aad-overview.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. A Azure Active Directory támogatja a [többtényezős hitelesítést](../database/authentication-mfa-ssms-configure.md) az adatkezelés és az alkalmazások biztonságának növelésére az egyszeri bejelentkezési folyamat támogatása mellett.

### <a name="authentication"></a>Hitelesítés

Az SQL felügyelt példányának hitelesítése arra utal, hogy a felhasználók hogyan igazolják identitásukat az adatbázishoz való csatlakozáskor. A felügyelt SQL-példány két típusú hitelesítést támogat:  

- **SQL-hitelesítés**:

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ.
- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ, és felügyelt és integrált tartományok esetén támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználók mit tehetnek egy adatbázisban az Azure SQL felügyelt példányain, és a felhasználói fiók adatbázis-szerepkör-tagsága és az objektum-szintű engedélyek vezérlik. Az SQL felügyelt példánya ugyanazokkal az engedélyezési képességekkel rendelkezik, mint a SQL Server 2017.

## <a name="database-migration"></a>Adatbázis-áttelepítés

Az SQL felügyelt példánya a helyszíni vagy IaaS adatbázis-megvalósításokból származó tömeges adatbázis-áttelepítés felhasználói forgatókönyveit célozza meg. Az SQL felügyelt példánya több adatbázis-áttelepítési lehetőséget is támogat:

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás  

Az áttelepítési módszer az SQL-alapú biztonsági mentéseket használja az Azure Blob Storage-ba. Az Azure Storage-blobokban tárolt biztonsági másolatok közvetlenül visszaállíthatók egy felügyelt példányra a [T-SQL Restore parancs](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)használatával.

- A Wide World Importers – standard adatbázis biztonságimásolat-fájljának visszaállítását bemutató rövid útmutató: [biztonságimásolat-fájl visszaállítása felügyelt példányra](restore-sample-database-quickstart.md). Ez a rövid útmutató azt mutatja be, hogy fel kell töltenie egy biztonságimásolat-fájlt az Azure Blob Storage-ba, és biztonságossá kell tennie egy közös hozzáférés-aláírási (SAS-) kulcs használatával.
- További információ az URL-címről való visszaállításról: [NATÍV visszaállítás az URL-](migrate-to-instance-from-sql-server.md#native-restore-from-url)címről.

> [!IMPORTANT]
> Felügyelt példányról származó biztonsági másolatok csak egy másik felügyelt példányra állíthatók vissza. Nem állíthatók vissza SQL Server példányra vagy Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

A Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és SQL Server adatbázisok áthelyezéséhez szükséges feladatokat a Azure SQL Database, az Azure SQL felügyelt példányának és az Azure-beli virtuális gépen SQL Server. Lásd: a helyszíni [adatbázis migrálása az SQL felügyelt példányára Database Migration Service használatával](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

Az SQL felügyelt példányának célja, hogy a szakaszos kiadási csomagon keresztül 100%-os felülettel kompatibilis legyen a legújabb SQL Server verziójával. A szolgáltatások és az összehasonlítások listájáért lásd: az [SQL felügyelt példány funkcióinak összehasonlítása](../database/features-comparison.md), valamint az SQL felügyelt példányok és a SQL Server közötti t-SQL-különbségek listája: az [SQL felügyelt példányok t-SQL-különbözetei SQL Server](transact-sql-tsql-differences-sql-server.md).

Az SQL felügyelt példánya támogatja a SQL Server 2008-adatbázisok visszamenőleges kompatibilitását. A SQL Server 2005 adatbázis-kiszolgálókról történő közvetlen áttelepítés támogatott, és az áttelepített SQL Server 2005-adatbázisok kompatibilitási szintje SQL Server 2008-ra frissül.
  
Az alábbi ábra az SQL felügyelt példányának felületi kompatibilitását ismerteti:  

![Áttelepítés](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server helyszíni és az SQL felügyelt példány közötti fő különbségek

Az SQL felügyelt példányának előnye, hogy mindig naprakészek a felhőben, ami azt jelenti, hogy SQL Server egyes funkciói elavultak lehetnek, kivonhatók, vagy alternatívákkal rendelkeznek. Bizonyos esetekben szükség van arra, hogy az eszközök tisztában legyenek azzal, hogy egy adott funkció némileg eltérő módon működik, vagy a szolgáltatás olyan környezetben fut, amely nem teljes mértékben szabályozható.

Néhány kulcsfontosságú különbség:

- A magas rendelkezésre állás az [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)hasonló technológiával beépített és előre konfigurált.
- Csak automatikus biztonsági mentések és időponthoz tartozó visszaállítások érhetők el. `copy-only`Az ügyfelek olyan biztonsági másolatokat indíthatnak, amelyek nem akadályozzák az automatikus biztonsági mentési láncot.
- A teljes fizikai elérési utak megadása nem támogatott, ezért az összes vonatkozó forgatókönyvet másképp kell támogatni: az adatbázis visszaállítása nem támogatja az ÁTHELYEZÉSt, az adatbázis létrehozása nem teszi lehetővé a fizikai elérési utak használatát, BULK INSERT csak az Azure-Blobokkal működik, stb.
- Az SQL felügyelt példánya támogatja az [Azure ad-hitelesítést](../database/authentication-aad-overview.md) a Windows-hitelesítés Felhőbeli alternatívája.
- A felügyelt SQL-példányok automatikusan kezelik a memóriában tárolt OLTP objektumokat tartalmazó adatbázisok XTP-fájlcsoportok és-fájljait.
- Az SQL felügyelt példánya támogatja a SQL Server Integration Services (SSIS) szolgáltatást, és üzemeltetheti a SSIS-csomagokat tároló SSIS-katalógust (SSISDB), de a rendszer felügyelt Azure-SSIS Integration Runtime (IR) hajtja végre Azure Data Factory. Lásd: [Azure-SSIS IR létrehozása a Data Factoryban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). A SSIS funkcióinak összehasonlítását lásd: [SQL Database összehasonlítása az SQL felügyelt példányával](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Felügyeleti funkciók

Az SQL felügyelt példánya lehetővé teszi a rendszergazdák számára, hogy kevesebb időt töltsenek a felügyeleti feladatokban, mert a szolgáltatás elvégzi azokat az Ön számára, vagy nagy mértékben egyszerűsíti ezeket a feladatokat Például az [operációs rendszer/RDBMS telepítése és javítása](../database/high-availability-sla.md), a [dinamikus példányok átméretezése és konfigurálása](../database/single-database-scale.md), a [biztonsági másolatok](../database/automated-backups-overview.md), az [adatbázis-replikáció](replication-between-two-instances-configure-tutorial.md) (beleértve a rendszeradatbázisokat is), a [magas rendelkezésre állás konfigurálása](../database/high-availability-sla.md), valamint az állapot-és [teljesítmény-figyelési](../../azure-monitor/insights/azure-sql.md) adatfolyamok konfigurálása.

További információkért tekintse meg [a támogatott és nem támogatott SQL felügyelt példányok szolgáltatásainak listáját](../database/features-comparison.md), valamint a [T-SQL-különbségeket az SQL felügyelt példánya és a SQL Server között](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Felügyelt példány programozott azonosítása

A következő táblázat több, a Transact-SQL szolgáltatáson keresztül elérhető tulajdonságot mutat be, amelyek segítségével észlelhető, hogy az alkalmazás működik-e az SQL felügyelt példányával, és hogy fontos tulajdonságokat kér le.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez az érték ugyanaz, mint a SQL Databaseban. Ez **nem** jelzi az SQL Engine 12-es verzióját (SQL Server 2014). A felügyelt SQL-példányok mindig a legújabb, stabil SQL Engine-verziót futtatják, ami egyenlő vagy magasabb, mint a legújabb elérhető RTM-verzió SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez az érték ugyanaz, mint a SQL Databaseban.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték egyedileg azonosítja a felügyelt példányt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Teljes példány DNS-neve a következő formátumban: `<instanceName>` . `<dnsPrefix>` . a database.windows.net, ahol az az `<instanceName>` ügyfél által megadott név, míg a `<dnsPrefix>` neve automatikusan létrejön a globális DNS-név egyediségét garantáló név ("wcus17662feb9ce98"), például:.|Példa: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Következő lépések

- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](instance-create-quickstart.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](../database/features-comparison.md).
- További információ a VNet konfigurálásáról: [SQL felügyelt példány VNet konfigurációja](connectivity-architecture-overview.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](instance-create-quickstart.md).
- A Azure Database Migration Service áttelepítésre való használatával kapcsolatos oktatóanyagért lásd: [SQL felügyelt példányok áttelepítése Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
- Az SQL felügyelt példány-adatbázis teljesítményének speciális monitorozásához a beépített hibaelhárítási intelligenciával kapcsolatban lásd: az [Azure SQL felügyelt példány figyelése Azure SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért tekintse meg a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/managed/).
