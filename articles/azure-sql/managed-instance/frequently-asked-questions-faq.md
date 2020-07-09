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
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135021"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL felügyelt példányok – gyakori kérdések (GYIK)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk az [Azure SQL felügyelt példányának](sql-managed-instance-paas-overview.md)leggyakoribb kérdéseit tartalmazza.

## <a name="supported-features"></a>Támogatott funkciók

**Hol találhatom meg a felügyelt SQL-példányok által támogatott szolgáltatások listáját?**

A felügyelt SQL-példány támogatott funkcióinak listáját lásd: az [Azure SQL felügyelt példányának funkciói](../database/features-comparison.md).

A felügyelt Azure SQL-példány és a SQL Server szintaxisának és viselkedésének különbségeit lásd: a [T-SQL eltérései SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Technikai spec & erőforrás-korlátok
 
**Hol találhatók a felügyelt SQL-példányok technikai jellemzői és erőforrás-korlátai?**

Az elérhető hardver-létrehozási jellemzőkkel kapcsolatban lásd: [technikai különbségek a hardveres generációkban](resource-limits.md#hardware-generation-characteristics).
A rendelkezésre álló szolgáltatási szintek és azok jellemzői a [szolgáltatási szintek közötti technikai különbségek](resource-limits.md#service-tier-characteristics)című részben olvashatók.

## <a name="known-issues--bugs"></a>Ismert problémák & hibák

**Hol találhatók az ismert problémák és hibák?**

A hibákkal és az ismert problémákkal kapcsolatban lásd: [ismert problémák](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Új funkciók

**Hol találhatók a legújabb funkciók és a szolgáltatások a nyilvános előzetes verzióban?**

Az új és az előzetes verziójú funkciókhoz lásd: [kibocsátási megjegyzések](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Üzembe helyezési idők 

**Mennyi idő szükséges a példány létrehozásához vagy frissítéséhez, vagy egy adatbázis visszaállításához?**

A felügyelt példány létrehozásához vagy a szolgáltatási szint (virtuális mag, tárterület) módosításához szükséges idő számos tényezőtől függ. Vessen egy pillantást a [kezelési műveletekre](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Elnevezési konvenciók

**A felügyelt példányok neve megegyezik egy SQL Server helyszíni példánnyal?**

A felügyelt példány nevének módosítása nem támogatott.

A felügyelt példányok alapértelmezett DNS-zónája módosítható. *Database.Windows.net* . 

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

## <a name="connectivity"></a>Kapcsolatok 

**Tudok csatlakozni a felügyelt példányhoz IP-cím használatával?**

Nem, ez nem támogatott. A felügyelt példány állomásneve a felügyelt példány virtuális fürtje előtt leképezi a terheléselosztó nevét. Mivel egy virtuális fürt több felügyelt példányt is tárolhat, a kapcsolat nem irányítható át a megfelelő felügyelt példányra a név megadása nélkül.
További információ az SQL felügyelt példányának virtuális fürtjének architektúráról: [virtuális fürt kapcsolati architektúrája](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Rendelkezhetnek felügyelt példányok statikus IP-címmel?**

Ez jelenleg nem támogatott.

Ritka, de szükséges helyzetekben előfordulhat, hogy egy felügyelt példány online áttelepítését egy új virtuális fürtre kell elvégezni. Ha szükséges, az áttelepítés a technológiai verem változásai miatt a szolgáltatás biztonságának és megbízhatóságának javítását célozza. Az új virtuális fürtre való Migrálás a felügyelt példány állomásneve számára leképezett IP-cím módosítását eredményezi. A felügyelt példány szolgáltatás nem igényel statikus IP-cím támogatását, és fenntartja a jogot arra, hogy a rendszeres karbantartási ciklusok részeként előzetes értesítés nélkül módosítsa.

Ezért erősen visszatartjuk az IP-cím módosíthatatlansági, mivel ez szükségtelen állásidőt eredményezhet.

**A felügyelt példány nyilvános végponttal rendelkezik?**

Igen. A felügyelt példány olyan nyilvános végponttal rendelkezik, amelyet alapértelmezés szerint csak a Service Management szolgáltatáshoz használ, de az ügyfél is engedélyezheti az adatelérést. További részletek: [SQL felügyelt példány használata nyilvános végpontokkal](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). A nyilvános végpont konfigurálásához nyissa meg a [nyilvános végpont konfigurálása az SQL felügyelt példányban című részt](public-endpoint-configure.md).

**Hogyan szabályozza a felügyelt példány a nyilvános végponthoz való hozzáférést?**

A felügyelt példány a hálózat és az alkalmazás szintjén szabályozza a nyilvános végpont elérését.

A felügyeleti és központi telepítési szolgáltatások egy felügyelt példányhoz csatlakoznak egy olyan [felügyeleti végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) használatával, amely egy külső terheléselosztó számára van leképezve. A rendszer csak akkor irányítja a forgalmat a csomópontokhoz, ha egy előre meghatározott porton érkezik, amely csak a felügyelt példány felügyeleti összetevői által használt. A csomópontokon a beépített tűzfal úgy van beállítva, hogy csak a Microsoft IP-címtartományok forgalmát engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt. További részletek: [az SQL felügyelt példányának kapcsolati architektúrája](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**Használhatom a nyilvános végpontot a felügyelt példányok adatbázisaiban lévő adathozzáféréshez?**

Igen. Az ügyfélnek engedélyeznie kell a nyilvános végponti adatok elérését az [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM-ből, és konfigurálnia kell a NSG az adatporthoz való hozzáférés zárolásához (3342-es portszám). További információkért lásd: [nyilvános végpont konfigurálása az Azure SQL felügyelt példányában](public-endpoint-configure.md) , és az [Azure SQL felügyelt példány biztonságos használata nyilvános végponttal](public-endpoint-overview.md). 

**Megadhatok egyéni portot az SQL-adatvégpont (ok) hoz?**

Nem, ez a lehetőség nem érhető el.  A privát adatvégpont esetében a felügyelt példány a 1433 alapértelmezett portszámot használja, a nyilvános adatvégpont esetében pedig a felügyelt példány a 3342-as alapértelmezett portszámot használja.

**Mi a javasolt módszer a különböző régiókba helyezett felügyelt példányok összekapcsolására?**

Az Express Route Circuit-társítás az előnyben részesített módszer. Ez nem keverhető a belső terheléselosztással kapcsolatos [korlátozás](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)miatt nem támogatott régiók közötti virtuális hálózattal.

Ha az expressz útvonal-összevonási kapcsolat nem lehetséges, az egyetlen lehetőség, hogy helyek közötti VPN-kapcsolatot ([Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal), [POWERSHELL](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell), [Azure CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)) hozzon létre.


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


## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő felügyelt példány időzónáját?**

Az időzóna-konfiguráció akkor állítható be, ha a felügyelt példányok első alkalommal lettek kiépítve. Egy meglévő felügyelt példány időzónájának módosítása nem támogatott. Részletekért lásd: [időzóna korlátozásai](timezones-overview.md#limitations).

A megkerülő megoldások közé tartozik egy új felügyelt példány létrehozása a megfelelő időzónával, majd manuális biztonsági mentés és visszaállítás végrehajtása, vagy az általunk ajánlott megoldás, amely egy [példányos időpontra történő visszaállítást](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)végez.


## <a name="resolve-performance-issues"></a>Teljesítménnyel kapcsolatos problémák megoldása

**Hogyan az SQL felügyelt példányával kapcsolatos teljesítményproblémák megoldását?**

A felügyelt SQL-példány és a SQL Server közötti teljesítmény-összehasonlításhoz jó kiindulási pont az [Azure SQL felügyelt példánya és a SQL Server közötti teljesítmény-összehasonlítás ajánlott eljárásai](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Az adatok betöltése gyakran lassabb a felügyelt SQL-példányon, mint SQL Server a kötelező teljes helyreállítási modell és a tranzakciós napló írási sebességének [korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) miatt. Néha ez a megoldás az átmeneti adatoknak a felhasználói adatbázis helyett a tempdb való betöltésével, vagy fürtözött oszlopcentrikus vagy memóriára optimalizált táblák használatával történő betöltésével végezhető el.


## <a name="restore-encrypted-backup"></a>Titkosított biztonsági mentés visszaállítása

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

