---
title: Az Azure SQL Database – gyakori kérdések |} Microsoft Docs
description: Gyakori kérdések az ügyfelek adott válaszok a felhőben szolgáltatott felhő adatbázisok és az Azure SQL Database, a Microsoft relációs adatbázis-kezelő rendszerének (RDBMS) és adatbázis kérje meg.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 5d77c2dc121d7f291fa755f66d7d9a5d7213bf9f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="sql-database-faq"></a>SQL Database GYIK

## <a name="what-is-the-current-version-of-sql-database"></a>Mi az az SQL-adatbázis jelenlegi verziója?
Az SQL-adatbázis aktuális verziója 12-es verzió. Verzió V11 eltávolították.

## <a name="what-is-the-sla-for-sql-database"></a>Mi az az SLA-t az SQL Database?
Legalább 99,99 %-ában garantáljuk, függetlenül a szolgáltatási réteg között a Microsoft Azure SQL Database és az internetes átjáró, a kapcsolat van. További információkért lásd: [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Az új vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) az Azure SQL Database Whatis?

Az új alapjául szolgáló vásárlási modell mellett a meglévő DTU-alapú modell van. A vCore-alapú modell az célja, hogy az ügyfelek rugalmasságot, átláthatóság, vezérlőelem, és egy egyszerű módon lefordítani a helyszíni munkaterhelési követelményeinek a felhőbe. Lehetővé teszi az ügyfelek számára a skála számítási és tárolási munkaterhelések igényeiknek alapján. Önálló adatbázis és a rugalmas készlet beállítások vCore modellel jogosultak is fel a 30 százalékos megtakarítások a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md) és [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md) további információt. 

## <a name="what-is-a-vcore"></a>Mi az a virtuális mag? 
Egy virtuális core jelenti. a logikai Processzor érhető el, hogy a hardver generációja közül választhat. 4 logikai processzorok alapulnak. generációból Intel E5-2673 v3 (Haswell) 2.4-GHz-es processzor és generációs 5 logikai processzorok alapul Intel E5-2673 v4 (Broadwell) 2.3-GHz-es processzor.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Mindenképpen át kell térnem a virtuálismag-alapú modellre?
Nem, a rugalmas készlet és a központi telepítési beállítások önálló adatbázis vCore-alapú modell működésbe tükrözi ügyfél rugalmasságának és iránt. Ha az ügyfelek folytatja a DTU-alapú modellel, nem kell tennie semmit, ezt a hirdetményt és a felhasználói élmény, és számlázási változatlanok maradnak. 

Sok esetben alkalmazás is kihasználhatja az egyszerűség erőforrások előre konfigurált kötegelt le. Ezért továbbra is kínálnak, és a DTU-alapú lehetőségek a ügyfelek támogatásához. Ha használja őket, és az üzleti igényeknek megfelelő, akkor továbbra is ehhez.

