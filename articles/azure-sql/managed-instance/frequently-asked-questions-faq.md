---
title: Gyakori kérdések (GYIK)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL SQL felügyelt példány – gyakori kérdések (GYIK)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: b72952618b2d024bd2c4b445c3ea673ed523866b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247937"
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

Hibák és ismert problémák esetén tekintse meg az [ismert problémákat](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Új funkciók

**Hol találhatók a legújabb funkciók és a szolgáltatások a nyilvános előzetes verzióban?**

Az új és az előzetes verziójú funkciókhoz lásd: [kibocsátási megjegyzések](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Üzembe helyezési idők 

**Mennyi idő szükséges a példány létrehozásához vagy frissítéséhez, vagy egy adatbázis visszaállításához?**

A felügyelt SQL-példányok létrehozásának vagy a szolgáltatási szint (virtuális mag, tárterület) változásának várható ideje számos tényezőtől függ. Tekintse meg a [kezelési műveleteket](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Elnevezési konvenció

**A felügyelt SQL-példányok neve megegyezik a helyszíni SQL Server példánnyal?**

Az SQL felügyelt példány nevének módosítása nem támogatott.

Az SQL felügyelt példányának alapértelmezett DNS-zónája *. a Database.Windows.net* módosítható. 

Ha egy másik DNS-zónát szeretne használni az alapértelmezett helyett, például: *. contoso.com*: 
- Alias definiálása CliConfig használatával. Az eszköz csak a beállításjegyzék-beállítások burkolója, így a csoportházirend vagy a parancsfájl használatával is elvégezhető.
- *CNAME* használata a *TrustServerCertificate = True* kapcsolóval.

## <a name="move-db-from-mi"></a>ADATBÁZIS áthelyezése a MIből 

**Hogyan helyezhetek át adatbázist a felügyelt SQL-példányról SQL Server vagy Azure SQL Databasere?**

[Exportálhatja az adatbázist a BACPAC-be](../database/database-export.md) , majd [importálhatja a BACPAC-fájlt]( ../database/database-import.md). Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb.

A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

Az `COPY_ONLY` SQL felügyelt példányból készített natív biztonsági mentések nem állíthatók vissza SQL Serverre, mert az SQL felügyelt példánya nagyobb adatbázis-verzióval rendelkezik, mint SQL Server.

## <a name="migrate-instance-db"></a>Példány-adatbázis migrálása

**Hogyan telepíthetem át a példány-adatbázist egyetlen Azure SQL Databasere?**

Az egyik lehetőség az, hogy [exportálja az adatbázist egy BACPAC](../database/database-export.md) , majd [importálja a BACPAC-fájlt](../database/database-import.md). 

Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb. A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

## <a name="switch-hardware-generation"></a>Hardver generálásának váltása 

**Válthatok-e az SQL felügyelt példányának hardveres generációja a 4. és az 5. generációs online kapcsolat között?**

A hardveres generációk közötti automatizált online váltás akkor lehetséges, ha mindkét hardveres generáció elérhető abban a régióban, ahol az SQL felügyelt példánya van kiépítve. Ebben az esetben megtekintheti a [virtuális mag-modell áttekintését ismertető oldalt](../database/service-tiers-vcore.md) , amely ismerteti, hogyan válthat a hardveres generációk között.

Ez egy hosszan futó művelet, mivel új SQL felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan átkerülnek a régi és az új példány között a folyamat végén található gyors feladatátvételsel. 

**Mi a teendő, ha a hardveres generációk nem támogatottak ugyanabban a régióban?**

Ha a hardveres generációk nem támogatottak ugyanabban a régióban, akkor a hardveres generáció módosítása lehetséges, de manuálisan kell elvégezni. Ehhez olyan új példányt kell kiépíteni a régióban, ahol a kívánt hardver-létrehozás elérhető, és manuálisan végezheti el a régi és az új példány közötti biztonsági mentést és visszaállítást.

**Mi a teendő, ha nincs elegendő IP-cím a frissítési művelet végrehajtására?**

Ha nincs elegendő IP-cím abban az alhálózatban, amelyben a felügyelt példányt kiépítik, akkor létre kell hoznia egy új alhálózatot és egy új felügyelt példányt. Azt is javasoljuk, hogy az új alhálózat több IP-címmel legyen létrehozva, így a jövőbeli frissítési műveletek elkerülik a hasonló helyzetet (a megfelelő alhálózatok méretéhez, és a [vnet alhálózat méretének meghatározásához](vnet-subnet-determine-size.md). Az új példány üzembe helyezése után manuálisan végezheti el az adatok biztonsági mentését és visszaállítását a régi és az új példány között, vagy elvégezheti a példányok közötti [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Teljesítmény hangolása

**Hogyan a felügyelt SQL-példány teljesítményének finomhangolását?**

Általános célú SQL felügyelt példánya a távoli tárterületet használja, mert az adatmennyiség és a naplófájlok teljesítménye fontos. További információ: a [naplófájl méretének hatása általános célú SQL felügyelt példány teljesítményére](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Ha a munkaterhelés sok kis tranzakcióból áll, érdemes lehet átváltani a csatlakozás típusát a proxyról átirányítási módba.

## <a name="maximum-storage-size"></a>Maximális tárterület

**Mekkora a maximális tárolási méret az SQL felügyelt példány esetében?**

Az SQL felügyelt példányának tárolási mérete a kiválasztott szolgáltatási szinttől (általános célú vagy üzletileg kritikus) függ. A szolgáltatási szintek tárolási korlátozásait lásd: [szolgáltatási szintek jellemzője](../database/service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Tárolási díj biztonsági mentése 

**A biztonsági mentési tárterület le lett vonva az SQL felügyelt példányok tárterületéről?**

Nem, a biztonsági mentési tár nem vonható le az SQL felügyelt példányának tárterületéről. A biztonsági mentési tár a példány tárolóhelytől független, és nem korlátozódik a méretre. A biztonsági mentési tárterületet a példány-adatbázisok biztonsági mentésének időtartamára korlátozza, amely 7 – 35 napig állítható be. Részletekért lásd: [automatizált biztonsági mentések](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Számlázás követése

**Van lehetőség az SQL felügyelt példányom számlázási díjainak nyomon követésére?**

Ezt a [Azure Cost Management megoldás](/azure/cost-management/)használatával teheti meg. Navigáljon a [Azure Portal](https://portal.azure.com) **előfizetésekhez** , és válassza a **Cost Analysis**lehetőséget. 

Használja a **halmozott költségek** lehetőséget, majd az erőforrástípus szerint szűrje az **értéket** `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Bejövő NSG szabályok

**Hogyan állíthatok be bejövő NSG szabályokat a felügyeleti portokon?**

Az SQL SQL felügyelt példányának vezérlője a felügyeleti portok felügyeletére szolgáló NSG-szabályokat tartja karban.

A következő felügyeleti portok használatosak:

Service Fabric infrastruktúra a 9000-es és a 9003-es portot használja. Service Fabric elsődleges szerepkör, hogy a virtuális fürtöt Kifogástalan állapotba kell tartani, és az összetevő-replikák száma alapján meg kell őrizni a cél állapotát.

A csomópont-ügynök a 1438, 1440 és 1452 portokat használja. A csomópont-ügynök egy olyan alkalmazás, amely a fürtön belül fut, és a vezérlési sík használja a felügyeleti parancsok végrehajtásához.

A NSG-szabályok mellett a beépített tűzfal védi a példányt a hálózati rétegben. Az alkalmazás rétegének kommunikációja védett a tanúsítványokkal.
  
További információ és a beépített tűzfal ellenőrzése: az [Azure SQL felügyelt példányának beépített tűzfala](management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Az adatkiszűrése kockázatok enyhítése  

**Hogyan csökkentheti az adatkiszűrése kockázatait?**

A kiszűrése kockázatok enyhítése érdekében az ügyfeleknek ajánlott biztonsági beállításokat és vezérlőket alkalmaznia:

- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) bekapcsolása az összes adatbázison.
- A közös nyelvi futtatókörnyezet (CLR) kikapcsolása. Ez ajánlott a helyszínen is.
- Csak Azure Active Directory (HRE) hitelesítést használjon.
- Hozzáférési példány alacsony jogosultsági szintű DBA-fiókkal.
- Állítsa be a JiT Jumpbox-hozzáférést a sysadmin fiókhoz.
- Kapcsolja be az [SQL-naplózást](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine), és integrálja azt a riasztási mechanizmusokkal.
- Kapcsolja be a [fenyegetések észlelését](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) a [speciális adatbiztonsági (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) csomagból.


## <a name="cost-saving-use-cases"></a>Költségtakarékos használati esetek

**Hol találom a használati eseteket és a költségmegtakarítást az SQL felügyelt példányával?**

SQL felügyelt példányok esettanulmányai:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Ha jobban meg szeretné ismerni a felügyelt SQL-példányok üzembe helyezéséhez kapcsolódó előnyöket, költségeket és kockázatokat, a Forrester tanulmánya: a [mi teljes gazdasági hatása](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS-frissítés 

**Használhatok DNS-frissítést?**

Jelenleg nem biztosítunk olyan szolgáltatást, amely frissíti a DNS-kiszolgáló konfigurációját a felügyelt SQL-példányhoz.

A DNS-konfigurációt a rendszer végül frissíti:

- A DHCP-bérlet lejárta után.
- A platform verziófrissítése.

Megkerülő megoldásként az SQL felügyelt példányát 4 virtuális mag-re, majd később újra kell frissíteni. Ez a DNS-konfiguráció frissítésének mellékhatása.


## <a name="ip-address"></a>IP-cím

**Csatlakozhatok az SQL felügyelt példányához IP-cím használatával?**

Az SQL felügyelt példány IP-címmel történő csatlakoztatása nem támogatott. Az SQL felügyelt példány állomásneve az SQL felügyelt példányának virtuális fürtje előtt leképezi a terheléselosztó nevét. Egyetlen virtuális fürt több SQL felügyelt példányt is tárolhat, a kapcsolatok nem irányíthatók át a megfelelő SQL felügyelt példányra anélkül, hogy explicit módon megadta a nevet.

További információ az SQL felügyelt példányának virtuális fürtjének architektúráról: [virtuális fürt kapcsolati architektúrája](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**A felügyelt SQL-példányok statikus IP-címmel rendelkeznek?**

Ritka, de szükséges helyzetekben előfordulhat, hogy egy SQL felügyelt példány online áttelepítését kell elvégezni egy új virtuális fürtön. Ha szükséges, az áttelepítés a technológiai verem változásai miatt a szolgáltatás biztonságának és megbízhatóságának javítását célozza. Az új virtuális fürtre való Migrálás az SQL felügyelt példány állomásneve számára leképezett IP-cím módosítását eredményezi. A felügyelt SQL-példányok szolgáltatás nem igényel statikus IP-cím-támogatást, és fenntartja a jogot arra, hogy a normál karbantartási ciklusok részeként előzetes értesítés nélkül módosítsa azt.

Ezért erősen visszatartjuk az IP-cím módosíthatatlansági, mivel ez szükségtelen állásidőt eredményezhet.

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő SQL felügyelt példány időzónáját?**

Az időzóna-konfiguráció akkor állítható be, ha a felügyelt SQL-példányok első alkalommal lettek kiépítve. A meglévő SQL felügyelt példány időzónájának módosítása nem támogatott. Részletekért lásd: [időzóna korlátozásai](timezones-overview.md#limitations).

A megkerülő megoldások közé tartozik az új SQL felügyelt példány létrehozása a megfelelő időzónával, majd a manuális biztonsági mentés és visszaállítás végrehajtása, vagy az általunk javasolt művelet, amely egy [példányok közötti időpontra történő visszaállítást](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)hajt végre.


## <a name="resolve-performance-issues"></a>Teljesítménnyel kapcsolatos problémák megoldása

**Hogyan az SQL felügyelt példánnyal kapcsolatos teljesítményproblémák megoldását?**

A felügyelt SQL-példány és a SQL Server közötti teljesítmény-összehasonlításhoz jó kiindulási pont [ajánlott eljárások az Azure SQL felügyelt példánya és a SQL Server cikk teljesítményének összehasonlításához](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) .

Az adatok betöltése gyakran lassabb a felügyelt SQL-példányon, mint SQL Server a kötelező teljes helyreállítási modell és a tranzakciós napló írási sebességének [korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) miatt. Néha ez a megoldás az átmeneti adatok tempdb történő betöltése a felhasználói adatbázis helyett, fürtözött oszlopcentrikus vagy memóriára optimalizált táblák használatával.


## <a name="restore-encrypted-backup"></a>Titkosított biztonsági mentés visszaállítása

**Visszaállíthatom a titkosított adatbázist a felügyelt SQL-példányra?**

Igen, nem kell visszafejtenie az adatbázist az SQL felügyelt példányra való visszaállításhoz. Meg kell adnia egy tanúsítványt/kulcsot, amelyet titkosítási kulcsként kell használni a forrásrendszer SQL felügyelt példánya számára, hogy el tudja olvasni az adatait a titkosított biztonságimásolat-fájlból. Két lehetséges módszer:

- *A tanúsítvány feltöltése a felügyelt SQL-példányra*. Ezt csak a PowerShell használatával lehet elvégezni. A [minta parancsfájl](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) a teljes folyamatot ismerteti.
- *Töltse fel az aszimmetrikus kulcs-védőt a Azure Key Vault (AKV) és a Point SQL felügyelt példányát*. Ez a megközelítés hasonlít a saját kulcsú (BYOK) TDE használati esetére, amely a AKV-integrációt is használja a titkosítási kulcs tárolásához. Ha nem szeretné a kulcsot titkosítási kulcsként használni, és csak azt szeretné, hogy elérhető legyen az SQL felügyelt példánya a titkosított adatbázis (ok) visszaállításához, kövesse az BYOK- [TDE beállításához](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)szükséges utasításokat, és ne jelölje be a jelölőnégyzetet, *hogy a kijelölt kulcs legyen az alapértelmezett TDE-védő*.

Miután a titkosítási védő elérhetővé vált az SQL felügyelt példánya számára, folytathatja a szabványos adatbázis-visszaállítási eljárást.

## <a name="migrate-from-sql-database"></a>Áttelepítés SQL Databaseról 

**Hogyan telepíthetek át Azure SQL Databaseról SQL felügyelt példányra?**

A felügyelt SQL-példányok a számítási és a tárolási méretnél ugyanazok a teljesítménnyel rendelkeznek, mint Azure SQL Database. Ha egyetlen példányon szeretné összevonni az adatait, vagy egyszerűen csak az SQL felügyelt példányában támogatott szolgáltatást kell használnia, az adatait exportálási/importálási (BACPAC) funkciók használatával is áttelepítheti.

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
