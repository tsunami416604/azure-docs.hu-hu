---
title: Az SQL Database felügyelt példány – gyakori kérdések |} A Microsoft Docs
description: Az SQL Database felügyelt példány – gyakori kérdések (GYIK)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798066"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Az SQL Database felügyelt példány – gyakori kérdések (GYIK)

Ez a cikk tartalmazza a leggyakoribb kérdések számos kapcsolatos [SQL Database felügyelt példány](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Hol található a felügyelt példány által támogatott funkciók listáját?

A felügyelt példány támogatott funkciók listáját lásd: [Azure SQL Database és SQL Server összehasonlítása](sql-database-features.md).

Szintaxist és a viselkedés az Azure SQL Database felügyelt példány és a helyszíni SQL Server közötti különbségeket, lásd: [T-SQL eltérései az SQL Serverből](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Hol találhatok műszaki jellemzők és erőforráskorlátok felügyelt példány?

Rendelkezésre álló hardverjellemzők. generációs, lásd: [a hardvergenerációk – műszaki eltérések](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Elérhető a szolgáltatási szintek és azok tulajdonságait: [szolgáltatásszintek között – műszaki eltérések](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Hol találhatóak az ismert problémák és hibák?

Hibák és ismert problémák: [viselkedésbeli változásokat](sql-database-managed-instance-transact-sql-information.md#Changes) és [ismert problémák](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Felügyelt példány lehet a neve megegyezik a helyszíni SQL Server?

Felügyelt példány végződő névvel kell rendelkeznie *database.windows.net*. Az alapértelmezett, például helyett egy másik DNS-zóna használandó **mi egy másik nevet**. contoso.com: 
- Alias meghatározására CliConfig használni (az eszköz csak egy beállításjegyzékbeli beállítások burkoló, így ez is elvégezhetők a csoportházirend vagy parancsfájl).
- Használat *CNAME* a *TrustServerCertificate = true* lehetőséget.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Hogyan helyezhetem át adatbázis felügyelt példány vissza az SQL Server-vagy Azure SQL Database?

Is [adatbázis exportálása bacpac](sql-database-export.md) , majd [a bacpac-fájl importálása]( sql-database-import.md). Ez a megközelítés akkor ajánlott, ha az adatbázis nem 100 GB-nál kisebb.

Tranzakciós replikáció is használható, ha az adatbázis összes táblák elsődleges kulcsot tartalmaznak.

Natív `COPY_ONLY` biztonsági másolatokat a felügyelt példány nem állítható vissza a SQL Server, mert a felügyelt példány az SQL Server képest magasabb adatbázis-verzió.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Hogyan telepíthet át egy Azure SQL Database a példány adatbázisom?

Az egyik lehetőség, hogy [az adatbázis exportálása bacpac](sql-database-export.md) , majd [a bacpac-fájl importálása]( sql-database-import.md). 

Ez az az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb. Tranzakciós replikáció is használható, ha az adatbázis összes táblák elsődleges kulcsot tartalmaznak.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Hogyan válassza a 4. generációs és a Gen 5 hardver generációja felügyelt példány között?

A számítási feladatok, néhány hardver generációja jobb bizonyos típusú számítási feladatokat, mint a többi Ez függ. Meglehetősen összetett egy egyszerűsítése érdekében a teljesítmény tárgya pedig a következő különbségek az hatással lenne a számítási feladatok teljesítményére hardvergenerációk között:
- A gen 4 biztosít számítási egy jobb támogatása érdekében, mert a fizikai processzorok és a Gen 5 virtuális mag processzorok alapján alapul. Számításigényes feladatokra előnyösebb lehet.
- A gen 5 támogatja a gyorsított hálózati eredményez a jobb i/o-sávszélességet távoli tárolóra. I/o-igényű számítási feladatokhoz az általános célú szolgáltatáscsomagban előnyösebb lehet. A gen 5 használ a gyorsabb SSD helyi lemezek 4. generációs képest. I/o-igényű számítási feladatokhoz a kritikus fontosságú szolgáltatásszintek üzleti előnyösebb lehet.

Ügyfelek javasolja, hogy a teljesítmény teszteléséhez az üzem előtti ellenőrizzen szánt tényleges számítási feladatok milyen hardver meghatározásához. generációs fog működni jobban abban az esetben.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Válthatok-e a felügyelt példány hardver generációja 4. generációs és a Gen 5 között online? 

Automatikus online közötti váltáskor hardvergenerációk akkor lehetséges, ha mindkét hardvergenerációk érhetők el ugyanabban a régióban, ahol a felügyelt példány üzembe van helyezve. Ebben az esetben lehetőség van az árképzési szint szakaszában hardvergenerációk közötti váltás az Azure Portalon.

Ez egy hosszú ideig a művelet futtatása, mert az új felügyelt példány lesznek üzembe helyezve a háttérrendszer és az adatbázisok automatikusan továbbítja a régi és új példányok között. Ez a folyamat ügyfelek számára zökkenőmentes lesz.

Mindkét hardvergenerációk ugyanabban a régióban nem támogatottak, ha a hardver generációja módosítása lehetséges, de manuálisan kell elvégezni. Ehhez a szükséges, hogy hozzon létre egy új példányt a régióban hol érhető el a kívánt hardver generációja, és manuálisan biztonsági mentése és visszaállítása a régi és az új példány közötti.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Hogyan finomhangolása a teljesítmény a felügyelt példány? 

Általános célú felügyelt példány miatt, amelyeket adat és naplófájl méretét a fontos teljesítmény távoli tárolást használ. Általános célú szolgáltatási szintű teljesítmény hangolására, kövesse az utasításokat ebben a blogbejegyzésben találhat.

I/o-igényű számítási feladatokhoz érdemes Gen 5 hardver, és 4. generációs használatával a számításigényes feladatokra. További információkért lásd a gyakori kérdésekkel foglalkozó szakaszban lévő hardvergenerációk közötti választáshoz.

Ha a számítási feladatok kis tranzakciók rengeteg áll, fontolja meg a kapcsolat típusát, a proxy átirányítási mód.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Mi az a maximális tárméret felügyelt példány? 

Felügyelt példány tárolási mérete attól függ, hogy a kiválasztott szolgáltatási szint (általános célú és az üzletileg kritikus). Lásd: storage korlátozásaival szolgáltatásszintek, [szolgáltatási szint jellemző](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>A biztonsági mentési tár a felügyelt példány tárolási vonni? 

Nem, biztonsági mentési tároló nem vonni a felügyelt példány tárhelyre. A biztonsági mentési tár a példány lemezterület független, és mérete nem korlátozott. Biztonsági mentési tár korlátozza a mennyi ideig őrizze meg a biztonsági mentés, a példányok adatbázisai, konfigurálható 7-ről 35 napon belül. További információkért lásd: [automatizált biztonsági mentések](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Hogyan állíthatok be bejövő NSG-szabályok a felügyeleti portokat?

A beépített tűzfal funkció csak a Microsoft management vagy üzembe helyező gépek és a biztonságos rendszergazdai munkaállomások használatával hatékonyan akadályozza társított IP-címtartományok a bejövő kapcsolatok engedélyezése érdekében a fürt összes virtuális gép konfigurálja a Windows tűzfal a hálózati réteg behatolások.

Itt látható, milyen portokat használják:

Service Fabric-infrastruktúrában 9000 és 9003 portot használja. A Service Fabric elsődleges szerepkör, hogy a virtuális fürt kifogástalan állapotban tartani, és tartsa célállapotát tekintetében összetevő replikák száma.

Csomóponti ügynök által használt portok 1438 1440 és 1452. Csomóponti ügynök egy alkalmazás, amely a fürtben fut, és hajtsa végre a felügyeleti parancsokat a vezérlősík használják.

A beépített tűzfal a hálózati réteg mellett kommunikációs tanúsítványok is védi.
  
További információ és a beépített tűzfal ellenőrzése: [Azure SQL Database felügyelt példány beépített tűzfal](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Hogyan lehet a hálózati kockázatok csökkentése? 

Bármely hálózati kockázatok csökkentése, az ügyfelek javasolt biztonsági beállításokat és a vezérlőket a alkalmazni:

- Kapcsolja be a transzparens adattitkosítás (TDE) az összes adatbázis.
- Kapcsolja ki a közös nyelvi futtatókörnyezet (CLR). Ez a helyszíni is ajánlott.
- Csak az Azure AD-fiókokat kell használni.
- Hozzáférés Buszpéldány SQL az alacsony jogosultsági szintű DBA-fiókhoz.
- Igény szerinti jumpbox hozzáférés rendszergazdai fiók konfigurálása.
- SQL-naplózás bekapcsolása, és integrálhatja a riasztási mechanizmusokat.
- Kapcsolja be a Fenyegetésészlelés ATS-suite-ból.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Hol találhatok használatieset-forgatókönyveit és az eredményül kapott költségmegtakarítást a felügyelt példány?

Felügyelt példány esettanulmányok:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Az előnyöket, a költségek és az Azure SQL Database felügyelt példány üzembe helyezésével járó kockázatokat jobban megértette lekéréséhez is van a Forrester tanulmány: [Total Economic Impact, MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Használhatom-e a DNS-frissítési? 
  
Jelenleg egy szolgáltatás frissítése a DNS-kiszolgáló konfigurációját a következő felügyelt példány esetében nem biztosítunk.

Végül frissül a DNS-konfiguráció:

- Ha a DHCP-címbérlet lejár.
- A platform frissítése.

Áthidaló megoldásként alacsonyabbra a felügyelt példány 4 virtuális mag, és azt később újra frissítése. A DNS-konfiguráció frissítése mellékhatása azt.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Felügyelt példány lehet statikus IP-cím?

Ritka, de a szükséges helyzetekben előfordulhat, hogy kell egy online áttelepítés egy új virtuális fürt felügyelt példány. Ha szükséges, az áttelepítés van, a biztonság és a szolgáltatás javítására irányuló technológiák változásai miatt. Áttelepítés egy új virtuális fürt eredményeket a felügyelt példány állomásneve leképezett IP-cím megváltoztatása. A felügyelt példány szolgáltatás nem statikus IP-cím támogatási jogcím, és fenntartja a jogot arra, hogy az rendszeres karbantartási ciklus részeként értesítés nélkül megváltozhatnak.

Ebből kifolyólag kifejezetten nem ajánlott, ez okozhat a felesleges IP-cím módosíthatatlansági hagyatkoznia.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Módosíthatom a meglévő felügyelt példány időzónáját?

Időzóna-beállításai állíthatók, ha egy felügyelt példány üzembe van helyezve, az első alkalommal. A meglévő felügyelt példány időzónájának megváltoztatása nem támogatott. További információkért lásd: [időzóna korlátozások](sql-database-managed-instance-timezone.md#limitations).

Megkerülő megoldások közé tartozik, hozzon létre egy új felügyelt példányt a megfelelő időzónával együtt, vagy végezze el a manuális biztonsági mentés és helyreállítás vagy mi azt javasoljuk, végrehajtásához egy [cross-példány időponthoz visszaállítási](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Hogyan oldhatom fel teljesítménybeli problémák és a felügyelt példány

Felügyelt példány és az SQL Server teljesítményének összehasonlítását, jó kiindulási pont van [ajánlott eljárások a teljesítmény összehasonlítása az Azure SQL felügyelt példány és az SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Általában lassabb, mint a felügyelt példány az SQL Server kötelező a teljes helyreállítási modell miatt adatbetöltést és [korlátok](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) a tranzakciós napló írási teljesítmény. Egyes esetekben ez macről körül felhasználói adatbázis helyett a tempdb átmeneti adatok betöltését, vagy használja a fürtözött oszlopcentrikus vagy a memóriaoptimalizált táblákhoz.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Vissza tudok állítani biztonsági titkosított-adatbázisomat a felügyelt példány?

Igen, nem kell az adatbázist visszaállíthatja azt a felügyelt példány visszafejtéséhez. Meg kell adnia a tanúsítvány és kulcs-titkosítási kulcs védelme, a forrásrendszerben a felügyelt példányhoz el tudják olvasni az adatokat a titkosított biztonsági mentési fájlból. Ehhez két lehetséges módja van:

- Töltse fel a tanúsítvány-védőelem a felügyelt példányhoz. Ehhez csak a PowerShell használatával. A minta parancsfájl a teljes folyamatát ismerteti.
- Aszimmetrikus kulcsvédő feltöltése az Azure Key Vault (AKV), és a felügyelt példány mutasson. Ez a megközelítés hasonló bring-your-saját-key (BYOK) TDE használati eset is használ az AKV-integráció a titkosítási kulcs tárolására. Ha csak a kulcs titkosított adatbázisok visszaállítása nélkül, egy titkosítási kulcs védelme a kulcsot használja a felügyelt példány számára elérhető AKV feltöltött, hajtsa végre a BYOK TDE beállításával kapcsolatos utasításokat, és ne jelölje be a jelölőnégyzetet, ellenőrizze a kiválasztott kulcs a alapértelmezett TDE-védőhöz.

Miután elvégezte a titkosítás védőelem a felügyelt példány számára elérhető, folytathatja a standard adatbázis visszaállítási eljárás.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Hogyan telepíthetek át az Azure SQL Database különálló vagy elasztikus készletből a felügyelt példány? 

A felügyelt példány ajánlatok a azonos teljesítményszintek egy számítási és tárolási mérete, az Azure SQL Database egyéb telepítési lehetőségekért. Ha szeretne összevonni az adatokat egyetlen példányán, vagy egy szolgáltatás kizárólag a felügyelt példány támogatott egyszerűen, áttelepítheti az adatokat az Exportálás/importálás (BACPAC) nevű funkciója segítségével.