A DTU- és a virtuálismag-alpú modell továbbra is egymás mellett lesz elérhető. A virtuálismag-alapú modell bevezetésével azon felhasználók kéréseinek teszünk eleget, akik az adatbázis-erőforrások jobb áttekinthetőségét, illetve a számítási és a tárolási erőforrások független méretezését igénylik. A vCore-alapú modell is lehetővé teszi, hogy további megtakarítás érhető el aktív Szoftverbiztosítási keresztül az Azure hibrid juttatás rendelkező ügyfelek esetén az SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>Hogyan válasszak közötti vásárlási modell DTU-alapú vs vCore alapú alapjául szolgáló vásárlási modell (előzetes verzió)? 
A Database Transaction Unit (adatbázis-tranzakciós egység – DTU) a CPU- és memóriahasználaton, valamint az olvasási és írási műveletek számán alapuló vegyes mérték. A DTU-alapú teljesítményszintek különböző alkalmazásteljesítmény-szinteknek eleget tevő előre konfigurált erőforráscsomagoknak felelnek meg. Az ügyfelek, akik nem szeretné, hogy a mögöttes erőforrások foglalkoznia, és inkább egy előre konfigurált köteg egyszerűsége míg a rögzített méretű minden hónapban a DTU-alapú modell tapasztalhatja igényeiknek ideális. A felhasználókat a mögöttes erőforrások további betekintést kell vagy méretezést azokat egymástól függetlenül az optimális teljesítmény eléréséhez, azonban a vCore-alapú modell lesz a legjobb választás.  Továbbá, ha az ügyfél egy aktív szoftverfrissítési megbízhatósági (SA) az SQL Server, hogy kihasználja a meglévő befektetések, és legfeljebb 30 %-a Mentés [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Beállítások belül minden vásárlási modell előnyei egy teljes körűen felügyelt szolgáltatás, például az automatikus biztonsági mentés, frissítések és javítások. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Miért érdemes az Azure hibrid az SQL Server? 
A [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md) segítséget nyújt a jelenlegi licencelési beruházások kinyert érték maximalizálása érdekében, és az áttelepítés érdekében, a felhőhöz. Egy Azure-alapú juttatásra, amely lehetővé teszi, hogy az SQL Server licenccel rendelkező Software Assurance kell fizetnie a csökkentett ráta "base" SQL adatbázis Azure hibrid kihasználhatja az SQL Server rendszer. Az SQL Server Azure hibrid előnyt az jelenti a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) az SQL-adatbázis önálló adatbázisok és rugalmas készletek nyilvános előnézete érhető el. Előfordulhat, hogy az előnyök érvényesek, még akkor is, ha a Termékváltozat aktív, de vegye figyelembe az alap a óta, akkor válassza ki azt az Azure portálon alkalmaznak. Visszamenőleges jóváírásra nincs mód.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Vannak-e kettős jogosultságokkal rendelkező Azure hibrid juttatás az SQL Server?
Kettős használata jogok a licenc áttelepítések zökkenőmentesen futnak 180 nap van. A 180 napos időszak után az SQL Server licence csak akkor használható az SQL-adatbázis a felhőben, és nincs kettős jogok helyszíni és a felhőben.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Eltérések a licenchordozhatósági az SQL Server Azure hibrid juttatásra?
Napjainkban kínálunk Software Assurance licenccel rendelkező SQL Server-ügyfelek mobilitási előnyeit, amely lehetővé teszi a saját licencek külső megosztott kiszolgálók ismételt hozzárendelése. Az előnyök Azure infrastruktúra-szolgáltatási és AWS EC2 is használhatók.
SQL Server Azure hibrid juttatása eltér az licenchordozhatósági két fő területen:
- Nagymértékben virtualizált munkaterhelések áthelyezésére Azure gazdasági előnyt is biztosít. SQL EE igényelhető 4 mag nagymértékben virtualizált alkalmazások helyszíni saját az általános célú SKU a minden core az Azure-ban. Licenchordozhatósági nem enged semmilyen különleges költségelőnyökhöz juthat virtualizált munkaterhelések áthelyezését a felhőbe.
- Egy PaaS célra az SQL Server helyszíni – SQL-adatbázis felügyelt példány magas kompatibilis Azure biztosít.

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Mik azok a megfelelő jogosultságok az SQL Server Azure hibrid juttatása?
SQL-adatbázis az ügyfelek az alábbi jogosultságokkal az SQL Server Azure hibrid juttatás társított lesz:

