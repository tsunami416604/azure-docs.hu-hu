---
title: Az Azure SQL Database – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések ügyfeleknek adott válaszok érdeklődjön felhőalapú adatbázisok és az Azure SQL Database, a Microsoft relációs adatbázis-kezelő rendszer (RDBMS) és adatbázis szolgáltatás a felhőben.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 7bb692735cb3541fb05b2c39dd7325fa74a00034
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232205"
---
# <a name="sql-database-frequently-asked-questions-faq"></a>Az SQL Database – gyakori kérdések (GYIK)

## <a name="what-is-the-current-version-of-sql-database"></a>Mi a jelenlegi verzió az SQL Database

A jelenlegi verzió az SQL Database V12-es. Verzió V11 visszavontuk.

## <a name="what-is-the-sla-for-sql-database"></a>Mit jelent az SLA-t az SQL Database

Garantáljuk, hogy az idő legalább 99,99 %, a, hogy rendelkezik a Microsoft Azure SQL Database és az internetes átjárónkhoz között a szolgáltatási szintben függetlenül. 0,01 % javítások, frissítések és feladatátvétel van fenntartva. További információkért lásd: [SLA](http://azure.microsoft.com/support/legal/sla/). Azure SQL Database a rendelkezésre állási architektúrával kapcsolatos információk: [magas rendelkezésre állást és az Azure SQL Database](sql-database-high-availability.md).

## <a name="can-i-control-when-patching-downtime-occurs"></a>Szabályozhatom javítás leállás esetén

Nem. A javítás hatását az általában nem észrevehető Ha Ön [újrapróbálkozási logikát alkalmazni](sql-database-develop-overview.md#resiliency) az alkalmazásban.

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Mi az az új vCore-alapú vásárlási modell az Azure SQL Database

Az új vásárlási modell mellett továbbra is használható a már meglévő DTU-alapú modell. A Virtuálismag-alapú modell úgy tervezték, hogy ügyfelei rugalmasan, átláthatóság, vezérlőelem, és a közvetlen módon lefordítani a helyszíni tevékenységprofil követelményeinek a felhőben. Azt is lehetővé teszi, hogy ügyfelek számára, hogy azok számítási és tárolási erőforrást, a munkaterhelés igényei alapján. Önálló adatbázis és rugalmas készlet beállításai a Virtuálismag-modell használatával jogosultak is fel, a 30 %-os megtakarítást a [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#services). Lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) további információt.

## <a name="what-is-a-vcore"></a>Mi az a virtuális mag

Egy virtuális magot és generációk hardver lehetőség elérhető logikai CPU jelöli. Gen 4 logikai CPU k Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorral és a Gen 5 logikai CPU alapulnak Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorral.

## <a name="is-moving-to-the-vcore-based-model-required"></a>A Virtuálismag-alapú modell szükséges kerül át

Nem, a rugalmas készlet és különálló adatbázishoz a Virtuálismag-alapú modell bevezetésével elkötelezettségünknek customer és szabadságának jeleníti meg. Ha ügyfeleket szeretne továbbra is a DTU-alapú modellel, nincs szükségük az ezt a hirdetményt és szerzett semmit sem kell, és a számlázás változatlan marad.

Sok esetben alkalmazások egy előre konfigurált erőforrások kötegelt egyszerűsége is kihasználhatják. Így továbbra is kínál, és ezek szolgáltatásunkat az ügyfelek számára a DTU-alapú lehetőségek támogatja. Ha használja őket, és az üzleti igényeinek megfelel-e, hogy továbbra is ehhez.

A DTU- és a virtuálismag-alpú modell továbbra is egymás mellett lesz elérhető. Mi a Virtuálismag-alapú modell az adatbázis-erőforrások és függetlenül méretezhető a számítási és tárolási erőforrások jobb áttekinthetőségét kéréseinek indította el. A Virtuálismag-alapú modell is további megtakarításokat tesz lehetővé az Azure Hybrid Benefit révén frissítési garanciával rendelkező ügyfelek számára az SQL Serverhez.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Hogyan válasszak a DTU-alapú vásárlási modell vs között a Virtuálismag-alapú vásárlási modell

A Database Transaction Unit (adatbázis-tranzakciós egység – DTU) a CPU- és memóriahasználaton, valamint az olvasási és írási műveletek számán alapuló vegyes mérték. A DTU-alapú számítási méretű előre konfigurált erőforráscsomagoknak felelnek az erőforrások teljesítményszintek különböző alkalmazásteljesítmény. Ügyfelek, akik nem kell foglalkoznia a mögöttes erőforrások, és míg rögzített havi fizetésével előre konfigurált csomaggal szeretne előfordulhat, hogy keresse meg a DTU-alapú modell jobban megfelel az igényeiknek. Ügyfelek esetében, akik jobban átlátni a mögöttes erőforrások, illetve kell méreteznie azokat egymástól függetlenül, hogy optimális teljesítményt, azonban a Virtuálismag-alapú modell lesz a legjobb választás.  Emellett, ha egy ügyfél az SQL Server egy aktív frissítési garanciával (SA) rendelkezik, kihasználhatja a meglévő befektetések és is akár 30 %-a Mentés [SQL Serverhez készült Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Beszerzési modell belül lehetőségek előnyei a egy teljes körűen felügyelt szolgáltatás, például az automatikus biztonsági másolatokat, szoftverfrissítések és javításait.

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Mi az SQL Serverhez készült Azure Hybrid Benefit

A [SQL Serverhez készült Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) segítségével maximalizálhatja a licencelési beruházásai értékét, és felgyorsíthatja a migrálás a felhőbe. Az Azure Hybrid Benefit az SQL Server rendszer egy Azure-alapú kedvezmény, amely lehetővé teszi, hogy az SQL Server-licenceit frissítési garanciával rendelkező kell fizetnie ("alapdíjjal") kedvezményes díjra jogosít az SQL Database. Az Azure Hybrid Benefit SQL Serverhez a Virtuálismag-alapú vásárlási modell SQL Database önálló adatbázisok és rugalmas készletek a nyilvános előzetes verzióban érhető el. Előfordulhat, hogy ezzel az értékelemmel alkalmazza, akkor is, ha a Termékváltozat aktív, de vegye figyelembe az alapdíj mellett a alkalmazni, válassza ki azt az Azure Portalon. Visszamenőleges jóváírásra nincs mód.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Vannak-e kettős-használati jogok az Azure Hybrid Benefittel az SQL Server

Kettős használati jogok a licenc annak biztosítása érdekében az áttelepítés zökkenőmentesen futnak, 180 nap áll rendelkezésére. A 180 napos időszak után a SQL Server licence csak akkor használható a felhőben, SQL Database-ben, és nincs kettős használati jogok a helyszíni és a felhőben.

## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Miben licenc mobility az Azure Hybrid Benefittel az SQL Server

Licenc mobility előnyeit még ma, az SQL Server-ügyfelek számára, amely lehetővé teszi az ismételt hozzárendelése után a külső megosztott kiszolgálókra licenceiket frissítési garanciával rendelkező biztosítunk. Ez a kedvezmény az Azure IaaS- és AWS EC2 lehet használni.
Az Azure Hybrid Benefittel az SQL Server eltér a licenchordozhatósági program két fő területen:

- Nagymértékben virtualizált számítási feladatok áthelyezése az Azure gazdasági haszna biztosítja. SQL EE magonkénti 4 mag az Azure-ban minden egyes tartozó magért általános célú termékváltozatban saját nagymértékben virtualizált alkalmazások helyszíni. Licenc mobility nem engedélyezi a semmilyen különleges árelőnyök virtualizált számítási feladatok áthelyezése a felhőbe.
- Biztosítja az Azure-on (SQL Database felügyelt példánya), amely nagy mértékben kompatibilis a helyszíni SQL Server egy PaaS-cél

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Mik az Azure Hybrid Benefit SQL Serverhez, a megfelelő jogosultságok

Az SQL Database ügyfelei az Azure Hybrid benefittel az SQL Server társított alábbi jogosultságokkal fog rendelkezni:

|Licenc üzembe helyezésének előkészítése|Az SQL Server beolvasása, mire Azure Hybrid Benefit?|
|---|---|
|Az SQL Server Enterprise Edition core ügyfeleit, SA|<li>Általános célú vagy üzletileg kritikus fontosságú Termékváltozatoknál alapegysége is kell fizetnie.</li><br><li>helyszíni 1 mag = 4 mag, általános célú termékváltozatban</li><br><li>helyszíni 1 mag = 1 mag az üzletileg kritikus fontosságú Termékváltozatoknál</li>|
|SQL Server Standard Edition core ügyfeleit, SA|<li>Fizethet a szolgáltatásért alapegysége/az általános célú Termékváltozatban csak</li><br><li>helyszíni 1 mag = 1 mag, általános célú termékváltozatban</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>A Virtuálismag-alapú modell érhető el az SQL Database felügyelt példány

[Felügyelt példány](sql-database-managed-instance.md) csak a Virtuálismag-alapú modell érhető el. További információk is: a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>A számítási és tárolási költsége a szolgáltatási rétegben, amely kiválasztása függ

A számítási költségeket tükrözi az összes számítási kapacitás, amely ki van építve az alkalmazás. Az üzletileg kritikus szolgáltatási rétegben hogy automatikusan lefoglalni a legalább 3 replika. A számítási erőforrások a további lefoglalási megfelelően, a 2.7 x magasabb a kritikus fontosságú üzleti szolgáltatás virtuális mag díja. Ugyanezen okból, magasabb tárolási ár GB-onként az üzletileg kritikus tartalmazza a nagy i/o és alacsony késést az SSD-tárhelyet. Egy időben a költség, a biztonsági mentési tár nem tér mindkét esetben a standard szintű tárterülettel osztály használatának.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Hogyan fizetnem tárolás – mi konfigurálható előre vagy az adatbázis használja a alapján

Különböző típusú tárolóhelyek eltérően számítjuk fel. Az adattárolás díjkötelesek a felhasznált tárterület alapján választja maximális adatbázis vagy készlet méretét. Díja nem változik, kivéve, ha csökkentse vagy növelje a legnagyobb. Biztonsági mentési tár a példány automatizált biztonsági mentések társított, és dinamikusan le van foglalva. A biztonsági másolatok megőrzési időszakának kiterjesztésével a példány által felhasznált biztonsági mentési tárterület is nő. A kiszolgáló kiépített tárolója 100%-ának megfelelő biztonsági mentési tár díjmentesen használható. További használati a biztonsági mentési tár díjszabásának GB / hó. Ha például az adatbázistárhelye 100 GB, 100 GB biztonsági mentési tárhelyet kap díjmentesen. De ha a biztonsági mentés mérete 110 GB, 10 GB Többletméretért fizetnie.

Biztonságimásolat-tároláshoz egyetlen adatbázis a számlázás a tárolóhoz az adatbázis biztonsági mentéséhez az adatbázis mérete csökkentve lett lefoglalva arányos alapon. A biztonsági mentési tár egy rugalmas készlet a számlázás a arányos alapon, az adatbázis biztonsági másolatait a rugalmas készlet adatok maximális mérete csökkentve a készletben található összes adatbázishoz lefoglalt tárolására. Azokat az adatbázis mérete vagy a rugalmas készlet növekedése, illetve a tranzakciós sebesség növekedése több tárhelyet igényel, és így növeli a biztonsági mentési tár számlázási.  Ha növeli a maximális adatméret, ez a mennyiség vonni számlázott biztonsági másolati tárhely méretét.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Hogyan válassza meg a megfelelő Termékváltozatot, ha egy meglévő adatbázis konvertálása folyamatban van az új szolgáltatási csomagokra

Meglévő SQL-adatbázis alkalmazások a DTU-alapú modellel az általános célú szolgáltatásszint összehasonlítható a Standard szint. Az üzletileg kritikus szolgáltatási rétegben az a prémium szintű hasonlítható. Mindkét esetben érdemes lefoglalni az egyes 100 dtu-k az alkalmazás által használt a DTU-alapú modell legalább 1 virtuális mag.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-compute-sizes-compatible-with-all-existing-compute-sizes"></a>Hajtsa végre az új, Virtuálismag-alapú szolgáltatásszintek kínálnak, a számítási méret kompatibilis az összes meglévő számítási méret

Az új, Virtuálismag-alapú szolgáltatásszintek összehasonlítható teljesítményszintek az összes rugalmas készletek és adatbázisok használatával 100 dtu-k vagy több kínálnak.  Folytatjuk hozzáadásához a nagyobb számítási teljesítményt befogadásához idővel méretek sub 100 DTU számítási feladatokhoz.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Vannak-e bármilyen adatbázis szolgáltatás funkciói a meglévő DTU-alapú és az új vCore-alapú szolgáltatásszintek közti különbségekről

Az új szolgáltatási szintekről felülbírálja a az aktuális DTU-alapú termékek szolgáltatásait támogatja. A további szolgáltatásai többek között további dinamikus felügyeleti nézetekkel (DMV-kkel) és további konfigurációs beállításai.

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Ha saját adatbázis processzorigénye, és mennyi tárhelyet használ, növelhető a számítási anélkül az extra tárterületért

Igen, egymástól függetlenül kiválaszthatja az alkalmazás van szüksége, és nem változik a tárhely tartsa számítási szintjét. A storage akár 32 GB-ot is beállítható.

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Az új vCore-alapú szolgáltatásszintek támogatni fogja pont 35 napig a mai (PITR) időpontra

A biztonsági másolatok megőrzése 7 – 35 nap közötti PITR konfigurálható. A biztonsági másolatok tárolási számítunk fel külön a tényleges tárterület-használat alapján, ha ez meghaladja a maximális adatméret egyenlő tárolókapacitás. Előzetes verzióban elérhető alapértelmezés szerint a PITR a megőrzési időszak értéke 7 nap. Sok esetben adatok maximális mérete 7 nap a biztonsági mentések tárolásához elegendő.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Miért érdemes engedélyeznie a számítási hardver generációja kiválasztását

Az a célunk, hogy engedélyezze a lehető legnagyobb rugalmasságot, hogy kiválaszthatja, hogy a teljesítmény-konfigurációkat, amely szorosan megfelel az alkalmazás igényeinek. Gen4 hardver / virtuális mag lényegesen több memóriát biztosít. Azonban Gen5 hardver lehetővé teszi a vertikális felskálázása a számítási erőforrások sokkal magasabb. További információkért lásd: [Virtuálismag-beszerzési modell](sql-database-service-tiers-vcore.md)

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Kell az alkalmazásom átalakítása a Virtuálismag-alapú szolgáltatási szinten a DTU-alapú adatbázis

Az új szolgáltatáscsomagok egyszerű online konverziós módszert kínálnak, amely hasonló az adatbázisok Standardról Premium szolgáltatáscsomagra és vissza történő átállításának jelenlegi eljárásához. Ez a konverzió használatával indítható el az Azure portal, PowerShell, Azure CLI-vel, a T-SQL vagy a REST API-t. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Átválthatók egy adatbázist a Virtuálismag-alapú szolgáltatási szinten a DTU-alapú egy

Az adatbázis Igen, minden támogatott teljesítmény célja az Azure portal, PowerShell, Azure CLI-vel, a T-SQL vagy a REST API használatával könnyedén átalakíthatja. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Frissítse és Visszaléptetés az általános célú és az üzletileg kritikus szolgáltatási csomagok között

Igen, bizonyos korlátozásokkal. A cél Termékváltozat meg kell felelnie az adatbázis maximális vagy a rugalmas készlet mérete, a meglévő üzembe helyezési beállított. Ha használ [SQL Serverhez készült Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md), üzletileg kritikus fontosságú Termékváltozatoknál csak Enterprise Edition-licencek rendelkező ügyfelek számára érhető el. Csak olyan ügyfelek, akik át a helyszínről az Azure Hybrid Benefit használatával az SQL Server Enterprise Edition-licencek az általános célú frissítheti üzletileg kritikus fontosságú. További részletekért lásd: [Mik az Azure Hybrid Benefit SQL Serverhez, a megfelelő jogosultságok](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Ez a konverzió nem okoz állásidőt, és az Azure portal, PowerShell, Azure CLI-vel, a T-SQL vagy a REST API használatával indítható el. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Használom, amely nem lesz általánosan elérhető a prémium RS adatbázis - helyezhetem frissít egy új csomag és egy hasonló ár-teljesítmény haszon elérése

A Virtuálismag-modell lehetővé teszi, hogy a kiépített számításért és tárolásért mennyisége független felett, mert hatékonyabban kezelheti költségeit, így egy vonzó cél prémium RS adatbázisok számára. Emellett a [SQL Serverhez készült Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) egy jelentős kedvezményt nyújt a Virtuálismag-alapú modell használata.

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Milyen gyakran módosíthatja az erőforrások száma készletenként

Olyan gyakran a kívánt. Lásd: [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-compute-size-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Mennyi ideig tart módosíthatja a szolgáltatásszintet, vagy egy önálló adatbázis mérete számítási vagy adatbázis elhelyezhetők a rugalmas készlet

Módosítása az adatbázis és a egy készlet áramló szükséges háttér műveletként platformon kell másolni az adatbázist. A szolgáltatási szint is igénybe vehet pár percet vagy az adatbázis méretétől függően több órát. Mindkét esetben az adatbázisokat továbbra is online és elérhető az áthelyezés során. Önálló adatbázisok módosításával kapcsolatos részletekért lásd: [adatbázis szolgáltatási szintjének módosítása](sql-database-service-tiers-dtu.md).

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Mikor célszerű használni egy önálló adatbázis és rugalmas adatbázisok

Általánosságban véve a rugalmas készletek tervezett tipikus [szoftver--szolgáltatásként (SaaS) alkalmazásminta](sql-database-design-patterns-multi-tenancy-saas-applications.md), ahol az ügyfél vagy a bérlő egy adatbázist. Önálló adatbázisok vásárlásával és a változó, valamint minden adatbázisra vonatkozó túlzott kiosztása ez sokszor nem költséghatékony megoldás. A készletek kezelheti a készlet közös teljesítményéért teljesítményét, és az adatbázisok automatikusan skálázható felfelé és lefelé. Intelligens motor az Azure a készletben az adatbázisok használatát javasolja, amikor a használati minta szükségessé teszi. További információkért lásd: [rugalmas készlet útmutatást](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Hogyan jelenik az SQL Database-hez a DTU-alapú vásárlási modell-használatát, meg a számlámon

Az SQL Database számlázása alapján egy kiszámítható óradíj alapján a [beszerzési modell](sql-database-service-tiers-dtu.md). Tényleges használat számított és számoljuk el Óránként, ezért a számlán tört egy óra. Például ha egy adatbázis egy hónapban 12 órán át létezik, a számlán 0,5 nap használat látható.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Mi történik, ha egyetlen e egy óránál kevesebb ideig aktív, vagy egy magasabb szolgáltatási szintre használ egy óránál kevesebb

Számlázása óránként, a legmagasabb szolgáltatási szintet létezik adatbázis + compute-méretet, létezése alatt, hogy egy óránál kevesebb ideig volt az adatbázis aktív függetlenül. Például ha egy önálló adatbázis létrehozása, és öt perc múlva törli azt a számla egy adatbázisóráért díját tükrözi.

Példák:

- Ha létrehoz egy alapszintű adatbázist, és majd azonnal Standard S1 szintűre, akkor az első órában számoljuk el a Standard S1 díjszabással.
- Ha frissít egy adatbázist, az alapszintű, prémium szintű 10:00 órakor és a frissítés fejeződik be, 1:35-kor a következő napon, díjon számoljuk el a prémium szintű 1:00 órakor indítása
- Ha egy prémium szintű adatbázisról alapszintű Visszaléptetés a 11:00 órakor 2:15 órakor fejeződik be, majd az adatbázis havidíjat számítunk fel a prémium tarifa amíg 3:00 órakor, amely után, akkor az alapszintű díjszabás szerint számlázzuk.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>A rugalmas készlet használata a DTU-alapú vásárlási modell használatával hogyan jelenik meg a számlámon

Rugalmas készlet díjak show fel a számlán, rugalmas dtu-kkal (edtu-k) vagy a virtuális magok plusz a storage jelenik meg a lépésekkel [a díjszabási lapon](https://azure.microsoft.com/pricing/details/sql-database/). Nem jár a rugalmas adatbázis-költségekkel. Egy készlet a legmagasabb edtu-k vagy a virtuális magok, hogy egy óránál kevesebb ideig volt a készlet active függetlenül meglétének minden órája számlázzuk ki.

DTU-alapú vásárlási modell példák:

- Ha 11:18 órakor 200 edtu-t hoz létre egy Standard rugalmas készletek, öt adatbázisok hozzáadása a készlethez, díjkötelesek 200 edtu-k az egész órát 11 órakor kezdődő – az a nap fennmaradó részében.
- 2. napon, reggel 5:05 adatbázis 1 50 edtu-t használó kezdődik, és állandó napon keresztül tárolja. 2 – 5 adatbázisok 0 és 80 edtu-k között ingadozik. A nap folyamán öt más adatbázisok által felhasználható a nap folyamán különböző edtu-k hozzáadása. 2. napon 200 edtu-k számlázása egész napos.
- A napi 3, 5-kor hozzáadhat egy másik 15 adatbázisok. Adatbázis használattal növeli a pont, ha úgy dönt, hogy növelje a 400-as, 200-ról a készlet edtu-k 8:05 órakor, a nap folyamán A 200 eDTU szintjén díjak érvényben levő du. 8-ig, és a fennmaradó 4 órán keresztül 400 edtu növeli.

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Hogyan megvásárolt rugalmas készlet kell díjat fizetni a DTU-alapú modell

Rugalmas készletek számlázása a következő jellemzőkkel:

- Rugalmas készlet a létrehozása után történik, akkor is, ha nincsenek a készletben található adatbázisok.
- Rugalmas készlet óradíjat kell fizetni. Ez az önálló adatbázisok számítási méretű meghajtóbetűjeleket azonos mérési gyakoriságát.
- Rugalmas készlet átméretezték, majd a készlet nem számoljuk fel az új erőforrások mennyisége alapján az átméretezési művelet befejeződéséig. Ez a következő ugyanazt a mintát, mint az önálló adatbázisok számítási méretének módosítása.
- A rugalmas készlet árát az erőforrásokat a készlet alapul. Rugalmas készletek ára független, és a benne található rugalmas adatbázisok kihasználtsága.

További információkért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/), [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md), és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Hogyan jelenik a Virtuálismag-alapú használat meg a számlámon

A Virtuálismag-alapú modellben a szolgáltatás díjszabása a szolgáltatásszint alapján kiszámítható, óraalapú díjakra épül, kiépített számítási feladatain, kiépített, GB/hó tárolási, és a felhasznált biztonsági mentési tár. A biztonsági mentések tárolási meghaladja az adatbázis teljes méretét (azaz 100 % az adatbázis mérete), ha nincsenek további díjakat. a Virtuálismag-óradíjak, a konfigurált adatbázistárhely, a felhasznált i/o és a biztonsági mentési tár vannak egyértelműen részletezett a számlán, egyszerűbben erőforrásokat használja, a részletek megtekintéséhez. Biztonsági mentési tár akár a maximális adatbázisméret 100 %-át részét képezi, amely használatáért túli GB/hó az adott hónapban felhasznált.

Példa:

- Ha az SQL-adatbázis egy hónapban 12 órán át létezik, a számla 12 órányi Virtuálismag használati mutatja be. Ha az SQL database további 100 GB tárhelyet, a számlán a, és tartalmazza óraalapú számítással, az adott hónapban felhasznált a tárhelyhasználat GB/hó egységekben jeleníti meg.
- Ha az SQL-adatbázis egy óránál kevesebb beállítás aktiválása után számítunk fel díjat az adatbázis meglétének minden órája a legmagasabb szolgáltatási szintet, üzembe helyezett, tárolási és -nak létezése alatt, függetlenül a használat vagy az adatbázis aktív volt-e a kisebb, mint egy óra.
- Ha létrehoz egy felügyelt példányt, majd öt perc múlva törli azt, akkor egy adatbázisóráért kell fizetnie.
- Ha az Általános célú csomagban hoz létre egy 8 virtuális maggal rendelkező felügyelt példányt, amelyet azonnal 16 magosra frissít, az első óráért a 16 magra vonatkozó díjat kell kifizetnie.

> [!NOTE]
> Korlátozott ideig ingyenesen biztonsági mentésért és i/o.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Hogyan történik az aktív georeplikáció egy rugalmas készlet jelennek meg a számlámon a használatát

Önálló adatbázisok használatával ellentétben [aktív georeplikáció](sql-database-geo-replication-overview.md) a rugalmas adatbázisoknak nem közvetlen hatást számlázási.  Csak díjkötelesek minden, a készletek (készlet elsődleges és másodlagos készlet) kiosztott erőforrásokat

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Milyen hatással van a a naplózási funkció használata a számlámon

Naplózás az SQL Database szolgáltatás részeként be van építve az összes szolgáltatáscsomagban elérhető. Azonban a naplófájlok a naplózási szolgáltatás által használt Azure Storage-fiókot, és a táblák és üzenetsorok az Azure Storage díjszabása a alkalmazni alapján a napló méretét.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Hogyan alaphelyzetbe állítja a jelszót a kiszolgálói rendszergazda

Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL Server-kiszolgálók**, válassza ki a kiszolgálót a listából, és kattintson **jelszó alaphelyzetbe állítása**.

## <a name="how-do-i-manage-databases-and-logins"></a>Hogyan kezelhetem az adatbázisok és bejelentkezések

Lásd: [adatbázisok és bejelentkezések kezelése](sql-database-manage-logins.md).

> [!NOTE]
> A kiszolgálói rendszergazdai fiók nevét létrehozás után nem módosítható.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Hogyan ellenőrizhetem, hogy csak engedélyezett IP-címek engedélyezettek a kiszolgálóhoz való hozzáférést

Lásd: [Útmutató: az SQL Database tűzfalbeállításainak konfigurálása](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Mi az a várt replikációs késés replikáláskor geo adatbázis Azure azonos földrajzi helyen belül két régió között

A Microsoft jelenleg támogatják a helyreállítási Időkorlátot öt másodperc és a replikációs késés kevesebb, mint, hogy amikor a geo-secondary az Azure-ban üzemeltetett ajánlott párosított régió és ugyanazon a szolgáltatásszinten.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Mit jelent egy várt replikációs késés a geo-secondary ugyanabban a régióban, az elsődleges adatbázis létrehozásakor

Empirikus adatok alapján esetén nem túl sok intra-régióban vagy régiók közötti replikációs késés közötti különbség az Azure ajánlott párosított régióra történik.

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Ha két régió között hálózati hiba, az újrapróbálkozási logika működése georeplikáció üzembe helyezésekor

Ha egy kapcsolat bontása, 10 másodpercenként próbálkozni kapcsolatok újbóli létrehozásához.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Mire használhatom garantálja, hogy a rendszer replikálja az elsődleges adatbázison kritikus módosítása

A geo-secondary egy aszinkron replika, és azt ne próbálja az elsődleges és a teljes szinkronizálás tárolja. De kényszerítését szinkronizálási annak biztosítására, a replikáció kritikus változások (például jelszó frissítések) biztosítunk. A kényszerített szinkronizálási mert blokkolja a hívó szálat mindaddig, amíg minden véglegesített tranzakciók replikálva vannak hatással van a teljesítményre. További információkért lásd: [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Milyen eszközök állnak rendelkezésre a replikációs késés az elsődleges adatbázis és a geo-secondary figyelése

A valós idejű replikációs késés az elsődleges adatbázis és a egy DMV keresztül földrajzilag másodlagos közötti elérhetővé tesszük. További információkért lásd: [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>-Adatbázis áthelyezése egy másik kiszolgálóra ugyanabban az előfizetésben

Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL-adatbázisok**, válasszon ki egy adatbázist a listából, és kattintson **másolási**. Lásd: [egy Azure SQL database másolása](sql-database-copy.md) további részleteket talál.

## <a name="to-move-a-database-between-subscriptions"></a>-Adatbázis áthelyezése előfizetések között

Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL Server-kiszolgálók** , és válassza ki azt a kiszolgálót a listából az adatbázist tároló. Kattintson a **áthelyezése**, majd válasszon az áthelyezni kívánt erőforrásokat és az előfizetés áthelyezése.
