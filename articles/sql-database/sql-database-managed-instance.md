---
title: SQL felügyelt példányok áttekintése
description: Ez a cikk Azure SQL Database felügyelt példányt ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 11/27/2019
ms.openlocfilehash: 1a9c24846606c53fefa1ffc1de59f358524020c4
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707621"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Mi Azure SQL Database felügyelt példány?

A felügyelt példány a Azure SQL Database új üzembe helyezési lehetősége, amely közel 100%-os kompatibilitást biztosít a legújabb SQL Server helyszíni (Enterprise Edition) adatbázismotor számára, amely egy natív [virtuális hálózati (VNet)](../virtual-network/virtual-networks-overview.md) implementációt biztosít, amely az általános biztonsági kérdésekre és a helyszíni SQL Server ügyfelek számára kedvező [üzleti modellre](https://azure.microsoft.com/pricing/details/sql-database/) épül. A felügyelt példányok telepítési modellje lehetővé teszi, hogy a meglévő SQL Server ügyfelek a helyszíni alkalmazásaikat a felhőbe a minimális alkalmazás-és adatbázis-módosításokkal emeljenek és váltsanak. Ugyanakkor a felügyelt példányok üzembe helyezési lehetősége megőrzi az összes (automatikus javítás és verziófrissítés, [automatikus biztonsági mentés](sql-database-automated-backups.md), [magas rendelkezésre állás](sql-database-high-availability.md) ) szolgáltatást, amely drasztikusan csökkenti a felügyeleti terhelést és a TCO-t.

> [!IMPORTANT]
> Azoknak a régióknak a listáját, amelyekben a felügyelt példány központi telepítése lehetőség jelenleg elérhető, tekintse meg a [támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions)című témakört.

A következő ábra a felügyelt példányok legfontosabb funkcióit vázolja fel:

![főbb funkciók](./media/sql-database-managed-instance/key-features.png)

A felügyelt példány üzembe helyezési modellje olyan ügyfelek számára készült, akik nagy mennyiségű alkalmazást telepíthetnek a helyszíni vagy IaaS, a saját készítésű vagy a független szoftvergyártók által biztosított környezetből teljes körűen felügyelt, kihasználatlan Felhőbeli környezetbe, a lehető legkevesebb migrációs erőfeszítéssel. Az Azure-ban a teljes mértékben automatizált [adatáttelepítési szolgáltatás (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) használatával az ügyfelek a helyszíni SQL Servert egy felügyelt példányra helyezhetik át, amely a helyszíni SQL Serverekkel való kompatibilitást és a natív VNet-támogatással rendelkező vásárlói példányok elkülönítését kínálja.  A frissítési garanciával lehetősége van arra, hogy a meglévő licenceket a felügyelt példányok kedvezményes díjszabására cserélje le a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával.  A felhőben a legjobb áttelepítési cél a felügyelt példány, ha olyan SQL Server-példányok, amelyek magas biztonságot és sokoldalú programozható felületet igényelnek.

A felügyelt példány üzembe helyezési lehetőségének célja, hogy a szakaszos kiadási csomagon keresztül 100%-os felülettel kompatibilis legyen a legújabb helyszíni SQL Server verzióval.

A Azure SQL Database központi telepítési lehetőségek közül: az önálló adatbázis, a készletezett adatbázis és a felügyelt példányok, valamint a virtuális gépen futtatott SQL Server az Azure-beli [SQL Server megfelelő verziójának kiválasztását](sql-database-paas-vs-sql-server-iaas.md)ismertető témakörben talál.

## <a name="key-features-and-capabilities"></a>Főbb funkciók és képességek

A felügyelt példány ötvözi a Azure SQL Database és SQL Server adatbázismotor által elérhető legjobb funkciókat.

> [!IMPORTANT]
> A felügyelt példányok a SQL Server legújabb verziójának összes szolgáltatásával futnak, beleértve az online műveleteket, az automatikus terv javításait és az egyéb vállalati teljesítménybeli fejlesztéseket. Az elérhető funkciók összehasonlítása a szolgáltatások [összehasonlítása: Azure SQL Database versus SQL Server](sql-database-features.md).

| **Pásti előnyei** | **Az üzletmenet folytonossága** |
| --- | --- |
|Nincs hardveres vásárlás és felügyelet <br>Nincs felügyeleti terhelés a mögöttes infrastruktúra kezeléséhez <br>Gyors kiépítés és a szolgáltatás skálázása <br>Automatikus javítás és verziófrissítés <br>Integráció más Pásti adatszolgáltatásokkal |99,99%-os rendelkezésre állási SLA  <br>Beépített [magas rendelkezésre állás](sql-database-high-availability.md) <br>[Automatizált biztonsági mentéssel](sql-database-automated-backups.md) védett adatvédelem <br>Ügyfél által konfigurálható biztonsági másolatok megőrzési ideje <br>Felhasználó által kezdeményezett [biztonsági másolatok](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Időponthoz tartozó adatbázis-visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore) képesség |
|**Biztonság és megfelelőség** | **Felügyeleti**|
|Elszigetelt környezet ([VNet-integráció](sql-database-managed-instance-connectivity-architecture.md), egybérlős szolgáltatás, dedikált számítás és tárolás) <br>[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure ad-hitelesítés](sql-database-aad-authentication.md), egyszeri bejelentkezés támogatása <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a>  <br>Megfelel a megfelelőségi szabványoknak, mint az Azure SQL Database <br>[SQL-naplózás](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager API a szolgáltatások kiosztásának és méretezésének automatizálásához <br>A szolgáltatás kézi üzembe helyezéséhez és méretezéséhez Azure Portal funkció <br>Adatáttelepítési szolgáltatás

> [!IMPORTANT]
> Azure SQL Database (az összes központi telepítési lehetőség), számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

A felügyelt példányok legfontosabb funkciói a következő táblázatban láthatók:

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
| Beépített jelentéskészítési szolgáltatás (SSRS) | Nem használható Power BI vagy SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A felügyelt példányok [virtuális mag-alapú vásárlási modellje](sql-database-service-tiers-vcore.md) rugalmasságot, irányítást, átláthatóságot és egyszerű módszert biztosít a helyszíni számítási feladatok követelményeinek a felhőbe való lefordításához. Ez a modell lehetővé teszi a számítási feladatok, a memória és a tárterület módosítását a munkaterhelés igényei alapján. A virtuális mag modell akár 30%-os megtakarítást is igénybe vehet, [Azure Hybrid Benefit SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

A virtuális mag modellben a hardver generációi közül választhat.

- **Gen4** A logikai CPU-k Intel E5-2673 v3 (Haswell) 2,4-GHz processzorok, csatlakoztatott SSD, fizikai magok, 7 GB RAM/mag, valamint 8 és 24 virtuális mag közötti számítási méretek.
- **Gen5** A logikai CPU-k Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok, a gyors NVMe SSD-k, a Hyper-threaded logikai mag és a 4 és 80 közötti számítási méretek alapján működnek.

További információk a [felügyelt példányok erőforrás-korlátaiban](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)található hardveres generációk közötti különbségről.

> [!IMPORTANT]
> Az új Gen4-adatbázisok már nem támogatottak a Kelet-Ausztrália vagy Brazília déli régiójában.

## <a name="managed-instance-service-tiers"></a>Felügyelt példányok szolgáltatási szintjei

A felügyelt példány két szolgáltatási rétegben érhető el:

- **Általános célú**: olyan alkalmazásokhoz tervezve, amelyek jellemző teljesítménybeli és IO-késési követelményekkel rendelkeznek.
- **Üzleti szempontból kritikus**: az alacsony i/o-késési követelményekkel rendelkező alkalmazások számára lett kialakítva, és a számítási feladatok mögöttes karbantartási műveletek minimális hatása.

Mindkét szolgáltatási szinten 99,99%-os rendelkezésre állást garantálunk, és lehetővé teszi a tárolási méret és a számítási kapacitás egymástól független kiválasztását. A Azure SQL Database magas rendelkezésre állású architektúrával kapcsolatos további információkért lásd: [magas rendelkezésre állás és Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Általános célú szolgáltatási szintek

Az alábbi lista ismerteti a általános célú szolgáltatási szinten szereplő legfontosabb jellemzőket:

- Az üzleti alkalmazások többségének kialakítása jellemző teljesítménnyel kapcsolatos követelményekkel
- Nagy teljesítményű Azure Blob Storage (8 TB)
- Beépített [magas rendelkezésre állás](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a megbízható Azure Blob Storage és az [Azure Service Fabric](../service-fabric/service-fabric-overview.md) alapján

További információ: [tárolási réteg általános célú rétegben](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) és [tárolási teljesítmény – ajánlott eljárások és szempontok a felügyelt példányok esetében (általános célú)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

További információk a [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)között a szolgáltatási szintek közötti különbségről.

### <a name="business-critical-service-tier"></a>üzletileg kritikus szolgáltatási szintet

Üzletileg kritikus szolgáltatási szintet a magas i/o-követelményeknek megfelelő alkalmazások számára készült. Több elszigetelt replika használatával a hibák legmagasabb rugalmasságát kínálja.

Az alábbi lista a üzletileg kritikus szolgáltatási szintje főbb jellemzőit ismerteti:

- A legnagyobb teljesítménnyel és HA követelményekkel rendelkező üzleti alkalmazásokhoz tervezve
- A szolgáltatás villámgyors helyi SSD-tárolóval (1 TB-ig Gen4 és akár 4 TB-ig Gen5) is rendelkezik
- Az [Always On rendelkezésre állási csoportokon](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) és az [Azure Service Fabricon](../service-fabric/service-fabric-overview.md)alapuló beépített [magas rendelkezésre állás](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) .
- Beépített további [írásvédett adatbázis-replikák](sql-database-read-scale-out.md) , amelyek jelentéskészítésre és más írásvédett számítási feladatokra is használhatók
- [Memóriabeli OLTP](sql-database-in-memory.md) , amely nagy teljesítményű követelményekkel használható munkaterheléshez  

További információk a [felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)között a szolgáltatási szintek közötti különbségről.


## <a name="managed-instance-management-operations"></a>Felügyelt példányok kezelési műveletei

Az Azure SQL Database olyan felügyeleti műveleteket biztosít, amelyekkel automatikusan üzembe helyezhet új felügyelt példányokat, frissítheti a példány tulajdonságait és törölheti a szükségtelenné vált példányokat. Ez a szakasz a felügyeleti műveletekkel és azok jellemző időtartamával kapcsolatos információkat tartalmaz.

Az [Azure Virtual Networks (virtuális hálózatok) szolgáltatáson belüli központi telepítések](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) támogatásához, valamint az ügyfelek elkülönítésének és biztonságának biztosításához a felügyelt példány a [virtuális fürtökre](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)támaszkodik, amelyek az ügyfél virtuális hálózati alhálózatán belül üzembe helyezett elkülönített virtuális gépek dedikált készletét képviselik. Az üres alhálózat minden felügyelt példányának telepítése lényegében egy új virtuális fürt buildout eredményez.

A központilag telepített felügyelt példányok további műveletei hatással lehetnek a mögöttes virtuális fürtre is. Ez befolyásolja a felügyeleti műveletek időtartamát, mivel a további virtuális gépek üzembe helyezése olyan terheléssel jár, amelyet figyelembe kell venni, amikor új központi telepítéseket vagy frissítéseket tervez a meglévő felügyelt példányokra.

A felügyeleti műveleteket a következőképpen lehet kategorizálni:

- Példány üzembe helyezése (új példány létrehozása). 
- Példány frissítése (példány tulajdonságainak módosítása, például virtuális mag vagy fenntartott tároló.
- Példány törlése.

Általában a virtuális fürtökön végrehajtott műveletek a leghosszabb időt jelentik. A virtuális fürtök műveleteinek időtartama eltérő lehet – az alábbi értékek általában a meglévő telemetria-adatokon alapulnak:

- Virtuális fürtök létrehozása. Ez egy szinkron lépés a példányok kezelési műveleteihez. **a műveletek 90%-a 4 órán belül befejeződik**.
- Virtuális fürt átméretezése (bővítés vagy zsugorodás). A bővítés egy szinkron lépés, míg a zsugorodás aszinkron módon történik (a példány-kezelési műveletek időtartamára gyakorolt hatás nélkül). a **fürt bővítésének 90%-a kevesebb, mint 2,5 óra**.
- Virtuális fürt törlése. A törlés egy aszinkron lépés, de [manuálisan is elindítható](sql-database-managed-instance-delete-virtual-cluster.md) egy üres virtuális fürtön, ebben az esetben a rendszer szinkron módon hajtja végre. a **virtuális fürtök törlésének 90%-a 1,5 órában fejeződik be**.

Emellett a példányok kezelése az üzemeltetett adatbázisokra vonatkozó egyik műveletet is tartalmazhatja, ami hosszabb időtartamot eredményez:

- Adatbázisfájlok csatolása az Azure Storage-ból. Ez egy szinkron lépés, például a számítás (virtuális mag), illetve a általános célú szolgáltatási szinten fel-vagy leskálázás. a **műveletek 90%-a 5 percen belül befejeződik**.
- Always On rendelkezésre állási csoport kivetése. Ez egy szinkron lépés, például a számítási (virtuális mag) vagy a tárolási skálázás a üzletileg kritikus szolgáltatási rétegben, valamint a szolgáltatási rétegnek a általános célúról a üzletileg kritikusra (vagy fordítva) való megváltoztatására. A művelet időtartama a teljes adatbázis méretével, valamint az aktuális adatbázis-tevékenységgel (aktív tranzakciók száma) arányos. Az adatbázis-tevékenység egy példány frissítésekor jelentős variancia bevezetését jelentheti a teljes időtartamra. a **műveletek 90%-a 220 GB/óra vagy újabb**.

A következő táblázat összefoglalja a műveleteket és a jellemző általános időtartamokat:

|Kategória  |Művelet  |Hosszan futó szegmens  |Becsült időtartam  |
|---------|---------|---------|---------|
|**Üzembe helyezés** |Első példány egy üres alhálózaton|Virtuális fürt létrehozása|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |Egy másik hardver generációjának első példánya egy nem üres alhálózatban (például az első gen 5 példány egy 4. generációs példánnyal rendelkező alhálózatban)|Virtuális fürt létrehozása *|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |4 virtuális mag első példányának létrehozása üres vagy nem üres alhálózatban|Virtuális fürt létrehozása * *|a műveletek 90%-a 4 órán belül befejeződik|
|Üzembe helyezés |A következő példány létrehozása a nem üres alhálózaton belül (2., 3. stb. példány)|Virtuális fürtök átméretezése|a műveletek 90%-a befejezve 2,5 órában|
|**Update** |Példány tulajdonságainak módosítása (rendszergazdai jelszó, HRE bejelentkezés, Azure Hybrid Benefit jelző)|–|Legfeljebb 1 perc|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (általános célú szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a befejezve 2,5 órában|
|Frissítés |A példány tárolási felskálázása felfelé/lefelé (üzletileg kritikus szolgáltatási szintet)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (általános célú)|– A virtuális fürtök átméretezése<br>– Adatbázisfájlok csatolása|a műveletek 90%-a befejezve 2,5 órában|
|Frissítés |A példány számítási (virtuális mag) méretezése felfelé és lefelé (üzletileg kritikus)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|Frissítés |Példány leskálázása 4 virtuális mag (általános célú)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>– Adatbázisfájlok csatolása|a műveletek 90%-a 4 óra 5 percben * *|
|Frissítés |Példány leskálázása 4 virtuális mag (általános célú)|– A virtuális fürtök átméretezése (ha első alkalommal végzett, a virtuális fürtök létrehozásához * *) szükséges<br>-Always On rendelkezésre állási csoport bevetése|a műveletek 90%-a 4 órán belül befejeződik + az összes adatbázis kivetéséhez szükséges idő (220 GB/óra)|
|Frissítés |Példány szolgáltatási szintjeinek változása (általános célú üzletileg kritikus és fordítva)|– A virtuális fürtök átméretezése<br>-Always On rendelkezésre állási csoport bevetése|az összes adatbázis 90%-ának befejezése 2,5 óra + idő alatt az összes adatbázis kivetéséhez (220 GB/óra)|
|**Törlés**|Példány törlése|Az összes adatbázis biztonsági másolatának naplózása|90%-os művelet akár 1 percenként is befejeződik.<br>Megjegyzés: Ha az alhálózat utolsó példánya törölve lett, a művelet a virtuális fürt törlését 12 óra elteltével * * * fogja ütemezni. * * *|
|Törlés|Virtuális fürt törlése (felhasználó által kezdeményezett művelet)|Virtuális fürt törlése|a műveletek 90%-a befejezve legfeljebb 1,5 óra|

a \* virtuális fürt hardveres generálásra épül.

\*\* a 4 virtuális mag üzembe helyezési lehetőséget 2019 júniusában adták ki, és egy új virtuális fürt verzióját igénylik. Ha a cél alhálózatban olyan példányok voltak, amelyek mind a 12. június előtt jöttek létre, a rendszer automatikusan telepíti az új virtuális fürtöt a 4 virtuális mag-példányok üzemeltetésére.

a \*\*\* 12 óra az aktuális konfiguráció, de a jövőben változhat, így nem fog nehéz függőséget okozni. Ha korábban törölni kell egy virtuális fürtöt (például az alhálózat felszabadításához), tekintse meg az [alhálózat törlése Azure SQL Database felügyelt példány törlése után](sql-database-managed-instance-delete-virtual-cluster.md)című témakört.

### <a name="instance-availability-during-management"></a>Példány rendelkezésre állása a felügyelet során

A felügyelt példányok nem érhetők el az ügyfélalkalmazások számára az üzembe helyezési és törlési műveletek során.

A felügyelt példányok elérhetők a frissítési műveletek során, de a feladatátvétel által okozott rövid állásidőt a frissítések végén, amely általában legfeljebb 10 másodpercig tart.

> [!IMPORTANT]
> A feladatátvétel időtartama jelentősen változhat abban az esetben, ha az adatbázisokon hosszan futó tranzakciók történnek, a hosszú távú [helyreállítási idő](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)miatt. Ezért nem ajánlott Azure SQL Database felügyelt példány számítási vagy tárolási kapacitásának méretezésére, vagy a szolgáltatási réteg módosítására egy időben a hosszan futó tranzakciók (adatimportálás, adatfeldolgozási feladatok, indexek újraépítése stb.) alapján. A művelet végén elvégezhető adatbázis-feladatátvétel megszakítja a folyamatban lévő tranzakciókat, és hosszan tartó helyreállítási időt eredményez.

A [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md) jelenleg nem érhető el Azure SQL Database felügyelt példányok számára. Ha engedélyezve van, ez a funkció jelentősen csökkenti a feladatátvételi idő változékonyságát, még a hosszan futó tranzakciók esetében is.



## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

A felügyelt példány központi telepítésének lehetősége az Azure Cloud és SQL Server adatbázismotor speciális biztonsági funkcióit ötvözi.

### <a name="managed-instance-security-isolation"></a>Felügyelt példányok biztonsági elkülönítése

A felügyelt példányok további biztonsági elkülönítést biztosítanak más bérlők számára az Azure-felhőben. A biztonsági elkülönítés magában foglalja a következőket:

- [Natív virtuális hálózat megvalósítása](sql-database-managed-instance-connectivity-architecture.md) és a helyszíni környezethez való kapcsolódás az Azure Express Route vagy a VPN Gateway használatával.
- Alapértelmezett telepítés esetén az SQL-végpont csak egy magánhálózati IP-címen keresztül érhető el, ami lehetővé teszi a biztonságos kapcsolódást a privát Azure-vagy hibrid hálózatokból.
- Önálló bérlő dedikált mögöttes infrastruktúrával (számítás, tárolás).

Az alábbi ábra az alkalmazások különböző csatlakozási lehetőségeit ismerteti:

![magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Ha többet szeretne megtudni a VNet-integrációval és a hálózatkezelési házirendek kényszerítésével kapcsolatban az alhálózat szintjén, tekintse meg a [felügyelt példányok VNet-architektúráját](sql-database-managed-instance-connectivity-architecture.md) és [az alkalmazás felügyelt példányhoz való csatlakozását](sql-database-managed-instance-connect-app.md)

> [!IMPORTANT]
> Helyezzen több felügyelt példányt ugyanabba az alhálózatba, bárhol is legyen a biztonsági követelményei, mivel ez további előnyöket biztosít. Az ugyanabban az alhálózatban lévő Collocating-példányok jelentősen leegyszerűsítik a hálózati infrastruktúra karbantartását, és csökkentik a példányok kiépítésének idejét, mivel a hosszú kiépítési időtartam az első felügyelt példánynak az alhálózaton való üzembe helyezésének költségeit veszi alapul.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database biztonsági funkciók

A Azure SQL Database speciális biztonsági funkciókat nyújt, amelyekkel védetté teheti adatait.

- A [felügyelt példányok naplózása](sql-database-managed-instance-auditing.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókba helyezett naplófájlba írja azokat. A naplózás segíthet a jogszabályi megfelelőség fenntartásában, az adatbázis-tevékenység megértésében, valamint az üzleti problémákat vagy a feltételezett biztonsági szabálysértéseket jelezhető eltérések és rendellenességek megismerésében.
- Adattitkosítás a mozgásban – a felügyelt példányok a Transport Layer Security használatával titkosítják a mozgásban lévő adatvédelmet. A szállítási réteg biztonsága mellett a felügyelt példányok üzembe helyezési lehetősége a bizalmas adatok védelmét is biztosítja a repülésben, a nyugalmi állapotban és a lekérdezés feldolgozásakor a [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használatával. Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például a Always Encrypted a hitelkártyák számát a rendszer az adatbázisban mindig titkosítja, még a lekérdezés feldolgozása során is, amely lehetővé teszi az olyan jogosult munkatársak vagy alkalmazások számára, amelyek az adatok feldolgozását végzik.
- A komplex [veszélyforrások elleni védelem](sql-database-managed-instance-threat-detection.md) kiegészíti a [naplózást](sql-database-managed-instance-auditing.md) azáltal, hogy egy további, a szolgáltatásba épített biztonsági információs réteget biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az adatbázisok eléréséhez vagy kiaknázásához. A rendszer riasztást küld a gyanús tevékenységekről, a lehetséges sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A komplex veszélyforrások elleni védelemre vonatkozó riasztások a [Azure Security Centerról](https://azure.microsoft.com/services/security-center/) tekinthetők meg, és a gyanús tevékenységek részletes ismertetését és a fenyegetés kivizsgálására és elhárítására vonatkozó javaslatokat nyújtanak.  
- A [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatokkal való érintkezést azáltal, hogy a nem Kiemelt felhasználók számára maszkolást végez. A dinamikus adatmaszkolás segít megakadályozni a bizalmas adatok jogosulatlan elérését azáltal, hogy kijelöli, hogy a bizalmas adatok mekkora részét mutatják ki az alkalmazási rétegre gyakorolt minimális hatás. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja.
- A [sor szintű biztonság](/sql/relational-databases/security/row-level-security) lehetővé teszi, hogy az adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzőinek (például csoporttagság vagy végrehajtási környezet) alapján szabályozza. A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Tegyük fel például, hogy a dolgozók csak a saját részlegéhez tartozó adatsorokhoz férhessenek hozzá, vagy csak a releváns adatokat korlátozzák.
- Az [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) titkosítja a felügyelt példányok adatfájljait, más néven az inaktív adatok titkosítását. A TDE az adatok és naplófájlok valós idejű I/O-titkosítását és visszafejtését hajtja végre. A titkosítás egy adatbázis-titkosítási kulcsot (ADATTITKOSÍTÁSI kulcsot) használ, amely az adatbázis-rendszerindítási rekordban van tárolva a helyreállítás során. A felügyelt példányokban lévő összes adatbázist transzparens adattitkosítással lehet védelemmel ellátni. A TDE SQL Server bevált titkosítási funkciója, amelyre számos megfelelőségi szabványnak szüksége van a tárolási adathordozók lopása elleni védelemhez.

A titkosított adatbázisok felügyelt példányra történő áttelepítése a Azure Database Migration Service (DMS) vagy a natív visszaállítás használatával támogatott. Ha a titkosított adatbázist natív visszaállítással kívánja áttelepíteni, a meglévő TDE-tanúsítvány áttelepítése a helyszíni SQL Server vagy a virtuális gépen SQL Server egy felügyelt példányra, egy szükséges lépés. További információ az áttelepítési lehetőségekről: [SQL Server példány áttelepítése felügyelt példányra](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

A felügyelt példány központi telepítési lehetősége támogatja a hagyományos SQL Server adatbázismotor-bejelentkezéseket és a Azure Active Directory (HRE) szolgáltatással integrált bejelentkezéseket. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) (**nyilvános előzetes**verzió) a helyszíni adatbázis-bejelentkezések Azure Cloud-verziója, amelyet a helyszíni környezetben használ. Az Azure AD-kiszolgáló rendszerbiztonsági tagjai (bejelentkezések) lehetővé teszik, hogy az Azure Active Directory-bérlőtől származó felhasználókat és csoportokat az igaz példányok hatókörű tagjaiként adja meg, amelyek képesek bármilyen szintű művelet végrehajtására, beleértve az azonos felügyelt adatbázison belüli lekérdezéseket is. például.

Új szintaxist vezetünk be az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozásához **a külső szolgáltatótól**. A szintaxissal kapcsolatos további információkért tekintse meg a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Bejelentkezés létrehozása</a>című témakört, és tekintse át a [felügyelt példány Azure Active Directory-rendszergazdájának kiépítése](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) című cikket.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

A felügyelt példányok központi telepítésének beállításával központilag kezelheti az adatbázis-felhasználó és más Microsoft-szolgáltatások identitásait [Azure Active Directory integrációval](sql-database-aad-authentication.md). Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést.

### <a name="authentication"></a>Hitelesítés

A felügyelt példányok hitelesítése arra utal, hogy a felhasználók hogyan igazolják identitásukat az adatbázishoz való csatlakozáskor. Az SQL Database két hitelesítési típust támogat:  

- **SQL-hitelesítés**:

  Ez a hitelesítési módszer egy felhasználónevet és egy jelszót használ.
- **Azure Active Directory hitelesítés**:

  Ez a hitelesítési módszer Azure Active Directory által felügyelt identitásokat használ, és felügyelt és integrált tartományok esetén támogatott. [Amikor csak lehet](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), használja az Active Directory-hitelesítést (beépített biztonság).

### <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a felhasználók mit tehetnek egy Azure SQL Databaseon belül, és a felhasználói fiók adatbázis-szerepkör-tagságai és objektum-szintű engedélyei vezérlik. A felügyelt példányok ugyanazokkal az engedélyezési képességekkel rendelkeznek, mint a SQL Server 2017.

## <a name="database-migration"></a>Adatbázis-áttelepítés

A felügyelt példány központi telepítési lehetősége a helyszíni vagy a IaaS adatbázis-implementációból származó tömeges adatbázis-áttelepítés felhasználói forgatókönyveit célozza meg. A felügyelt példány több adatbázis-áttelepítési lehetőséget is támogat:

### <a name="back-up-and-restore"></a>Biztonsági mentés és visszaállítás  

Az áttelepítési módszer az SQL-alapú biztonsági mentéseket használja az Azure Blob Storage-ba. Az Azure Storage-blobban tárolt biztonsági másolatok közvetlenül visszaállíthatók egy felügyelt példányra a [T-SQL Restore parancs](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)használatával.

- A Wide World Importers – standard adatbázis biztonságimásolat-fájljának visszaállítását bemutató rövid útmutató: [biztonságimásolat-fájl visszaállítása felügyelt példányra](sql-database-managed-instance-get-started-restore.md). Ez a rövid útmutató azt mutatja be, hogy fel kell töltenie egy biztonságimásolat-fájlt az Azure Blob Storage-ba, és biztonságossá kell tennie a közös hozzáférésű aláírás (SAS) kulcsával.
- További információ az URL-címről való visszaállításról: [NATÍV visszaállítás az URL-](sql-database-managed-instance-migrate.md#native-restore-from-url)címről.

> [!IMPORTANT]
> Felügyelt példányról származó biztonsági másolatok csak egy másik felügyelt példányra állíthatók vissza. Nem állíthatók vissza helyszíni SQL Server vagy egyetlen adatbázishoz/rugalmas készlethez.

### <a name="data-migration-service"></a>Adatáttelepítési szolgáltatás

A Azure Database Migration Service egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy a zökkenőmentes áttelepítések több adatbázisból az Azure-beli adatplatformokra minimális állásidővel. Ez a szolgáltatás leegyszerűsíti a meglévő harmadik fél áttelepítéséhez szükséges feladatokat, valamint SQL Server adatbázisok Azure SQL Database (egyetlen adatbázis, a rugalmas készletekben lévő készletezett adatbázisok és a felügyelt példányban található példány-adatbázisok) és az Azure-beli virtuális gépek SQL Server. Lásd: a helyszíni [adatbázis áttelepítését felügyelt példányra a DMS használatával](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Támogatott SQL-funkciók

A felügyelt példány központi telepítési lehetőségének célja, hogy a szolgáltatás általános rendelkezésre állása előtt 100%-os területtel kompatibilis legyen a helyszíni SQL Serverekkel. A szolgáltatások és az összehasonlítások listájáért lásd: [SQL Database funkció összehasonlítása](sql-database-features.md), valamint a felügyelt példányok és a SQL Server által kezelt t-SQL különbségek listáját lásd: a [felügyelt példányok t-SQL eltérései a SQL Server](sql-database-managed-instance-transact-sql-information.md).

A felügyelt példány központi telepítési lehetősége támogatja az SQL 2008-adatbázisok visszamenőleges kompatibilitását. Az SQL 2005 adatbázis-kiszolgálókról történő közvetlen áttelepítés támogatott, az áttelepített SQL 2005-adatbázisok kompatibilitási szintje pedig az SQL 2008-ra frissül.
  
Az alábbi ábrán a felügyelt példányok felületének kompatibilitása látható:  

![áttelepítési](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server helyszíni és felügyelt példány közötti fő különbségek

A felügyelt példány központi telepítési lehetőségének előnye, hogy mindig naprakészek a felhőben, ami azt jelenti, hogy a helyszíni SQL Server egyes funkciói elavultak, kivonhatók vagy alternatívák lehetnek. Bizonyos esetekben szükség van arra, hogy az eszközök tisztában legyenek azzal, hogy egy adott funkció némileg eltérő módon működik, vagy a szolgáltatás nem fut olyan környezetben, amely nem teljes mértékben szabályozható:

- A magas rendelkezésre állás az [Always On rendelkezésre állási csoportokhoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)hasonló technológiával beépített és előre konfigurált.
- Automatizált biztonsági mentések és időpontok visszaállítása. Az ügyfél olyan `copy-only` biztonsági mentéseket kezdeményezhet, amelyek nem akadályozzák az automatikus biztonsági mentési láncot.
- A felügyelt példány nem teszi lehetővé teljes fizikai elérési utak megadását, így az összes vonatkozó forgatókönyvet másképp kell támogatni: az adatbázis visszaállítása nem támogatja az ÁTHELYEZÉSt, az adatbázis létrehozása nem teszi lehetővé a fizikai elérési utak használatát, BULK INSERT csak az Azure-Blobokkal működik, stb.
- A felügyelt példány támogatja az [Azure ad-hitelesítést](sql-database-aad-authentication.md) Felhőbeli Alternatívaként a Windows-hitelesítéshez.
- A felügyelt példány automatikusan kezeli a memóriában tárolt OLTP objektumokat tartalmazó adatbázisok XTP-fájlcsoportja és-fájljait
- A felügyelt példány támogatja a SQL Server Integration Services (SSIS) szolgáltatást, és képes tárolni a SSIS-csomagokat tároló SSIS-katalógust (SSISDB), de a Azure Data Factory (ADF) felügyelt Azure-SSIS Integration Runtime (IR) hajtja végre. lásd: [Azure-SSIS IR létrehozása ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Az SQL Database SSIS funkcióinak összehasonlítását lásd: [Azure SQL Database önálló adatbázis, rugalmas készlet és felügyelt példány összevetése](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Felügyelt példányok felügyeleti funkciói

A felügyelt példány központi telepítésének lehetősége lehetővé teszi, hogy a rendszergazda kevesebb időt fordítson a felügyeleti feladatokra, mert az SQL Database szolgáltatás elvégzi ezeket a feladatokat, vagy nagy mértékben leegyszerűsíti ezeket a feladatokat. Például az [operációs rendszer/RDBMS telepítése és javítása](sql-database-high-availability.md), a [dinamikus példányok átméretezése és konfigurálása](sql-database-single-database-scale.md), a [biztonsági másolatok](sql-database-automated-backups.md), az [adatbázis-replikáció](replication-with-sql-database-managed-instance.md) (beleértve a rendszeradatbázisokat is), a [magas rendelkezésre állás konfigurálása](sql-database-high-availability.md), valamint az állapot-és [teljesítmény-figyelési](../azure-monitor/insights/azure-sql.md) adatfolyamok konfigurálása.

> [!IMPORTANT]
> A támogatott, részben támogatott és nem támogatott szolgáltatások listáját itt tekintheti meg: [SQL Database szolgáltatások](sql-database-features.md). A felügyelt példányok és a SQL Server közötti T-SQL-különbségek listáját a következő témakörben tekintheti meg: [felügyelt példányok t-SQL-különbözetek SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Felügyelt példány programozott azonosítása

A következő táblázat több, a Transact SQL szolgáltatáson keresztül elérhető tulajdonságot mutat be, amelyekkel észlelhető, hogy az alkalmazás felügyelt példányon dolgozik, és fontos tulajdonságokat kér le.

|Tulajdonság|Value (Díj)|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez az érték ugyanaz, mint a SQL Databaseban.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez az érték ugyanaz, mint a SQL Databaseban.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték egyedileg azonosítja a felügyelt példányt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Teljes példány DNS-neve a következő formátumban:`<instanceName>``<dnsPrefix>`. database.windows.net, ahol a `<instanceName>` az ügyfél által megadott név, míg a `<dnsPrefix>` a globális DNS-név egyediségét garantáló név ("wcus17662feb9ce98") automatikusan generált része (például)
|Példa: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Következő lépések

- Az első felügyelt példány létrehozásával kapcsolatos információkért lásd: gyors [útmutató](sql-database-managed-instance-get-started.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- A VNet-konfigurációval kapcsolatos további információkért lásd: [felügyelt példányok VNet konfigurációja](sql-database-managed-instance-connectivity-architecture.md).
- Egy felügyelt példányt létrehozó gyors útmutató, valamint egy adatbázis biztonságimásolat-fájlból való visszaállítása: [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md).
- A Azure Database Migration Service (DMS) áttelepítésre való használatát ismertető oktatóanyagért lásd: a [felügyelt példányok áttelepítése a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).
- A felügyelt példányok adatbázisának teljesítményének speciális figyelése a beépített hibaelhárítási intelligenciával: [Azure SQL Database figyelése Azure SQL Analytics használatával](../azure-monitor/insights/azure-sql.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
