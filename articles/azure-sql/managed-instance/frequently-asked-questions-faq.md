---
title: Gyakori kérdések (GYIK)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL felügyelt példányok – gyakori kérdések (GYIK)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: d2e4b07c97e09fce5cdaa034e2fe67a18ef0d7f1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171159"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL felügyelt példányok – gyakori kérdések (GYIK)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk az [Azure SQL felügyelt példányának](sql-managed-instance-paas-overview.md)leggyakoribb kérdéseit tartalmazza.

## <a name="supported-features"></a>Támogatott funkciók

**Hol találhatom meg a felügyelt SQL-példányok által támogatott szolgáltatások listáját?**

A felügyelt SQL-példány támogatott funkcióinak listáját lásd: az [Azure SQL felügyelt példányának funkciói](../database/features-comparison.md).

A felügyelt Azure SQL-példány és a SQL Server szintaxisának és viselkedésének különbségeit lásd: a [T-SQL eltérései SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technikai specifikáció, erőforrás-korlátozások és egyéb korlátozások
 
**Hol találhatók a felügyelt SQL-példányok technikai jellemzői és erőforrás-korlátai?**

Az elérhető hardver-létrehozási jellemzőkkel kapcsolatban lásd: [technikai különbségek a hardveres generációkban](resource-limits.md#hardware-generation-characteristics).
A rendelkezésre álló szolgáltatási szintek és azok jellemzői a [szolgáltatási szintek közötti technikai különbségek](resource-limits.md#service-tier-characteristics)című részben olvashatók.

**Milyen szolgáltatási szintet vehetek igénybe?**

Bármely ügyfél bármely szolgáltatási szinten jogosult. Ha azonban a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával szeretné kicserélni a meglévő licenceket az Azure SQL felügyelt példányának kedvezményes díjszabására, vegye figyelembe, hogy a frissítési garanciával rendelkező SQL Server Enterprise Edition-ügyfelek jogosultak a [általános célú](../database/service-tier-general-purpose.md) vagy [üzletileg kritikus](../database/service-tier-business-critical.md) teljesítményi szintjeire, valamint a frissítési garanciával rendelkező SQL Server Standard Edition ügyfelekre csak a általános célú teljesítmény szintjére jogosultak. További részletekért tekintse meg [a AHB adott jogosultságait](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Milyen előfizetési típusokat támogat az SQL felügyelt példánya?**

A támogatott előfizetési típusok listáját a [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types)című részben tekintheti meg. 

**Mely Azure-régiók támogatottak?**

A felügyelt példányok a legtöbb Azure-régióban létrehozhatók; Lásd: [az SQL felügyelt példányainak támogatott régiói](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Ha olyan régióban felügyelt példányra van szüksége, amely jelenleg nem támogatott, [küldjön egy támogatási kérést a Azure Portalon keresztül](../database/quota-increase-request.md).

**Van-e kvóta-korlátozás a felügyelt SQL-példányok üzembe helyezéséhez?**

A felügyelt példány két alapértelmezett korláttal rendelkezik: a használható alhálózatok számának korlátozása és a kiépíthető virtuális mag számának korlátozása. A korlátok az előfizetési típusok és régiók között változnak. Az előfizetés típusa szerint a regionális erőforrásokra vonatkozó korlátozások listáját a következő témakörben tekintheti meg: táblázat a [regionális erőforrások korlátozása](resource-limits.md#regional-resource-limitations)alapján. Ezek olyan enyhe korlátok, amelyek igény szerint növelhetők. Ha az aktuális régiókban több felügyelt példányt kell kiépíteni, küldjön egy támogatási kérést a kvóta növeléséhez a Azure Portal használatával. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](../database/quota-increase-request.md).

**Megnövelhető az adatbázis-korlát (100) száma a felügyelt példányon igény szerint?**

Nem, és jelenleg nincsenek véglegesítve a felügyelt SQL-példányon található adatbázisok számának növelésére vonatkozó véglegesített csomagok. 

**Hol lehet migrálni, ha több mint 8TB tárhely van?**
Érdemes lehet áttelepíteni más Azure-beli ízekre, amelyek megfelelnek a számítási feladatnak: [Azure SQL Database nagy kapacitású](../database/service-tier-hyperscale.md) vagy [SQL Server az Azure Virtual Machineson](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Hol lehet migrálni, ha bizonyos hardverkövetelmények, például nagyobb RAM-virtuális mag arány vagy több processzor?**
Érdemes lehet áttelepíteni [SQL Serverre az Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) vagy az optimalizált memória/CPU [Azure SQL Database](../database/sql-database-paas-overview.md) .

## <a name="known-issues--bugs"></a>Ismert problémák & hibák

**Hol találhatók az ismert problémák és hibák?**

A hibákkal és az ismert problémákkal kapcsolatban lásd: [ismert problémák](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Új funkciók

**Hol találhatók a legújabb funkciók és a szolgáltatások a nyilvános előzetes verzióban?**

Az új és az előzetes verziójú funkciókhoz lásd: [kibocsátási megjegyzések](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>SQL felügyelt példány létrehozása, frissítése, törlése vagy áthelyezése

**Hogyan lehet SQL felügyelt példányt kiépíteni?**

Üzembe helyezhet egy példányt az [Azure Portal](instance-create-quickstart.md), a [PowerShell](scripts/create-configure-managed-instance-powershell.md), az [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) és az [ARM sablonok](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)használatával.

**Létrehozhatok felügyelt példányokat egy meglévő előfizetésben?**

Igen, egy meglévő előfizetésben is kiépítheti a felügyelt példányokat, ha az előfizetés a [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types)közé tartozik.

**Miért nem sikerült olyan felügyelt példányt kiépíteni az alhálózatban, amelyben a név számjegytel kezdődik?**

Ez az alapul szolgáló összetevő aktuális korlátozása, amely ellenőrzi az alhálózat nevét a regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. A regexnek átadott nevek és az érvényes alhálózatok nevei jelenleg támogatottak.

**Hogyan lehet méretezni a felügyelt példányt?**

Felügyelt példányát az [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), a [PowerShell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), az [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) vagy az [ARM sablonok](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)segítségével méretezheti.

**Áthelyezhetem a felügyelt példányt az egyik régióból a másikba?**

Igen, írhat. Útmutatásért lásd: [erőforrások áthelyezése régiók között](../database/move-resources-across-regions.md).

**Hogyan törölhetem a felügyelt példányt?**

A felügyelt példányokat az Azure Portal, a [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0), az [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) vagy a [Resource Manager REST API](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)-k használatával törölheti.

**Mennyi ideig tart egy példány létrehozása vagy frissítése, vagy egy adatbázis visszaállítása?**

Az új felügyelt példány létrehozásához vagy a szolgáltatási szintek (virtuális mag, tárolás) módosításához szükséges idő számos tényezőtől függ. Lásd: [felügyeleti műveletek](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Elnevezési konvenciók

**A felügyelt példányok neve megegyezik egy SQL Server helyszíni példánnyal?**

A felügyelt példány nevének módosítása nem támogatott.

**Módosíthatom a DNS-zóna előtagját?**

Igen, felügyelt példány alapértelmezett DNS-zónája *. a Database.Windows.net* módosítható. 

Ha egy másik DNS-zónát szeretne használni az alapértelmezett helyett, például: *. contoso.com*: 
- Alias definiálása CliConfig használatával. Az eszköz csak a beállításjegyzék-beállítások burkolója, így a csoportházirend vagy egy parancsfájl használatával is elvégezhető.
- Használjon *CNAME* -t a *TrustServerCertificate = True* kapcsolóval.

## <a name="move-a-database-from-sql-managed-instance"></a>Adatbázis áthelyezése a felügyelt SQL-példányból 

**Hogyan helyezhetek át egy adatbázist az SQL felügyelt példányról SQL Server vagy Azure SQL Databasere?**

[Exportálhat egy adatbázist a BACPAC-be](../database/database-export.md) , majd [importálhatja a BACPAC-fájlt](../database/database-import.md). Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb.

A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

Az `COPY_ONLY` SQL felügyelt példányból készített natív biztonsági mentések nem állíthatók vissza SQL Serverre, mert az SQL felügyelt példánya nagyobb adatbázis-verzióval rendelkezik, mint SQL Server.

## <a name="migrate-an-instance-database"></a>Példány-adatbázis migrálása

**Hogyan telepíthetem át a példány-adatbázist Azure SQL Databasere?**

Az egyik lehetőség az, hogy [exportálja az adatbázist egy BACPAC](../database/database-export.md) , majd [importálja a BACPAC-fájlt](../database/database-import.md). 

Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb. A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

## <a name="switch-hardware-generation"></a>Hardver generálásának váltása 

**Válthatok-e az SQL felügyelt példányának hardveres generációja a 4. és az 5. generációs online kapcsolat között?**

A hardveres generációk közötti automatizált online váltás akkor lehetséges, ha mindkét hardveres generáció elérhető abban a régióban, ahol az SQL felügyelt példánya van kiépítve. Ebben az esetben megtekintheti a [virtuális mag-modell áttekintése lapot](../database/service-tiers-vcore.md), amely ismerteti, hogyan válthat a hardveres generációk között.

Ez egy hosszan futó művelet, mivel új felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan átkerülnek a régi és az új példányok között, és a folyamat végén gyors feladatátvételt végeznek. 

**Mi a teendő, ha a hardveres generációk nem támogatottak ugyanabban a régióban?**

Ha a hardveres generációk nem támogatottak ugyanabban a régióban, akkor a hardveres generáció módosítása lehetséges, de manuálisan kell elvégezni. Ehhez olyan új példányt kell kiépíteni a régióban, ahol a kívánt hardver-létrehozás elérhető, és manuálisan végezheti el az adatbiztonsági mentést és visszaállítást a régi és az új példányok között.

**Mi a teendő, ha nincs elegendő IP-cím a frissítési művelet végrehajtására?**

Abban az esetben, ha nincs elegendő IP-cím abban az alhálózatban, amelyben a felügyelt példányt kiépíti, létre kell hoznia egy új alhálózatot és egy új felügyelt példányt. Azt is javasoljuk, hogy az új alhálózat több IP-címmel lett létrehozva, így a későbbi frissítési műveletek elkerülik a hasonló helyzeteket. (A megfelelő alhálózatok méretének [meghatározásához tekintse meg az VNet-alhálózat méretét](vnet-subnet-determine-size.md).) Az új példány üzembe helyezése után manuálisan végezheti el az adatok biztonsági mentését és visszaállítását a régi és az új példányok között, vagy elvégezheti a példányok közötti [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Teljesítmény hangolása

**Hogyan a felügyelt SQL-példány teljesítményének finomhangolását?**

Az SQL felügyelt példánya a általános célú szinten a távoli tárterületet használja, így az adat-és naplófájlok mérete a teljesítményre is kiterjed. További információ: a [naplófájl méretének hatása általános célú SQL felügyelt példány teljesítményére](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Ha a munkaterhelés sok kis tranzakcióból áll, érdemes lehet átváltani a csatlakozás típusát a proxyról átirányítási módba.

## <a name="maximum-storage-size"></a>Maximális tárterület

**Mekkora a maximális tárolási méret az SQL felügyelt példány esetében?**

Az SQL felügyelt példányának tárolási mérete a kiválasztott szolgáltatási szinttől (általános célú vagy üzletileg kritikus) függ. A szolgáltatási szintek tárolási korlátozásai a [szolgáltatási szintek jellemzői](../database/service-tiers-general-purpose-business-critical.md)című részben olvashatók.

  
## <a name="networking-requirements"></a>Hálózati követelmények 

**Mik a jelenlegi bejövő/kimenő NSG megkötései a felügyelt példány alhálózatán?**

A szükséges NSG-és UDR-szabályokat [itt](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)dokumentálja, és automatikusan beállítja a szolgáltatás.
Ne feledje, hogy ezek a szabályok csak a szolgáltatás karbantartásához szükségesek. A felügyelt példányhoz való kapcsolódáshoz és a különböző funkciók használatához további, a szolgáltatásra vonatkozó szabályokat kell megadnia, amelyeket meg kell őriznie.

**Hogyan állíthatok be bejövő NSG szabályokat a felügyeleti portokon?**

A felügyeleti portok szabályainak beállítása a felügyelt SQL-példány feladata. Ez a [szolgáltatás által támogatott alhálózat-konfiguráció](connectivity-architecture-overview.md#service-aided-subnet-configuration)nevű funkcióval érhető el.
Ezzel biztosítható, hogy a felügyeleti forgalom zavartalanul elérhető legyen az SLA teljesítése érdekében.

**Lekérhetem a bejövő felügyeleti forgalomhoz használt forrás IP-tartományokat?**

Igen. A hálózati biztonsági csoporton keresztül érkező forgalmat a [Network Watcher flow-naplók konfigurálásával](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group)elemezheti.

**Beállíthat NSG az adatvégponthoz való hozzáférés szabályozásához (1433-es port)?**

Igen. Egy felügyelt példány üzembe helyezése után beállíthat olyan NSG, amelyek a 1433-es porthoz való bejövő hozzáférést vezérlik. Javasoljuk, hogy a lehető legnagyobb mértékben Szűkítse az IP-tartományt.

**Beállítható a NVA vagy a helyszíni tűzfal a kimenő felügyeleti forgalom szűrésére a teljes tartománynevek alapján?**

Nem. Ez számos okból nem támogatott:
-   A bejövő felügyeleti kérelemre válaszként szolgáló útválasztási forgalom aszimmetrikus, és nem működik.
-   A tárterületre kerülő útválasztási forgalmat a rendszer az átviteli korlátok és a késés miatt befolyásolja, így a várt szolgáltatás minősége és rendelkezésre állása nem lesz elérhető.
-   A tapasztalatok alapján ezek a konfigurációk a hibás és nem támogatottak.

**Megadhatom a NVA vagy a tűzfalat a kimenő nem felügyeleti forgalomhoz?**

Igen. Ennek a legegyszerűbb módja, ha 0/0 szabályt ad hozzá a felügyelt példány alhálózatához társított UDR, hogy átirányítsa a forgalmat a NVA-on keresztül.
 
**Hány IP-címre van szükség egy felügyelt példányhoz?**

Az alhálózatnak elegendő számú elérhető [IP-címmel](connectivity-architecture-overview.md#network-requirements)kell rendelkeznie. A felügyelt példányhoz tartozó VNet-alhálózat méretének meghatározásához lásd: a [szükséges alhálózat méretének és tartományának meghatározása a felügyelt példányhoz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**Mi a teendő, ha nincs elegendő IP-cím a példány-frissítési művelet végrehajtására?**

Abban az esetben, ha nincs elegendő [IP-cím](connectivity-architecture-overview.md#network-requirements) abban az alhálózatban, amelyben a felügyelt példányt kiépíti, létre kell hoznia egy új alhálózatot és egy új felügyelt példányt. Azt is javasoljuk, hogy az új alhálózat több IP-címmel lett létrehozva, így a későbbi frissítési műveletek elkerülik a hasonló helyzeteket. Az új példány üzembe helyezése után manuálisan végezheti el az adatok biztonsági mentését és visszaállítását a régi és az új példányok között, vagy elvégezheti a példányok közötti [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell).

**Szükség van egy üres alhálózatra a felügyelt példány létrehozásához?**

Nem. Használhat egy üres alhálózatot vagy egy olyan alhálózatot, amely már tartalmazza a felügyelt példány (oka) t. 

**Módosíthatom az alhálózat címtartományt?**

Nem, ha vannak felügyelt példányok belül. Ez egy Azure hálózati infrastruktúra korlátozása. Csak [egy üres alhálózathoz adhat hozzá további címterület-helyet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Áthelyezhetem a felügyelt példányt egy másik alhálózatra?**

Nem. Ez a felügyelt példányok jelenlegi tervezési korlátozása. Létrehozhat azonban egy új példányt egy másik alhálózatban, és manuálisan is elvégezheti az adatok biztonsági mentését és visszaállítását a régi és az új példány között, vagy egy példányon belüli [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell)végezhet.

**A felügyelt példány létrehozásához szükség van egy üres virtuális hálózatra?**

Ez nem kötelező. [Létrehozhat egy virtuális hálózatot az Azure SQL felügyelt példányához](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) , vagy [beállíthat egy meglévő virtuális hálózatot az Azure SQL felügyelt példányaihoz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Helyezhetek felügyelt példányt más szolgáltatásokkal egy alhálózaton?**

Nem. Jelenleg nem támogatott olyan felügyelt példány elhelyezése olyan alhálózatban, amely már tartalmaz más típusú erőforrásokat.


## <a name="mitigate-data-exfiltration-risks"></a>Az adatkiszűrése kockázatok enyhítése  

**Hogyan csökkentheti az adatkiszűrése kockázatait?**

A kiszűrése kockázatok enyhítése érdekében az ügyfeleknek ajánlott biztonsági beállításokat és vezérlőket alkalmaznia:

- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) bekapcsolása az összes adatbázison.
- A közös nyelvi futtatókörnyezet (CLR) kikapcsolása. Ez ajánlott a helyszínen is.
- Csak Azure Active Directory (Azure AD-) hitelesítést használjon.
- A példányt egy alacsony jogosultsági szintű DBA-fiókkal érheti el.
- Állítsa be az JIT Jumpbox hozzáférését a sysadmin fiókhoz.
- Kapcsolja be az [SQL-naplózást](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine), és integrálja azt a riasztási mechanizmusokkal.
- A [veszélyforrások észlelésének](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) bekapcsolása a [speciális adatbiztonsági (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) csomagból.


## <a name="cost-saving-use-cases"></a>Költségtakarékos használati esetek

**Hol találom a használati eseteket és a költségmegtakarítást az SQL felügyelt példányával?**

SQL felügyelt példányok esettanulmányai:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Ha jobban meg szeretné ismerni az Azure SQL felügyelt példányának üzembe helyezéséhez kapcsolódó előnyöket, költségeket és kockázatokat, akkor is van egy Forrester-tanulmány: [Microsoft Azure SQL Database felügyelt példány teljes gazdasági hatása](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns"></a>DNS

**Beállíthat egyéni DNS-t az SQL felügyelt példányaihoz?**

Igen. Lásd: [Egyéni DNS konfigurálása az Azure SQL felügyelt példányaihoz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Használhatok DNS-frissítést?**

Jelenleg nem biztosítunk olyan szolgáltatást, amely frissíti a DNS-kiszolgáló konfigurációját a felügyelt SQL-példányhoz.

A DNS-konfigurációt a rendszer végül frissíti:

- A DHCP-bérlet lejárta után.
- A platform verziófrissítése.

Megkerülő megoldásként az SQL felügyelt példányát 4 virtuális mag, majd később frissítse. Ez a DNS-konfiguráció frissítésének mellékhatása.


## <a name="ip-address"></a>IP-cím

**Csatlakozhatok az SQL felügyelt példányhoz egy IP-cím használatával?**

Az SQL felügyelt példányhoz való csatlakozás IP-cím használatával nem támogatott. A felügyelt SQL-példány állomásneve az SQL felügyelt példányának virtuális fürtje előtt leképezi a terheléselosztó nevét. Mivel egy virtuális fürt több felügyelt példányt is tárolhat, a kapcsolatok nem irányíthatók át a megfelelő felügyelt példányra anélkül, hogy explicit módon megadta a nevet.

További információ az SQL felügyelt példányának virtuális fürtjének architektúráról: [virtuális fürt kapcsolati architektúrája](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**A felügyelt SQL-példányok statikus IP-címmel rendelkeznek?**

Ritka, de szükséges helyzetekben szükség lehet az SQL felügyelt példány online áttelepítésére egy új virtuális fürtre. Ha szükséges, az áttelepítés a technológiai verem változásai miatt a szolgáltatás biztonságának és megbízhatóságának javítását célozza. Az új virtuális fürtre való Migrálás az SQL felügyelt példány állomásneve számára leképezett IP-cím módosítását eredményezi. A felügyelt SQL-példányok szolgáltatás nem igényel statikus IP-cím-támogatást, és fenntartja a jogot arra, hogy a normál karbantartási ciklusok részeként előzetes értesítés nélkül módosítsa azt.

Ezért erősen visszatartjuk az IP-cím módosíthatatlansági, mivel ez szükségtelen állásidőt eredményezhet.

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő felügyelt példány időzónáját?**

Az időzóna-konfiguráció akkor állítható be, ha a felügyelt példányok első alkalommal lettek kiépítve. Egy meglévő felügyelt példány időzónájának módosítása nem támogatott. Részletekért lásd: [időzóna korlátozásai](timezones-overview.md#limitations).

A megkerülő megoldások közé tartozik egy új felügyelt példány létrehozása a megfelelő időzónával, majd manuális biztonsági mentés és visszaállítás végrehajtása, vagy az általunk ajánlott megoldás, amely egy [példányos időpontra történő visszaállítást](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)végez.


## <a name="security-and-database-encryption"></a>Biztonság és adatbázis-titkosítás

**Elérhető-e az SQL felügyelt példányának sysadmin kiszolgálói szerepköre?**

Igen, az ügyfelek létrehozhatnak olyan bejelentkezéseket, amelyek a sysadmin (rendszergazda) szerepkör tagjai.  A sysadmin (rendszergazda) jogosultságot feltételező ügyfelek a példány üzemeltetésének felelősségét is feltételezik, amely negatív hatással lehet az SLA-ra. A sysadmin (rendszergazda) kiszolgálói szerepkörbe való bejelentkezéshez lásd: [Azure ad-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication).

**Támogatott az SQL felügyelt példánya transzparens adattitkosítás?**

Igen, transzparens adattitkosítás támogatott az SQL felügyelt példányain. Részletekért lásd: [transzparens adattitkosítás a felügyelt SQL-példányhoz](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal).

**Kihasználhatom a "saját kulcs használata" modellt a TDE?**

Igen, a BYOK forgatókönyvhöz Azure Key Vault érhető el az Azure SQL felügyelt példányaihoz. Részletekért lásd: [transzparens adattitkosítás ügyfél által felügyelt kulccsal](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Áttelepíthetek egy titkosított SQL Server adatbázist?**

Igen, írhat. Titkosított SQL Server-adatbázis átirányításához exportálnia és importálnia kell a meglévő tanúsítványokat a felügyelt példányba, majd készítsen egy teljes adatbázis biztonsági mentést, és állítsa vissza a felügyelt példányban. 

[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) használatával is áttelepítheti a TDE titkosított adatbázisait.

**Hogyan állíthatom be a TDE Protector rotációját az SQL felügyelt példányaihoz?**

Azure Cloud Shell használatával elforgathatja a felügyelt példányhoz tartozó TDE-védőt. Útmutatásért lásd: [transzparens adattitkosítás az SQL felügyelt példányában a saját kulcsának használatával Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Visszaállíthatom a titkosított adatbázist a felügyelt SQL-példányra?**

Igen, nem kell visszafejtenie az adatbázist az SQL felügyelt példányra való visszaállításhoz. Meg kell adnia egy tanúsítványt/kulcsot, amelyet titkosítási kulcsként kell használni a forrásrendszer SQL felügyelt példánya számára, hogy el tudja olvasni az adatait a titkosított biztonságimásolat-fájlból. Két lehetséges módszer:

- *Tanúsítvány feltöltése – a felügyelt SQL-példányra*. Ezt csak a PowerShell használatával lehet elvégezni. A [minta parancsfájl](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) a teljes folyamatot ismerteti.
- *Töltse fel az aszimmetrikus kulcs-védőt, hogy Azure Key Vault, és irányítsa az SQL felügyelt példányát*. Ez a megközelítés hasonlít a saját kulcsú (BYOK) TDE használatára, amely a titkosítási kulcs tárolásához a Key Vault-integrációt is használja. Ha nem szeretné a kulcsot titkosítási kulcsként használni, és csak azt szeretné, hogy elérhető legyen az SQL felügyelt példánya a titkosított adatbázis (ok) visszaállításához, kövesse az BYOK- [TDE beállításához](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)szükséges utasításokat, és ne jelölje be a jelölőnégyzetet, **hogy a kijelölt kulcs legyen az alapértelmezett TDE-védő**.

Miután a titkosítási védő elérhetővé vált az SQL felügyelt példánya számára, folytathatja a szabványos adatbázis-visszaállítási eljárást.

## <a name="purchasing-models-and-benefits"></a>Modellek és előnyök vásárlása

**Milyen beszerzési modellek érhetők el az SQL felügyelt példányaihoz?**

Az SQL felügyelt példánya [virtuális mag-alapú vásárlási modellt](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)kínál.

**Milyen költségadatok érhetők el az SQL felügyelt példányaihoz?**

A költségeket az alábbi módokon mentheti az Azure SQL-előnyökkel:
-   Maximalizálja a meglévő beruházásokat a helyszíni licencekben, és akár 55%-ot is megtakaríthat [Azure Hybrid Benefit](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Véglegesítse a számítási erőforrások foglalását, és akár 33%-ot is megtakaríthat a [fenntartott példányok előnyeivel](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Az Azure Hybrid benefittel akár 82%-os megtakarítást is használhat. 
-   Az [Azure dev/test díjszabási juttatásával](https://azure.microsoft.com/pricing/dev-test/) akár 55%-ot is megtakaríthat, amely kedvezményes díjszabást biztosít a folyamatos fejlesztési és tesztelési feladatokhoz.

**Ki jogosult a fenntartott példányokra?**

Ahhoz, hogy jogosult legyen a fenntartott példányok juttatására, az előfizetés típusának nagyvállalati szerződésnek (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy az utólagos elszámolású díjszabással rendelkező egyedi szerződésnek (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P) kell lennie. A foglalásokkal kapcsolatos további információkért lásd a [fenntartott példányok juttatása](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)című témakört. 

**Lehetséges a foglalások megszakítása, cseréje vagy visszatérítése?**

Bizonyos korlátozásokkal megszakíthatja, kicserélheti vagy visszafizetheti a foglalásokat. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="billing-for-managed-instance-and-backup-storage"></a>A felügyelt példány és a biztonsági mentési tár számlázása

**Mik az SQL felügyelt példány díjszabási lehetőségei?**

A felügyelt példány díjszabási lehetőségeinek megismeréséhez tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Hogyan követhetem nyomon a felügyelt példányom számlázási költségeit?**

Ezt a [Azure Cost Management megoldás](https://docs.microsoft.com/azure/cost-management-billing/)használatával teheti meg. Navigáljon a [Azure Portal](https://portal.azure.com) **előfizetésekhez** , és válassza a **Cost Analysis**lehetőséget. 

Használja a **halmozott költségek** lehetőséget, majd az erőforrástípus szerint szűrje az **értéket** `microsoft.sql/managedinstances` .

**Mennyibe kerül az automatizált biztonsági mentés?**

A biztonsági mentési tárolóhelyek mennyiségét a megvásárolt fenntartott tárterületnek megfelelően kapja meg, a biztonsági másolatok megőrzési időszaka beállítástól függetlenül. Ha a biztonsági mentési tárhely a lefoglalt ingyenes biztonsági mentési tárolóhelyen belül van, akkor a felügyelt példányok automatizált biztonsági mentései nem lesznek további költségek, ezért díjmentes. A biztonsági mentési tár a szabad terület feletti használatának meghaladása esetén az USA-beli régiókban körülbelül $0,20 – $0,24 GB/hó összegű költséget eredményez, vagy a régió részleteiért tekintse meg a díjszabási oldalt. További részletekért lásd: a [biztonsági másolatok tárolási felhasználásának magyarázata](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Hogyan figyelhető meg a biztonsági mentési tárhely-felhasználás számlázási díja?**

A biztonsági mentési tár költségeit az Azure Portalon figyelheti. Útmutatásért lásd: [az automatikus biztonsági mentések költségeinek figyelése](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Hogyan optimalizálható a biztonsági másolatok tárolási költségei a felügyelt példányon?**

A biztonsági másolatok tárolási költségeinek optimalizálásához tekintse [meg a biztonsági mentés finomhangolása az SQL felügyelt példányon](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)című témakört.

## <a name="password-policy"></a>Jelszóházirend 

**Milyen jelszavas szabályzatok vonatkoznak az SQL felügyelt példány SQL-bejelentkezésekre?**

Az SQL-bejelentkezések SQL-alapú felügyelt példányának jelszavas szabályzata örökli a felügyelt példányt tároló virtuális fürtöket alkotó virtuális gépekre alkalmazott Azure platform-házirendeket. Jelenleg nem lehet módosítani ezeket a beállításokat, mivel ezeket a beállításokat az Azure definiálja, és a felügyelt példány örökli.

 > [!IMPORTANT]
 > Az Azure platform megváltoztathatja a házirendre vonatkozó követelményeket anélkül, hogy a szolgáltatásokat a szabályzatokra támaszkodva értesíti.

**Mik a jelenlegi Azure platform-szabályzatok?**

Minden bejelentkezéskor be kell állítania a jelszavát a bejelentkezés után, és módosítania kell a jelszavát, miután elérte a maximális kort.

| **Szabályzat** | **Biztonsági beállítás** |
| --- | --- |
| Jelszó maximális kora | 42 nap |
| Jelszó minimális kora | 1 nap |
| Jelszó minimális hossza | 10 karakter |
| A jelszónak meg kell felelnie a bonyolultsági követelményeknek | Engedélyezve |

**Le lehet tiltani a jelszó bonyolultságát és a lejáratot az SQL felügyelt példányain a bejelentkezési szinten?**

Igen, a bejelentkezési szinten CHECK_POLICY és CHECK_EXPIRATION mezőket is szabályozhatja. Az aktuális beállításokat a következő T-SQL-parancs végrehajtásával tekintheti meg:

```sql
SELECT *
FROM sys.sql_logins
```

Ezt követően a következő végrehajtásával módosíthatja a megadott bejelentkezési beállításokat:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(cserélje le a "test" értéket a kívánt bejelentkezési névvel, és állítsa be a szabályzatot és a lejárati értékeket)

## <a name="azure-feedback-and-support"></a>Azure-visszajelzés és-támogatás

**Honnan hagyhatom el az SQL felügyelt példányok tökéletesítésével kapcsolatos ötleteket?**

Szavazzon egy új felügyelt példány-szolgáltatásra, vagy tegyen fel új fejlesztési elképzelést az [SQL felügyelt példányok visszajelzési fórumának](https://feedback.azure.com/forums/915676-sql-managed-instance)szavazásakor. Ily módon hozzájárulhat a termék fejlesztéséhez, és segítheti a lehetséges fejlesztések rangsorolását.

**Hogyan hozhatok létre Azure-támogatási kérést?**

Az Azure-támogatási kérelem létrehozásáról az Azure- [támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben olvashat bővebben.