|Licenc erőforrásigényét|Az SQL Server első Ön funkciója Azure hibrid juttatásra?|
|---|---|
|SQL Server Enterprise Edition core ügyfeleivel rendszergazdai (SA)|<li>Általános célú vagy a kritikus fontosságú üzleti SKU talál aránya is kell fizetnie.</li><br><li>helyszíni 1 core = 4 mag, az általános célú Termékváltozat</li><br><li>helyszíni 1 core = 1 core kritikus üzleti termékváltozatban</li>|
|SQL Server Standard Edition core ügyfeleivel rendszergazdai (SA)|<li>Is kell fizetnie talál arány általános célú SKU csak</li><br><li>helyszíni 1 core = 1 mag az általános célú Termékváltozat</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Az SQL-adatbázis felügyelt példány számára elérhető a vCore-alapú modell?
[Felügyelt példány](sql-database-managed-instance.md) csak a vCore-alapú modell érhető el. További információk is: a [árképzést ismertető oldalra SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>A számítási és tárolási költségek a szolgáltatási rétegben, amely a kiválasztott függ?
A számítási költsége, amely ki van építve az alkalmazás összes számítási kapacitás tükrözi. A fontos üzleti szolgáltatási rétegben hogy automatikus kiosztása a legalább 3 Alwayson-replikák. A további lefoglalása a számítási erőforrásokat tükrözéséhez vCore ára 2.7 x nagyobb a fontos üzleti. Ugyanezen okból a magasabb tárolási ára / GB a fontos üzleti szinten lévő tükrözi, a magas IO és az alacsony késleltetés az SSD-tárolás. Egy időben a biztonsági másolatok tárolásának költségét nincs másik mivel mindkét esetben egy standard tárolási osztály használjuk.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Hogyan vagyok felszámított tárolási - mi konfigurálni előzetes megfizetése esetén vagy az adatbázis használja a alapján?
Különböző típusú tárolási másképp vannak számlázva. A ritkán használt adatok tárolására van szó a kiépített tárolási az adatbázis maximális vagy választja, a készlet mérete alapján. A költség nem változik, kivéve, ha a csökkentése, vagy növelje a legnagyobb. A biztonsági mentési tár a példány automatizált biztonsági mentéseihez tartozik. A biztonsági másolatok megőrzési időszakának kiterjesztésével a példány által felhasznált biztonsági mentési tárterület is nő. A kiszolgáló kiépített tárolója 100%-ának megfelelő biztonsági mentési tár díjmentesen használható. További biztonsági másolatokat tároló fogyasztása havonta díjfizetéssel GB-ban. Ha például az adatbázistárhelye 100 GB, 100 GB biztonsági mentési tárhelyet kap díjmentesen. De ha a biztonsági másolat 110 GB-ban, kell fizetnie a további 10 GB-ban.

Egy adatbázis biztonsági másolatok tárolására van szó, az adatbázis biztonsági másolatait az adatbázis mérete csökkentve lett lefoglalva tárolóhoz van szükség. Egy rugalmas készlet biztonsági másolatok tárolására van szó, a rugalmas készlet maximális méretét csökkentve a készlet összes adatbázisát a biztonsági másolatokat lett lefoglalva tárolóhoz van szükség. Azokat az adatbázis méretének vagy a rugalmas készlet növekedése, illetve megnövelheti a tranzakciók sebességét, a további tárhely szükséges, és így növekszik a biztonsági másolatok tárolásának számlázási.  Amikor növeli a maximális méretét, ezt a mennyiséget a számlázott biztonsági másolatokat tároló mérete vonni.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Hogyan válassza meg a megfelelő SKU, ha a meglévő adatbázis konvertálása az új szolgáltatási szinteket? 
Meglévő SQL adatbázis-alkalmazások a DTU-alapú modellel az általános célú szolgáltatási réteg esetén a Standard szint összehasonlítható. A fontos üzleti szolgáltatásréteg hasonlítható összes a prémium tarifacsomagra. Mindkét esetben legalább 1 vCore kell lefoglalni az egyes 100 DTU, amely az alkalmazás által a DTU-alapú modellben.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Hajtsa végre az új vCore-alapú szolgáltatási szinteket kínálnak az összes meglévő szolgáltatási szint célkitűzései (Slo) kompatibilis teljesítményszintek?
Az új vCore-alapú szolgáltatási szinteket összes rugalmas készletek és a 100 dtu-inak vagy több adatbázis hasonló teljesítmény lehetőségek kínálnak.  Adja hozzá a további segítségével olyan sub 100 DTU munkaterhelések időbeli folytatjuk.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Vannak-e bármilyen adatbázis szolgáltatások eltérései között a meglévő vCore-alapú DTU-alapú és az új szolgáltatási szinteket? 
Az új szolgáltatási szinteket támogatja az aktuális DTU-alapú ajánlatok elérhető szolgáltatások felülbírálja. A további szolgáltatások közé tartoznak az olyan további dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek) és a további konfigurációs beállításai. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Ha az adatbázis CPU-korlátja, és mekkora helyet használ, tudom növelni a számítási a megnövelt tárhely fizető nélkül?
Igen, egymástól függetlenül kiválaszthatja az alkalmazás van szüksége, és a tárolás nem változik számítási szintjét. A tárolási akár csupán 32 GB-os állítható be. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Az új vCore-alapú szinteket támogatja pont visszaállítás egy korábbi időpontra (PITR) 35 napon, ma? 
A biztonsági másolatok megőrzésének PITR 7 és 35 nap között konfigurálható. A biztonsági másolatok tárolási külön-külön a a tényleges tárolási felhasználás alapján, ha az érték meghaladja a maximális adatok méretével megegyező mennyisége fogjuk számlázni. A képen alapértelmezés szerint a PITR megőrzési időtartam értéke 7 nap. Sok esetben az adatok maximális mérete 7 nap, a biztonsági mentések tárolására elegendő.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Miért engedélyezheti a számítási hardver generációs kiválasztása?
Célunk engedélyezik a legnagyobb rugalmasságot, így kiválaszthatja az alkalmazás igényeinek leginkább megfelelő teljesítmény konfiguráció. A fenti táblázatban Gen4 és Gen5 közötti különbségeket. Különösen Gen4 hardver kínál vCore száma jóval több memóriát. Azonban Gen5 hardver segítségével növelheti a jóval nagyobb számítási. Azt szeretnénk, hogy ezek a különbségek átlátszó, hogy az alkalmazás optimális price/teljesítményesemények aránya érhet el.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Kell tenni az alkalmazást a hálózatról a DTU-alapú adatbázis konvertálása vCore alapú szolgáltatásréteg? 
Az új szolgáltatáscsomagok egyszerű online konverziós módszert kínálnak, amely hasonló az adatbázisok Standardról Premium szolgáltatáscsomagra és vissza történő átállításának jelenlegi eljárásához. Ez a konverzió a portál, az ARM, a PowerShell, az Azure CLI vagy a T-SQL használatával indítható el. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-resources.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>I konvertálhatja egy adatbázist egy vCore-alapú szolgáltatási réteg alapján DTU-alapú egy? 
Igen, egyszerűen válthat az adatbázis bármely támogatott teljesítmény célra portálon vagy programozottan Portal, ARM, PowerShell, az Azure parancssori felület vagy a T-SQL. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-resources.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Frissítés vagy visszaminősítését között az általános célú és fontos üzleti szolgáltatási szinteket? 
Igen, bizonyos korlátozásokkal. A cél SKU meg kell felelnie a maximális adatbázis vagy a rugalmas készlet mérete a meglévő telepítéshez konfigurálta. Ha használ [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md), az üzleti kritikus termékváltozata csak Enterprise Edition-licenccel rendelkező ügyfél számára érhető el. Csak az ügyfelek, akik át a helyszíni Azure hibrid juttatás használatának az SQL Server Enterprise Edition-licenccel rendelkező általános célú fontos üzleti frissíthetők. További információ: [Mik azok a megfelelő jogosultságok az SQL Server Azure hibrid használata előnyeinek](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Az átalakításhoz nem állásidőt eredményezhettek, és a portálon, a ARM, a PowerShell, a Azure CLI vagy a T-SQL kezdeményezhető. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-resources.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Használom, amelyek nem lesznek általánosan elérhető Premium RS adatbázis - is szeretnék frissíti új réteget és elérése a hasonló price/teljesítménynövekedést?
A vCore modell lehetővé teszi, hogy a kiépített számítási és tárolási felett független vezérlő, mert hatékonyabban kezelheti költségeit, így egy prémium szintű RS adatbázisok vonzó célját. Emellett a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md) jelentős kedvezménnyel biztosít, amikor a vCore-alapú modellt használja. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Milyen gyakran módosíthatja az erőforrások készletenként?
Olyan gyakran ahányat csak szeretne. Lásd: [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Mennyi időt vesz igénybe a szolgáltatási szint vagy a teljesítmény szint egyetlen adatbázisra módosíthatja vagy adatbázis áthelyezése rugalmas készletek mindkét?
Egy adatbázis a szolgáltatási szint módosítása, és mindkét készlet áthelyezése van szükség az adatbázis másolása háttér műveletként a platformon. A szolgáltatási réteg eltarthat néhány percig adatbázisok méretétől függően több órába. Mindkét esetben az adatbázisokat továbbra is online és elérhető az áthelyezés során. Önálló adatbázisok módosításával kapcsolatos részletekért lásd: [módosíthatja a szolgáltatásszintet adatbázis](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Mikor kell használni egy önálló adatbázis és a rugalmas adatbázisok?
Általában rugalmas készletek készültek, a szokásos [szoftver,--szolgáltatás (SaaS) alkalmazásminta](sql-database-design-patterns-multi-tenancy-saas-applications.md), ahol van egy adatbázist használ az ügyfél vagy a bérlőt. Ebben a modellben önálló adatbázisok vásárlásával és a rendszer ebből adódó, túlzott mértékű kiépítésével tudnak megfelelni az adott adatbázisra vonatkozó változó, illetve a csúcsidőszakokban jellemző igényeknek. -Készletek kezelheti a készlet közös teljesítményéért, és az adatbázisok automatikus méretezése felfelé és lefelé. Azure intelligens motor adatbázisok készlet azt javasolja, amikor a használati mód szükségessé teszi. További információkért lásd: [rugalmas készlet útmutatást](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>A DTU-alapú alapjául szolgáló vásárlási modell használatával SQL-adatbázis használatát, hogyan nem jelennek meg a saját számlázási?
SQL-adatbázis váltók a egy előre jelezhető óránkénti arány alapján a [vásárlási modell](sql-database-service-tiers-dtu.md). Tényleges használathoz számított és egyenletesen szerint óránként, a számlán törtek az óra lehet, hogy megjelenítése. Például ha egy adatbázist egy hónapban a 12 óra, a számlán 0,5 nap-használatát mutatja. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Mi történik, ha egy önálló adatbázis aktív kisebb, mint egy óráig vagy magasabb szolgáltatásréteg kisebb, mint egy óráig használ?
Minden órában a legmagasabb szolgáltatásszint létezik adatbázis + használati vagy hogy az adatbázis aktív kisebb, mint egy óráig függetlenül, hogy órán belül alkalmazott teljesítményszintet kell fizetni. Például ha egy önálló adatbázis létrehozása, és törölje azt 5 percen belül a számlázási egy adatbázis óra díjat tükrözi. 

Példák:

* Ha egy alapszintű adatbázis létrehozása, majd azonnal frissítsen, Standard szintű, S1 van szó, a standard szintű, S1 díj az első egy óra.
* Ha rendszerről egy adatbázis Basic a Premium 10:00 órakor és reggel 1:35 frissítés befejezése a következő napon van szó, az 1:00 órakor kezdődő prémium díj 
* Ha Ön egy adatbázist a prémium alapvető visszaminősítését 11:00 órakor 2:15 órakor befejeződik, majd az adatbázis fel van töltve a prémium szintű díj 3:00 órakor, amíg elteltével az alapvető ütemben számítjuk fel.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>A DTU-alapú alapjául szolgáló vásárlási modell használatával rugalmas készlet használati módját nem jelennek meg a saját számlázási?
A rugalmas készlet díja megjelenítése fel a számlázási, rugalmas dtu-i (edtu-k) vagy vCores plus tárolás jelenik meg a lépésekben [az árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/sql-database/). Nincs a rugalmas adatbázis-díjmentes. A készlet létezik-e a legmagasabb edtu-ra vagy vCores, függetlenül a használati vagy a készlet volt-e aktív kisebb, mint egy óráig óránként kell fizetni. 

DTU-alapú vásárlási modell példák:

* Ha egy Standard rugalmas készletet hoz létre a 200 edtu-k szerint 18, öt adatbázisok hozzáadása a készlethez van szó, a 200 edtu-k az egész órát 11 órakor kezdődő a nap további része.
* Naponta 2, reggel 5:05 adatbázis 1 50 edtu-k fel kezdődik, és állandó napon keresztül tárolja. Adatbázisok 2 – 5 ingadozik 0 és 80 edtu-k között. A nap folyamán öt más adatbázisokhoz, napjainkat különböző edtu-k használó hozzáadása. 2 nap terhelve, 200 eDTU teljes napi. 
* A nap 3: 00 órai egy másik 15 adatbázisok hozzáadása. A pont, ha úgy dönt, hogy növelje a 400-as 200 a készlet edtu-k 8:05-kor, napjainkat növeli az adatbázis-használat A 200 eDTU szinten díjak érvényben levő csak 8 óra és a fennmaradó 4 óra 400 edtu-k száma növekszik. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Hogyan fizetnie kell a DTU-alapú rugalmas készlet modell beszerzési?
Rugalmas készletek óraalapú a következő jellemzőkkel:

* Rugalmas készlethez való létrehozás után lesz számlázva, akkor is, ha nincsenek a készletben lévő adatbázisok.
* Egy rugalmas készlet számlázása óránként történik. Ez a teljesítményszintet önálló adatbázisok esetében ugyanaz mérési gyakoriságát.
* Ha a rugalmas készletekben átméretezték, majd a készlet van nem számlázzuk a új mennyiségű erőforrást csak az átméretezési művelet befejeződése után. Ez az önálló adatbázisok teljesítményszintjének változó állapotúként azonos mintát követi.
* A rugalmas készletek ára az erőforrásokat a készlet alapul. A rugalmas készletek ára független a számát és a hozzá tartozó rugalmas adatbázisokban felhasználása.

További információkért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/), [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md), és [vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>A vCore-alapú használati módját nem jelennek meg a saját számlázási? 
A modellben vCore-alapú, a szolgáltatás a egy előre jelezhető, óradíjon alapuló arány a szolgáltatási réteg alapján számlázzuk kiépített számítási vCores, a tárolás GB/hó kiosztása, és biztonsági másolatokat tároló felhasznált. A biztonsági mentések tárolási meghaladja a teljes adatbázis mérete (Ez azt jelenti, hogy 100 %-a az adatbázis méretének), ha nincsenek további díjakat. vCore óra, a konfigurált adatbázis tároló, a felhasznált IO és a biztonsági másolatok tárolásának van egyértelműen felsorolva a számlázási, így megkönnyíti, hogy a Részletek területen használt erőforrások. Biztonsági mentési tároló legfeljebb 100 %-a maximális méretét megtalálható, túl, amely számlázása a GB/hó egy hónap során.

Példa:
- Ha az SQL-adatbázis már létezik egy hónapban a 12 óra, a számlázási használati vCore 12 óra jelennek meg. Ha az SQL-adatbázis egy további 100 GB tárhely megadva, a számlázási tárolási használatát mutatja a GB/hó egységei óránkénti arányosított és egy hónap során IOs száma.
- Ha kevesebb mint egy óráig az SQL-adatbázis aktív, az adatbázis létezik óránként használatával a legmagasabb szolgáltatásszint kijelölve, kiépítve a tároló és a használati és az aktív a kevesebb mint volt-e az adatbázis függetlenül, hogy órán belül alkalmazott IO számlázása egy óra.
- Ha létrehoz egy felügyelt példányt, majd öt perc múlva törli azt, akkor egy adatbázisóráért kell fizetnie.
- Ha az Általános célú csomagban hoz létre egy 8 virtuális maggal rendelkező felügyelt példányt, amelyet azonnal 16 magosra frissít, az első óráért a 16 magra vonatkozó díjat kell kifizetnie.

> [!NOTE]
> Korlátozott időre. június 30 2018 keresztül és biztonsági mentési IO díjakat, amelyek ingyenesen.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hogyan működik az aktív georeplikáció a rugalmas készlet megjelennek a számlán meg a használatát?
Önálló adatbázisok használatával ellentétben [aktív georeplikáció](sql-database-geo-replication-overview.md) rugalmas adatbázisok nem közvetlen hatást számlázási.  Csak az egyes a készletek (alkalmazáskészlet elsődleges és másodlagos készlet) kiosztott erőforrásokat a van szó

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Milyen hatással van a számlára a naplózási funkció használata?
A SQL Database szolgáltatásba: nincs további naplózás beépített költségeket, és minden szolgáltatásszintek érhető el. Azonban a naplók tárolásához, a naplózási szolgáltatás által használt, egy Azure Storage-fiókot, és a táblák és az Azure Storage üzenetsorokat díjakat alapján a napló méretét.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hogyan a kiszolgáló rendszergazdai jelszavának visszaállítása?
Az a [Azure-portálon](https://portal.azure.com), kattintson a **SQL Server-kiszolgálók**, válassza ki a kiszolgálót a listából, és kattintson a **jelszó alaphelyzetbe állítása**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hogyan kezelhető adatbázisok és bejelentkezések?
Lásd: [adatbázisok és bejelentkezések kezelése](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hogyan tehetem meg arról, hogy csak engedélyezett IP-címek használata engedélyezett a kiszolgálóhoz való hozzáférést?
Lásd: [hogyan: SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md).

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
