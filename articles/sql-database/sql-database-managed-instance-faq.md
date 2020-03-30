---
title: Felügyelt példány – gyakori kérdések
description: SQL Database felügyelt példány a gyakori kérdések (GYIK)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364169"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database felügyelt példány a gyakori kérdések (GYIK)

Ez a cikk az SQL [Database által felügyelt példányokkal](sql-database-managed-instance.md)kapcsolatos leggyakoribb kérdéseket tartalmazza.

## <a name="supported-features"></a>Támogatott funkciók

**Hol találok listát a felügyelt példány által támogatott szolgáltatásokról?**

A felügyelt példány támogatott szolgáltatásainak listáját az [Azure SQL Database versus SQL Server című témakörben található.](sql-database-features.md)

Az Azure SQL Database által felügyelt példány és a helyszíni SQL Server szintaxisa és viselkedése közötti különbségekről a [T-SQL-különbségek az SQL Server kiszolgálóval szembeni különbségek](sql-database-managed-instance-transact-sql-information.md)című témakörben található.


## <a name="tech-spec--resource-limits"></a>Technikai specifikációk & erőforrás-korlátok
 
**Hol találhatom meg a felügyelt példány műszaki jellemzőit és erőforráskorlátait?**

A rendelkezésre álló hardvergenerálási jellemzőket lásd [a hardvergenerációk műszaki különbségei között.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)
Az elérhető szolgáltatási szintek és azok jellemzői a [szolgáltatásszintek közötti műszaki különbségeket](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)olvassa el.

## <a name="known-issues--bugs"></a>Ismert problémák & hibák

**Hol találhatok ismert problémákat és hibákat?**

