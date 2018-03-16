---
title: "Az Azure SQL Database – gyakori kérdések |} Microsoft Docs"
description: "Gyakori kérdések az ügyfelek adott válaszok a felhőben szolgáltatott felhő adatbázisok és az Azure SQL Database, a Microsoft relációs adatbázis-kezelő rendszerének (RDBMS) és adatbázis kérje meg."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 59058b3445fdaf75df331d771afd28599491c52e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-faq"></a>SQL Database GYIK

## <a name="what-is-the-current-version-of-sql-database"></a>Mi az az SQL-adatbázis jelenlegi verziója?
Az SQL-adatbázis aktuális verziója 12-es verzió. Verzió V11 eltávolították.

## <a name="what-is-the-sla-for-sql-database"></a>Mi az az SLA-t az SQL Database?
Garantáljuk ügyfeleinknek, hogy különálló vagy elasztikus alap-, standard vagy prémium szintű Microsoft Azure SQL Database szolgáltatásuk az idő legalább 99,99%-ában kapcsolódni fog internetes átjárónkhoz. További információkért lásd: [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hogyan a kiszolgáló rendszergazdai jelszavának visszaállítása?
Az a [Azure-portálon](https://portal.azure.com), kattintson a **SQL Server-kiszolgálók**, válassza ki a kiszolgálót a listából, és kattintson a **jelszó alaphelyzetbe állítása**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hogyan kezelhető adatbázisok és bejelentkezések?
Lásd: [adatbázisok és bejelentkezések kezelése](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hogyan tehetem meg arról, hogy csak engedélyezett IP-címek használata engedélyezett a kiszolgálóhoz való hozzáférést?
Lásd: [hogyan: SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>SQL-adatbázis használatát, hogy nem jelennek meg a számlán meg?
SQL-adatbázis váltók a egy előre jelezhető óránkénti arány alapján mindkét a szolgáltatási rétegben + önálló adatbázisok és rugalmas készletenként felhasználható edtu-k teljesítményszint szükséges. Tényleges használathoz számított és egyenletesen szerint óránként, a számlán törtek az óra lehet, hogy megjelenítése. Például ha egy adatbázist egy hónapban a 12 óra, a számlán 0,5 nap-használatát mutatja. Emellett szolgáltatásszintek + teljesítményszintet és készletenként felhasználható edtu-k bontottuk ki, az a számlázási egyszerűbb legyen, az adott hónapban az egyes használt adatbázis-elszámolási napok száma.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Mi történik, ha egy önálló adatbázis aktív kisebb, mint egy óráig vagy magasabb szolgáltatásréteg kisebb, mint egy óráig használ?
Minden órában a legmagasabb szolgáltatásszint létezik adatbázis + használati vagy hogy az adatbázis aktív kisebb, mint egy óráig függetlenül, hogy órán belül alkalmazott teljesítményszintet kell fizetni. Például ha egy önálló adatbázis létrehozása, és törölje azt 5 percen belül a számlázási egy adatbázis óra díjat tükrözi. 

Példák:

* Ha egy alapszintű adatbázis létrehozása, majd azonnal frissítsen, Standard szintű, S1 van szó, a standard szintű, S1 díj az első egy óra.
* Ha rendszerről egy adatbázis Basic a Premium 10:00 órakor és reggel 1:35 frissítés befejezése a következő napon van szó, az 1:00 órakor kezdődő prémium díj 
* Ha Ön egy adatbázist a prémium alapvető visszaminősítését 11:00 órakor 2:15 órakor befejeződik, majd az adatbázis fel van töltve a prémium szintű díj 3:00 órakor, amíg elteltével az alapvető ütemben számítjuk fel.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Hogyan nem rugalmas készlet használati jelennek meg a számlázási és mi történik, amikor módosíthatók készletenként felhasználható edtu-k?
A rugalmas készlet díja megjelenítése be a számlázási, rugalmas dtu-i (edtu-k) a készletenként felhasználható edtu-k mezőben látható lépésekben [az árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/sql-database/). Nincs a rugalmas adatbázis-díjmentes. A készlet létezik az a legnagyobb edtu-ra, függetlenül a használati vagy a készlet volt-e aktív kisebb, mint egy óráig óránként kell fizetni. 

Példák:

* Ha egy Standard rugalmas készletet hoz létre a 200 edtu-k szerint 18, öt adatbázisok hozzáadása a készlethez van szó, a 200 edtu-k az egész órát 11 órakor kezdődő a nap további része.
* Naponta 2, reggel 5:05 adatbázis 1 50 edtu-k fel kezdődik, és állandó napon keresztül tárolja. Adatbázisok 2 – 5 ingadozik 0 és 80 edtu-k között. A nap folyamán öt más adatbázisokhoz, napjainkat különböző edtu-k használó hozzáadása. 2 nap terhelve, 200 eDTU teljes napi. 
* A nap 3: 00 órai egy másik 15 adatbázisok hozzáadása. A pont, ha úgy dönt, hogy növelje a 400-as 200 a készlet edtu-k 8:05-kor, napjainkat növeli az adatbázis-használat A 200 eDTU szinten díjak érvényben levő csak 8 óra és a fennmaradó 4 óra 400 edtu-k száma növekszik. 

## <a name="elastic-pool-billing-and-pricing-information"></a>A rugalmas készlet számlázási és díjszabási információkat
Rugalmas készletek óraalapú a következő jellemzőkkel:

* Rugalmas készlethez való létrehozás után lesz számlázva, akkor is, ha nincsenek a készletben lévő adatbázisok.
* Egy rugalmas készlet számlázása óránként történik. Ez a teljesítményszintet önálló adatbázisok esetében ugyanaz mérési gyakoriságát.
* Ha egy rugalmas készlet edtu-k új számú átméretezték, majd a készlet van nem számlázzuk a edtu-k új mennyisége az átméretezési művelet befejezéséig. Ez az önálló adatbázisok teljesítményszintjének változó állapotúként azonos mintát követi.
* A rugalmas készletek ára a készlet edtu-inak száma alapul. A rugalmas készletek ára független a számát és a hozzá tartozó rugalmas adatbázisokban felhasználása.
* Árlista (a készlet edtu-inak száma) alapján számított x (eDTU / egységár).

Rugalmas készlethez tartozó eDTU Egységár értéke magasabb, mint egy önálló adatbázis ugyanazon a szolgáltatási rétegben DTU egységárát. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 

Az edtu-inak száma és a szolgáltatási szintek ismertetése: [SQL Database beállításai és teljesítménye](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hogyan működik az aktív georeplikáció a rugalmas készlet megjelennek a számlán meg a használatát?
Önálló adatbázisok használatával ellentétben [aktív georeplikáció](sql-database-geo-replication-overview.md) rugalmas adatbázisok nem közvetlen hatást számlázási.  Csak az edtu-inak kiépítve a készletek (alkalmazáskészlet elsődleges és másodlagos készlet) van szó

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Milyen hatással van a számlára a naplózási funkció használata?
A SQL Database szolgáltatásba: nincs további naplózás beépített költségeket, és készen áll a Basic, Standard és Premium adatbázisok. Azonban a naplók tárolásához, a naplózási szolgáltatás által használt, egy Azure Storage-fiókot, és a táblák és az Azure Storage üzenetsorokat díjakat alapján a napló méretét.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Hogyan találhatom a jobb szolgáltatási és teljesítményszintet szint önálló adatbázisok és rugalmas készletek?
Nincsenek elérhető néhány eszközök: 

* A helyszíni adatbázisokhoz, a [DTU méretezési advisor](http://dtucalculator.azurewebsites.net/) javasoljuk az adatbázisok és a szükséges dtu-inak száma és a rugalmas adatbáziskészletek értékeléséhez.
* Ha egy önálló adatbázis volna rendelkezésre áll a készletbe, Azure intelligens motor rugalmas készletek javasolja, ha azt látja, hogy egy korábbi használati mintát, amely szükségessé teszi. Lásd: [figyelése és kezelése az Azure-portálon a rugalmas készletekben](sql-database-elastic-pool-manage-portal.md). Számításokat végezni a saját kezűleg kapcsolatos részletekért lásd: [rugalmas készletek ára és teljesítménye szempontjai](sql-database-elastic-pool.md)
* Kell-e tárcsázni egy önálló adatbázis felfelé vagy lefelé, olvassa el [az önálló adatbázisok teljesítményének útmutatást](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Milyen gyakran módosíthatom a szolgáltatási szint vagy a teljesítmény szint egyetlen adatbázisra?
Módosíthatja a szolgáltatási réteg (között a Basic, Standard és Premium) vagy a teljesítményszintjének belül egy szolgáltatási réteg (például s2 S1) gyakran, ahányat csak szeretne. Korábbi adatbázisokhoz módosíthatja a szolgáltatási szint vagy a teljesítmény szint összesen négy alkalommal egy 24 órás időszakban.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Milyen gyakran módosíthatja az edtu-k készletenként?
Olyan gyakran ahányat csak szeretne.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Mennyi időt vesz igénybe a szolgáltatási szint vagy a teljesítmény szint egyetlen adatbázisra módosíthatja vagy adatbázis áthelyezése rugalmas készletek mindkét?
Egy adatbázis a szolgáltatási szint módosítása, és mindkét készlet áthelyezése van szükség az adatbázis másolása háttér műveletként a platformon. A szolgáltatási réteg eltarthat néhány percig adatbázisok méretétől függően több órába. Mindkét esetben az adatbázisokat továbbra is online és elérhető az áthelyezés során. Önálló adatbázisok módosításával kapcsolatos részletekért lásd: [módosíthatja a szolgáltatásszintet adatbázis](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Mikor kell használni egy önálló adatbázis és a rugalmas adatbázisok?
Általában rugalmas készletek készültek, a szokásos [szoftver,--szolgáltatás (SaaS) alkalmazásminta](sql-database-design-patterns-multi-tenancy-saas-applications.md), ahol van egy adatbázist használ az ügyfél vagy a bérlőt. Ebben a modellben önálló adatbázisok vásárlásával és a rendszer ebből adódó, túlzott mértékű kiépítésével tudnak megfelelni az adott adatbázisra vonatkozó változó, illetve a csúcsidőszakokban jellemző igényeknek. -Készletek kezelheti a készlet közös teljesítményéért, és az adatbázisok automatikus méretezése felfelé és lefelé. Azure intelligens motor adatbázisok készlet azt javasolja, amikor a használati mód szükségessé teszi. További információkért lásd: [rugalmas készlet útmutatást](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Mit jelent az, hogy a biztonsági tárhely a teljes adatbázistárhely legfeljebb 200%-áig vehető igénybe többletköltség nélkül?
Biztonságimásolat-tároláshoz pedig a tárhely, az automatikus adatbázis biztonsági mentése, a használt társított [pontot-a--visszaállítás egy korábbi időpontra](sql-database-recovery-using-backups.md#point-in-time-restore) és [georedundáns helyreállítás](sql-database-recovery-using-backups.md#geo-restore). A Microsoft Azure SQL Database által biztosított biztonsági tárhelyet a teljes adatbázistárhely legfeljebb 200%-áig lehet igénybe venni többletköltség nélkül. Például ha egy szabványos DB példány kiosztott DB méretű 250 GB-os, rendelkezésre álló 500 GB-os biztonsági mentési tároló használatáért nem kell külön fizetni. Ha az adatbázis meghaladja a megadott biztonsági mentési tárolót, ha szeretné csökkenteni a megőrzési időszakot lépjen kapcsolatba az Azure támogatási szolgálatának vagy standard írásvédett földrajzilag redundáns tárolás (RA-GRS) díj számlázva extra biztonságimásolat-tároláshoz kell fizetnie. RA-GRS számlázási további információkért lásd: Storage Díjszabásának részleteit.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>I vagyok áthelyezése Web vagy Business az új szolgáltatási szinteket, mit kell tudnia?
Az Azure SQL Web és Business adatbázisokat most kivezettük. A Basic, Standard, és a Premium és rugalmas rétegek cserélje le a leköszönő Web és Business adatbázisokat. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Mi az, hogy egy várt replikációs késés földrajzi replikálása esetén egy adatbázis belül az azonos Azure geográfiai két régiók között?
Azt jelenleg támogatják az RPO öt másodpercenként, és a replikációs késés kisebb, hogy amikor a földrajzi másodlagos az Azure-ban üzemeltetett-e ajánlott párosított régió és ugyanazt a szolgáltatási rétegben.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Egy várt replikációs késés Mi az a földrajzi-másodlagos ugyanabban a régióban, mint az elsődleges adatbázis létrehozásakor?
Tapasztalati adatok alapján, nincs túl sok intra-régiónként, illetve a régió közötti replikációs késés különbségének során az Azure ajánlott párosított régió szolgál. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Ha két régiók közötti hálózati hiba, nem az újrapróbálkozási logika működése georeplikáció beállításakor?
Ha a kapcsolata megszakad, 10 másodpercenként próbálkozni kapcsolatok újbóli létrehozásához.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Mit tehetek garantálja, hogy a rendszer replikálja az elsődleges adatbázis kritikus változás?
A földrajzi másodlagos aszinkron replikájának és azt nem az elsődleges teljes szinkronban tartsa. De nyújtunk szinkronizálásának annak biztosítása érdekében az (például jelszó frissítések) kritikus módosítások replikációjának kényszerítése metódus. Kényszerített szinkronizálási teljesítmény hatással van, mert a hívó szál blokkol, amíg a végrehajtott tranzakciók replikálódnak. További információkért lásd: [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Milyen eszközök érhetők el a replikációs késés az elsődleges adatbázis és a másodlagos földrajzi figyelése?
A valós idejű replikációs késés az elsődleges adatbázis és a földrajzi-másodlagos egy DMV keresztül elérhetővé kell tenni. További információkért lásd: [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Adatbázis áthelyezése egy másik kiszolgálóra ugyanabban az előfizetésben
Az a [Azure-portálon](https://portal.azure.com), kattintson a **SQL-adatbázisok**, válasszon ki egy adatbázist a listából, és kattintson a **másolási**. Lásd: [Azure SQL-adatbázis másolása](sql-database-copy.md) további részletek.

## <a name="to-move-a-database-between-subscriptions"></a>Az előfizetések közötti adatbázis áthelyezése
Az a [Azure-portálon](https://portal.azure.com), kattintson a **SQL Server-kiszolgálók** , és válassza ki a kiszolgálót, amelyen az adatbázist a listán. Kattintson a **áthelyezése**, majd válassza ki az áthelyezni kívánt erőforrásokat és áthelyezése az előfizetés és.
