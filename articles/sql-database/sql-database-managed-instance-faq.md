---
title: Felügyelt példány – gyakori kérdések
description: SQL Database felügyelt példányok gyakran ismételt kérdései (GYIK)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b409824c2adb544560dfa17b4871ba23760d35c7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443862"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database felügyelt példányok gyakran ismételt kérdései (GYIK)

Ez a cikk a [SQL Database felügyelt példányának](sql-database-managed-instance.md)számos leggyakoribb kérdését tartalmazza.

## <a name="supported-features"></a>Támogatott funkciók

**Hol találhatók a felügyelt példányok által támogatott szolgáltatások listája?**

A felügyelt példány támogatott szolgáltatásainak listáját lásd: [Azure SQL Database versus SQL Server](sql-database-features.md).

A Azure SQL Database felügyelt példány és a helyszíni SQL Server közötti eltéréseket lásd: a [T-SQL eltérései a SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Technikai spec & erőforrás-korlátok
 
**Hol találhatók a felügyelt példányok technikai jellemzői és erőforrás-korlátai?**

Az elérhető hardver-létrehozási jellemzőkkel kapcsolatban lásd: [technikai különbségek a hardveres generációkban](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
A rendelkezésre álló szolgáltatási szintek és azok jellemzői a [szolgáltatási szintek közötti technikai különbségek](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)című részben olvashatók.

## <a name="known-issues--bugs"></a>Ismert problémák & hibák

**Hol találhatók az ismert problémák és hibák?**

Hibák és ismert problémák esetén tekintse meg az [ismert problémákat](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="new-features"></a>ÚJ funkciók

**Hol találhatók a legújabb funkciók és a szolgáltatások a nyilvános előzetes verzióban?**

Az új és az előzetes verziójú funkciókhoz lásd: [kibocsátási megjegyzések](/azure/sql-database/sql-database-release-notes?tabs=managed-instance).

## <a name="deployment-times"></a>Üzembe helyezési idők 

**Mennyi idő szükséges a példány létrehozásához vagy frissítéséhez, vagy egy adatbázis visszaállításához?**

Az új felügyelt példány létrehozásához vagy a szolgáltatási szint (virtuális mag, tárterület) módosításához szükséges idő számos tényezőtől függ. Tekintse meg a [kezelési műveleteket](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Elnevezési konvenció

**A felügyelt példányok neve megegyezik a helyszíni SQL Servervel?**

A felügyelt példánynak olyan névvel kell rendelkeznie, amely a *Database.Windows.net*-szel végződik. Ha egy másik DNS-zónát szeretne használni az alapértelmezett helyett, például a **mi – másik név**. contoso.com: 
- Alias definiálása CliConfig használatával. Az eszköz csak a beállításjegyzék-beállítások burkolója, így a csoportházirend vagy a parancsfájl használatával is elvégezhető.
- *CNAME* használata a *TrustServerCertificate = True* kapcsolóval.

## <a name="move-db-from-mi"></a>ADATBÁZIS áthelyezése a MIből 

**Hogyan helyezhetek át adatbázist a felügyelt példányról SQL Server vagy Azure SQL Databasere?**

[Exportálhatja az adatbázist a BACPAC-be](sql-database-export.md) , majd [importálhatja a BACPAC-fájlt]( sql-database-import.md). Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb.

A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

A felügyelt példányból származó natív `COPY_ONLY` biztonsági másolatok nem állíthatók vissza SQL Server, mert a felügyelt példány magasabb verziójú adatbázis-verzióval rendelkezik, mint SQL Server.

## <a name="migrate-instance-db"></a>Példány-adatbázis migrálása

**Hogyan telepíthetem át a példány-adatbázist egyetlen Azure SQL Databasere?**

Az egyik lehetőség az, hogy [exportálja az adatbázist egy BACPAC](sql-database-export.md) , majd [importálja a BACPAC-fájlt](sql-database-import.md). 

Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb. A tranzakciós replikáció akkor használható, ha az adatbázis minden táblája rendelkezik elsődleges kulccsal.

## <a name="gen-4-vs-gen-5"></a>Gen 4 vs. gen 5 

**Hogyan választani a Gen 4 és az 5. generációs hardveres generáció között a felügyelt példányhoz?**

Ez a számítási feladattól függ, mivel egyes hardver-generációk jobbak bizonyos típusú számítási feladatokhoz, mint a többi. Míg a teljesítmény tárgya eléggé összetett az egyik egyszerűsítése érdekében, a következő különbségek vannak a számítási feladatok teljesítményét befolyásoló hardveres generációk között:
- A Gen 4 jobb számítási támogatást biztosít, mivel a fizikai processzorokon alapul, a Gen 5-öt pedig a virtuális mag processzorok alapján. Előnyösebb lehet a nagy számítási igényű munkaterhelések esetében.
- A Gen 5 támogatja a gyorsított hálózatkezelést, ami jobb IO-sávszélességet eredményez a távoli tárterület számára. Előnyösebb lehet az IO-igényes számítási feladatokhoz általános célú szolgáltatási szinten. A Gen 5 gyorsabb SSD helyi lemezeket használ, mint a 4. generációs. Az üzleti szempontból kritikus fontosságú szolgáltatási szintek esetében előnyös lehet az IO-igényes számítási feladatok ellátása.

Határozottan javasoljuk, hogy tesztelje az éles környezetbe szánt tényleges munkaterhelések teljesítményét, mielőtt megkezdi a működést annak meghatározásához, hogy melyik hardver-létrehozási folyamat egy adott esetben jobban fog működni.

## <a name="switch-hardware-generation"></a>Hardver generálásának váltása 

**Átválthatom a felügyelt példányok hardveres generációját a Gen 4 és a Gen 5 online között?**

A hardveres generációk közötti automatizált online váltás akkor lehetséges, ha mindkét hardveres generáció elérhető abban a régióban, ahol a felügyelt példány kiépítve van. Ebben az esetben a [blogbejegyzésben található parancsfájl](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Change-hardware-generation-on-Managed-Instance/ba-p/699824) használatával elmagyarázza, hogyan válthat a hardveres generációk között.

Ez egy hosszan futó művelet, mivel új felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan átkerülnek a régi és az új példány között a folyamat végén található gyors feladatátvételsel. 

Ha a hardveres generációk nem támogatottak ugyanabban a régióban, akkor a hardveres generáció módosítása lehetséges, de manuálisan kell elvégezni. Ehhez olyan új példányt kell kiépíteni a régióban, ahol a kívánt hardver-létrehozás elérhető, és manuálisan végezheti el a régi és az új példány közötti biztonsági mentést és visszaállítást.


## <a name="tune-performance"></a>Teljesítmény hangolása

\* * Hogyan felügyelt példánya teljesítményének finomhangolása? **

Általános célú felügyelt példány a távoli tárterületet használja, mert az adatmennyiség és a naplófájlok teljesítménye fontos. További információ: a [naplófájl méretének hatása általános célú felügyelt példány teljesítményére](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Az i/o-igényes számítási feladatoknál érdemes lehet a Gen 5 hardvert használni, a 4. generációs számítási feladatokhoz pedig a Gen 4 használatát. További információ: Hogyan a [Gen 4 és a Gen 5 közötti választás](#gen-4-vs-gen-5).

Ha a munkaterhelés sok kis tranzakcióból áll, érdemes lehet átváltani a csatlakozás típusát a proxyról átirányítási módba.

## <a name="maximum-storage-size"></a>Maximális tárterület

**Mi a felügyelt példányok maximális tárolási mérete?**

A felügyelt példány tárolási mérete a kiválasztott szolgáltatási szinttől (általános célú vagy üzletileg kritikus) függ. A szolgáltatási szintek tárolási korlátozásait lásd: [szolgáltatási szintek jellemzője](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Tárolási díj biztonsági mentése 

**A biztonsági mentési tárterület le lett vonva a felügyelt példányok tárterületéről?**

Nem, a biztonsági mentési tár nem vonható le a felügyelt példány tárolóhelyéről. A biztonsági mentési tár a példány tárolóhelytől független, és nem korlátozódik a méretre. A biztonsági mentési tárterületet a példány-adatbázisok biztonsági mentésének időtartamára korlátozza, amely 7 – 35 napig állítható be. Részletekért lásd: [automatizált biztonsági mentések](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Számlázás követése

**Van lehetőség a felügyelt példányom számlázási díjainak nyomon követésére?**

Ezt a [Azure Cost Management megoldás](/azure/cost-management/)használatával teheti meg. Navigáljon a [Azure Portal](https://portal.azure.com) **előfizetésekhez** , és válassza a **Cost Analysis**lehetőséget. 

Használja a **halmozott költségek** lehetőséget, majd az **erőforrástípus** szerint szűrje `microsoft.sql/managedinstances`ként. 
  
## <a name="inbound-nsg-rules"></a>Bejövő NSG szabályok

**Hogyan állíthatok be bejövő NSG szabályokat a felügyeleti portokon?**

A beépített tűzfal funkció konfigurálja a Windows tűzfalat a fürtben lévő összes virtuális gépen, hogy engedélyezze a csak a Microsoft Management/Deployment Machines szolgáltatáshoz társított IP-tartományokból érkező bejövő kapcsolatokat, és hogy biztonságos felügyeleti munkaállomások hatékonyan megelőzzék a behatolást a hálózati rétegen keresztül.

A következő portokat használja a rendszer:

Service Fabric infrastruktúra a 9000-es és a 9003-es portot használja. Service Fabric elsődleges szerepkör, hogy a virtuális fürtöt Kifogástalan állapotba kell tartani, és az összetevő-replikák száma alapján meg kell őrizni a cél állapotát.

A csomópont-ügynök a 1438, 1440 és 1452 portokat használja. A csomópont-ügynök egy olyan alkalmazás, amely a fürtön belül fut, és a vezérlési sík használja a felügyeleti parancsok végrehajtásához.

A hálózati réteg beépített tűzfalán kívül a kommunikáció is védelmet biztosít a tanúsítványokkal szemben.
  
További információ és a beépített tűzfal ellenőrzése: [Azure SQL Database felügyelt példány beépített tűzfala](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-network-risks"></a>Hálózati kockázatok enyhítése  

**Hogyan csökkentheti a hálózati kockázatokat?**

A hálózati kockázatok enyhítése érdekében javasoljuk, hogy az ügyfelek biztonsági beállításokat és vezérlőket alkalmazzanak:

- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) bekapcsolása az összes adatbázison.
- A közös nyelvi futtatókörnyezet (CLR) kikapcsolása. Ez ajánlott a helyszínen is.
- Csak Azure Active Directory (HRE) hitelesítést használjon.
- Hozzáférési példány alacsony jogosultsági szintű DBA-fiókkal.
- Állítsa be a JiT Jumpbox-hozzáférést a sysadmin fiókhoz.
- Kapcsolja be az [SQL-naplózást](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine), és integrálja azt a riasztási mechanizmusokkal.
- Kapcsolja be a [fenyegetések észlelését](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) a [speciális adatbiztonsági (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) csomagból.


## <a name="cost-saving-use-cases"></a>Költségtakarékos használati esetek

**Hol találhatok használati eseteket és a megtakarítást a felügyelt példányokkal?**

Felügyelt példányok esettanulmányai:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Ha jobban meg szeretné ismerni a Azure SQL Database felügyelt példány üzembe helyezéséhez kapcsolódó előnyöket, költségeket és kockázatokat, akkor a Forrester tanulmánya is a következő: [Mi a teljes gazdasági hatás](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS-frissítés 

\* * Használhatom a DNS-frissítést? **
  
Jelenleg nem biztosítunk olyan funkciót, amely a felügyelt példány DNS-kiszolgáló konfigurációját frissíti.

A DNS-konfigurációt a rendszer végül frissíti:

- A DHCP-bérlet lejárta után.
- A platform verziófrissítése.

Megkerülő megoldásként lépjen vissza a felügyelt példányra 4 virtuális mag, és később frissítse újra. Ez a DNS-konfiguráció frissítésének mellékhatása.


## <a name="static-ip-address"></a>Statikus IP-cím

**Rendelkezhetnek felügyelt példányok statikus IP-címmel?**

Ritka, de szükséges helyzetekben előfordulhat, hogy egy felügyelt példány online áttelepítését egy új virtuális fürtre kell elvégezni. Ha szükséges, az áttelepítés a technológiai verem változásai miatt a szolgáltatás biztonságának és megbízhatóságának javítását célozza. Az új virtuális fürtre való Migrálás a felügyelt példány állomásneve számára leképezett IP-cím módosítását eredményezi. A felügyelt példány szolgáltatás nem igényel statikus IP-cím támogatását, és fenntartja a jogot arra, hogy a rendszeres karbantartási ciklusok részeként előzetes értesítés nélkül módosítsa.

Ezért erősen visszatartjuk az IP-cím módosíthatatlansági, mivel ez szükségtelen állásidőt eredményezhet.

## <a name="moving-mi"></a>A MI áthelyezése

**Át lehet helyezni egy felügyelt példányt vagy annak VNet egy másik erőforráscsoporthoz?**

Nem, ez a platform jelenlegi korlátozása. Felügyelt példány létrehozása után a felügyelt példány vagy VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő felügyelt példány időzónáját?**

Az időzóna-konfiguráció akkor állítható be, ha a felügyelt példányok első alkalommal lettek kiépítve. A meglévő felügyelt példány időzónájának módosítása nem támogatott. Részletekért lásd: [időzóna korlátozásai](sql-database-managed-instance-timezone.md#limitations).

A megkerülő megoldások közé tartozik egy új felügyelt példány létrehozása a megfelelő időzónával, majd manuális biztonsági mentés és visszaállítás, vagy a javasolt műveletek végrehajtása egy [példányos időpontra történő visszaállítással](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Teljesítménnyel kapcsolatos problémák megoldása

**Hogyan a felügyelt példánnyal kapcsolatos teljesítményproblémák megoldását?**

A felügyelt példány és a SQL Server közötti teljesítmény-összehasonlításhoz jó kiindulási pont az [Azure SQL felügyelt példánya és a SQL Server cikk teljesítményének összehasonlítására szolgáló ajánlott eljárások](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) .

Az adatok betöltése gyakran lassabb a felügyelt példányon, mint SQL Server a kötelező teljes helyreállítási modell és a tranzakciónapló írási sebességének [korlátai](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) miatt. Néha ez a megoldás az átmeneti adatok tempdb történő betöltése a felhasználói adatbázis helyett, fürtözött oszlopcentrikus vagy memóriára optimalizált táblák használatával.


## <a name="restore-encrypted-backup"></a>Titkosított biztonsági mentés visszaállítása

**Visszaállíthatom a titkosított adatbázist a felügyelt példányra?**

Igen, nem kell visszafejtenie az adatbázist, hogy vissza tudja állítani a felügyelt példányra. Meg kell adnia egy olyan tanúsítványt/kulcsot, amelyet titkosítási kulcsként használt a forrásrendszer a felügyelt példánya számára, hogy el tudja olvasni az adatait a titkosított biztonságimásolat-fájlból. Két lehetséges módszer:

- *Tanúsítvány feltöltése – oltalmazó a felügyelt példányra*. Ezt csak a PowerShell használatával lehet elvégezni. A [minta parancsfájl](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) a teljes folyamatot ismerteti.
- *Töltse fel az aszimmetrikus kulcs-védőt, hogy Azure Key Vault (AKV) és pont felügyelt példánya*legyen. Ez a megközelítés hasonlít a saját kulcsú (BYOK) TDE használati esetére, amely a AKV-integrációt is használja a titkosítási kulcs tárolásához. Ha nem szeretné a kulcsot titkosítási kulcsként használni, és csak azt szeretné, hogy a felügyelt példány számára elérhető legyen a titkosított adatbázis (ok) visszaállítása, kövesse az [BYOK-TDE beállításához](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)szükséges utasításokat, és ne jelölje be a jelölőnégyzetet, *hogy a kijelölt kulcs az alapértelmezett TDE-védő legyen*.

Miután a titkosítási védő elérhetővé vált a felügyelt példány számára, folytathatja a szabványos adatbázis-visszaállítási eljárást.

## <a name="migrate-from-single-db"></a>Migrálás egyetlen ADATBÁZISból 

**Hogyan telepíthetek át Azure SQL Database egy vagy rugalmas készletből a felügyelt példányra?**

A felügyelt példányok a számítási és a tárolási méretnél ugyanazok a teljesítményszint, mint a Azure SQL Database egyéb központi telepítési lehetőségei. Ha egyetlen példányon szeretné összevonni az adatait, vagy egyszerűen csak felügyelt példányban támogatott szolgáltatást kell használnia, az adatait Exportálás/Importálás (BACPAC) funkcióval is áttelepítheti.