A hibákat és az ismert problémákat lásd [az ismert problémákról.](sql-database-release-notes.md#known-issues)

## <a name="new-features"></a>Új funkciók

**Hol találhatom meg a legújabb funkciókat és a funkciókat a nyilvános előzetes verzióban?**

Az új és az előnézeti funkciókat a [kibocsátási megjegyzések című témakörben tekintheti](sql-database-release-notes.md?tabs=managed-instance)meg.

## <a name="deployment-times"></a>Telepítési idők 

**Mennyi időt vesz igénybe a példány létrehozása vagy frissítése, illetve az adatbázis visszaállítása?**

Az új felügyelt példány létrehozásának vagy a szolgáltatási szint (virtuális magok, tárolás) módosításának várható ideje több tényezőtől függ. Tekintse meg a [menedzsment műveleteket](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Elnevezési konvenció

**Egy felügyelt példány neve megegyezhet a helyszíni SQL Server névvel?**

A felügyelt példány nevének módosítása nem támogatott.

A felügyelt példány alapértelmezett DNS-zónája *(database.windows.net database.windows.net)* módosítható. 

Ha az alapértelmezett helyett másik DNS-zónát szeretne használni, például *.contoso.com*: 
- Alias definiálása a CliConfig segítségével. A szerszám van csak egy iktató hivatal elintézés burkoló, tehát ez tudna lenni megtett használ csoport politika vagy kézírás ugyancsak.
- Használja a *CNAME* with *TrustServerCertificate=true* kapcsolót.

## <a name="move-db-from-mi"></a>Adatbázis áthelyezése a mi-ből 

**Hogyan helyezhetem át az adatbázist a felügyelt példányból az SQL Server vagy az Azure SQL Database adatbázisába?**

Az [adatbázist exportálhatja a BACPAC programba,](sql-database-export.md) majd [importálhatja a BACPAC-fájlt.]( sql-database-import.md) Ez ajánlott, ha az adatbázis kisebb, mint 100 GB.

Tranzakciós replikáció akkor használható, ha az adatbázis összes táblája elsődleges kulcsokkal rendelkezik.

A `COPY_ONLY` felügyelt példányból vett natív biztonsági mentések nem állíthatók vissza az SQL Server kiszolgálóra, mert a felügyelt példány adatbázisverziója az SQL Serverkiszolgálóhoz képest magasabb.

## <a name="migrate-instance-db"></a>Adatbázis példány áttelepítése

**Hogyan telepíthetem át a példányadatbázist egyetlen Azure SQL-adatbázisba?**

Az egyik lehetőség az, hogy [exportálja az adatbázist egy BACPAC-ba,](sql-database-export.md) majd [importálja a BACPAC fájlt](sql-database-import.md). 

Ez az ajánlott megközelítés, ha az adatbázis kisebb, mint 100 GB. Tranzakciós replikáció akkor használható, ha az adatbázis összes táblája elsődleges kulcsokkal rendelkezik.

## <a name="switch-hardware-generation"></a>Váltás hardvergenerálása között 

**Válthatok a felügyelt példány hardvergenerálása között a Gen 4 és a Gen 5 között online?**

A hardvergenerációk közötti automatikus online váltás akkor lehetséges, ha mindkét hardvergeneráció elérhető abban a régióban, ahol a felügyelt példány ki van építve. Ebben az esetben ellenőrizheti [a virtuálismag-modell áttekintése lapot,](sql-database-service-tiers-vcore.md) amely bemutatja, hogyan válthat a hardvergenerációk között.

Ez egy hosszú ideig futó művelet, mivel egy új felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan átadhatók a régi és az új példány között egy gyors feladatátvétel a folyamat végén. 

Ha mindkét hardvergeneráció nem támogatott ugyanabban a régióban, a hardvergenerálás módosítása lehetséges, de manuálisan kell elvégezni. Ehhez létre kell állítania egy új példányt abban a régióban, ahol a kívánt hardvergenerálás elérhető, és manuálisan biztonsági másolatot kell létrehoznia és vissza kell állítania az adatokat a régi és az új példány között.


## <a name="tune-performance"></a>Teljesítmény hangolása

**Hogyan hangolhatom a felügyelt példány teljesítményét?**

Az Általános célú felügyelt példány távtárolót használ, amely nek köszönhetően az adatok és a naplófájlok mérete a teljesítmény szempontjából fontos. További információ: [A naplófájl méretének hatása az általános célú felügyelt példány teljesítményére](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)című témakörben talál.

Ha a munkaterhelés sok kis tranzakcióból áll, fontolja meg a kapcsolat típusát proxyról átirányítási módra való váltásra.

## <a name="maximum-storage-size"></a>Maximális tárolási méret

**Mi a felügyelt példány maximális tárolási mérete?**

A felügyelt példány tárolási mérete a kiválasztott szolgáltatási szinttől függ (általános cél vagy üzleti szempontból kritikus). A szolgáltatásszintek tárolási korlátairól a [Service Tier jellemző ben.](sql-database-service-tiers-general-purpose-business-critical.md)

## <a name="back-up-storage-cost"></a>Biztonsági másolatot a tárolási költségekről 

**A biztonsági mentési tároló le van vonva a felügyelt példánytárolóból?**

Nem, a biztonsági mentési tároló nem kerül levonásra a felügyelt példány tárhelyéből. A biztonsági mentési tároló független a példány tárhelyétől, és nem korlátozott méretű. A biztonsági mentési tárolót a példányadatbázisok biztonsági másolatának megőrzése 7 és 35 nap között korlátozott. További információt az [Automatikus biztonsági mentések című témakörben](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)talál.

## <a name="track-billing"></a>Számlázás nyomon követése

**Van mód a felügyelt példány számlázási költségeinek nyomon követésére?**

Ezt az Azure [Cost Management megoldással](/azure/cost-management/)teheti meg. Nyissa meg az **Előfizetések** elemet az [Azure Portalon,](https://portal.azure.com) és válassza a **Költségelemzés lehetőséget.** 

Használja a **Halmozott költségek lehetőséget,** majd szűrje az **Erőforrás típusa szerint** a . `microsoft.sql/managedinstances` 
  
## <a name="inbound-nsg-rules"></a>Bejövő NSG-szabályok

**Hogyan állíthatok be bejövő NSG-szabályokat a felügyeleti portokra?**

A felügyelt példányvezérlő sík nsg-szabályokat tart fenn, amelyek védik a felügyeleti portokat.

Itt van mi vezetés kikötő van használt részére:

A 9000-es és 9003-as portokat a Service Fabric infrastruktúrája használja. A Service Fabric elsődleges szerepköre a virtuális fürt kifogástalan állapotának megőrzése és a célállapot megtartása az összetevő-replikák száma tekintetében.

Az 1438-as, 1440-es és 1452-es portokat a csomópontügynök használja. A csomópontügynök egy olyan alkalmazás, amely a fürtön belül fut, és amelyet a vezérlősík felügyeleti parancsok végrehajtására használ.

Az NSG-szabályok mellett a beépített tűzfal védi a példányt a hálózati rétegen. Az alkalmazásrétegen a kommunikáció a tanúsítványokkal védett.
  
További információt és a beépített tűzfal ellenőrzéséről az [Azure SQL Database által felügyelt példány beépített tűzfalcímű témakörében](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)talál.


## <a name="mitigate-data-exfiltration-risks"></a>Az adatok kiszivárgási kockázatainak csökkentése  

**Hogyan csökkenthetem az adatok kiszivárgási kockázatait?**

Az adatok kiszivárgásával kapcsolatos kockázatok csökkentése érdekében az ügyfeleknek ajánlott biztonsági beállításokat és vezérlőket alkalmazniuk:

- Kapcsolja be az [átlátszó adattitkosítást (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) az összes adatbázison.
- A közös nyelvi futásidő (CLR) kikapcsolása. Ez ajánlott a helyszínen is.
- Csak az Azure Active Directory (AAD) hitelesítést használja.
- Hozzáférés-példány alacsony jogosultsági szintű DBA-fiókkal.
- Állítsa be a JiT jumpbox-hozzáférést a rendszergazdai fiókhoz.
- Kapcsolja be az [SQL naplózást](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine), és integrálja a riasztási mechanizmusokkal.
- Kapcsolja be a [fenyegetésészlelést](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) a [Speciális adatbiztonság (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) csomagból.


## <a name="cost-saving-use-cases"></a>Költségmegtakarítási használati esetek

**Hol találhatok használati eseteket és az ebből eredő költségmegtakarítást a felügyelt példánysegítségével?**

Irányított esettanulmányok:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS (PowerDETAILS)](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Az Azure SQL Database felügyelt példányának telepítésével kapcsolatos előnyök, költségek és kockázatok jobb megértése érdekében van egy Forrester-tanulmány is: [Az MI teljes gazdasági hatása.](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)


## <a name="dns-refresh"></a>DNS-frissítés 

**Csinálhatok DNS-frissítést?**

Jelenleg nem biztosítunk olyan szolgáltatást, amely a felügyelt példány DNS-kiszolgálójának konfigurációját frissíti.

A DNS-konfiguráció végül frissül:

- Amikor a DHCP-címbérlet lejár.
- Platformfrissítés.

Kerülő megoldásként a felügyelt példányt 4 virtuális magra minősítse vissza, majd ezután frissítse újra. Ennek a DNS-konfiguráció frissítésének mellékhatása.


## <a name="ip-address"></a>IP-cím

**Csatlakozhatok a felügyelt példányhoz IP-címmel?**

A felügyelt példányhoz IP-címmel való csatlakozás nem támogatott. Felügyelt példány állomásnév leképezések terheléselosztó előtt felügyelt példány virtuális fürt. Mivel egy virtuális fürt több felügyelt példányt is üzemeltethet, a kapcsolatot nem lehet a megfelelő felügyelt példányhoz irányítani a nevének megadása nélkül.

A felügyelt példány virtuálisfürt-architektúráról a [Virtuálisfürt-kapcsolat architektúrája című témakörben](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)talál további információt.

**Egy felügyelt példány rendelkezhet statikus IP-címmel?**

Ritka, de szükséges helyzetekben előfordulhat, hogy egy felügyelt példány online áttelepítését kell végeznünk egy új virtuális fürtre. Szükség esetén ez az áttelepítés a szolgáltatás biztonságának és megbízhatóságának javítását célzó technológiai verem változásai miatt van. Az új virtuális fürtre való migrálás eredményeképpen módosítja a felügyelt példány állomásnevére leképezett IP-címet. A felügyelt példányszolgáltatás nem igényel statikus IP-címtámogatást, és fenntartja magának a jogot, hogy értesítés nélkül módosítsa azt a rendszeres karbantartási ciklusok részeként.

Emiatt erősen ellenezzük az IP-cím megváltoztathatatlanságára való támaszkodáslehetőségét, mivel szükségtelen állásidőt okozhat.

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő felügyelt példány időzónáját?**

Időzóna-konfiguráció állítható be, ha egy felügyelt példány van kiépítve az első alkalommal. A meglévő felügyelt példány időzónájának módosítása nem támogatott. További információt az [időzóna-korlátozások ban](sql-database-managed-instance-timezone.md#limitations)talál.

A megoldások közé tartozik egy új felügyelt példány létrehozása a megfelelő időzónával, majd manuális biztonsági mentés és visszaállítás, vagy azt javasoljuk, hogy [végezzen több példányban adott időpontban visszaállítást.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)


## <a name="resolve-performance-issues"></a>Teljesítménnyel kapcsolatos problémák megoldása

**Hogyan oldhatom meg a felügyelt példány teljesítményproblémáimat?**

A felügyelt példány és az SQL Server teljesítmény-összehasonlítása, egy jó kiindulási pont az Azure SQL felügyelt példány és az SQL Server cikk [teljesítmény-összehasonlításának ajánlott gyakorlati tanácsok.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

Az adatok betöltése gyakran lassabb a felügyelt példány, mint az SQL Server miatt kötelező teljes helyreállítási modell és [korlátozza](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) a tranzakciós napló írási átviteli átadó. Néha ez lehet megkerülkörül betöltésével tranziens adatok tempdb helyett a felhasználói adatbázis, vagy a fürtözött oszlopcentrikus, vagy a memória-optimalizált táblák.


## <a name="restore-encrypted-backup"></a>Titkosított biztonsági másolat visszaállítása

**Visszaállíthatom a titkosított adatbázisomat a felügyelt példányra?**

Igen, nem kell visszafejtenie az adatbázist ahhoz, hogy vissza tudja állítani a felügyelt példányt. A titkosított biztonságimásolat-fájlból történő adatok olvasásához meg kell adnia a forrásrendszerben titkosítási kulcsvédőként használt tanúsítványt/kulcsot. Ezt kétféleképpen lehet megtenni:

- *Tanúsítványvédő feltöltése a felügyelt példányba.* Ezt csak a PowerShell használatával teheti meg. A [mintaparancsfájl](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) a teljes folyamatot ismerteti.
- *Töltsön fel aszimmetrikus kulcsvédőt az Azure Key Vaultba (AKV), és mutasson hozzá felügyelt példányt.* Ez a megközelítés hasonlít bring-your-own-key (BYOK) TDE használati eset, amely szintén a KV-integráció thasználja a titkosítási kulcsot. Ha nem szeretné a kulcsot titkosítási kulcsvédőként használni, és csak elérhetővé szeretné tenni a kulcsot a felügyelt példány számára a titkosított adatbázis(ok) visszaállításához, kövesse a [BYOK TDE beállítására](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)vonatkozó utasításokat, és ne jelölje be a jelölőnégyzetet: *Legyen a kijelölt kulcs az alapértelmezett TDE-védő.*

Miután elérhetővé teszi a titkosítási védelmet a felügyelt példány számára, folytathatja a szabványos adatbázis-visszaállítási eljárást.

## <a name="migrate-from-single-db"></a>Áttelepítés egyetlen adatbázisból 

**Hogyan lehet áttelepíteni az Azure SQL Database egy- vagy rugalmas készlet felügyelt példány?**

A felügyelt példány ugyanazokat a teljesítményszinteket kínálja számítási és tárolási méretenként, mint az Azure SQL Database többi telepítési lehetősége. Ha egyetlen példányban szeretné összesíteni az adatokat, vagy egyszerűen csak egy kizárólag felügyelt példányban támogatott szolgáltatásra van szüksége, áttelepítheti az adatokat az exportálási/importálási (BACPAC) funkció használatával.
