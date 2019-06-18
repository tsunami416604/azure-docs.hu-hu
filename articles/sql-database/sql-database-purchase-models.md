---
title: Vásárlási modellek az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, amely elérhető az Azure SQL Database-beszerzési modell.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431370"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>A vcore magok és a dtu-k vásárlási modellek közül választhat

Az Azure SQL Database egy teljes körűen felügyelt platformmal könnyedén beszerzés a platformszolgáltatás (PaaS) adatbázis-kezelő a teljesítménnyel és költségekkel igényeinek leginkább teszi lehetővé. Úgy döntött, az Azure SQL Database üzemi modelljétől függően válassza ki az Ön számára legmegfelelőbb beszerzési modell:

- [Virtuális mag (más néven vCore)-alapú vásárlási modell](sql-database-service-tiers-vcore.md) (ajánlott). A vásárlási modell lehetővé teszi a választást a kiépített számítási kapacitás és a egy kiszolgáló nélküli (előzetes verzió) számítási szint között. És a kiépített számítási réteg ki az alkalmazások és szolgáltatások mindig felhasznált számítási erőforrások pontos mennyisége. A kiszolgáló nélküli számítási kapacitás, határozhat meg a számítási erőforrások automatikus méretezése egy konfigurálható compute-tartományban. Ez a számítási teljesítményszint, az automatikusan is is szüneteltetése és folytatása az adatbázis-munkaterhelés tevékenységek alapján. A virtuális mag egységár egységenként idő a kiépített számítási rétegben alacsonyabb, mint a kiszolgáló nélküli számítási szinten van.
- [Adatbázis-tranzakciós egységek (DTU)-alapú vásárlási modell](sql-database-service-tiers-dtu.md). A vásárlási modell a leggyakoribb munkaterhelésekhez elosztott terhelésű csomagolt számítási és tárolási csomagokat biztosít.

Különböző vásárlási modell a különböző Azure SQL Database üzembe helyezési modellek számára érhetők el:

- A [önálló adatbázis](sql-database-single-databases-manage.md) és [rugalmas készlet](sql-database-elastic-pool.md) üzembe helyezési lehetőségei a [Azure SQL Database](sql-database-technical-overview.md) egyaránt kínál a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- A [felügyelt példány](sql-database-managed-instance.md) csak kínál az Azure SQL Database üzembe helyezési lehetősége a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- A [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md) érhető el, amelyek az önálló adatbázisok számára a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

Az alábbi táblázatos és hasonlítsa össze a Virtuálismag-alapú és a DTU-alapú beszerzési modell:

|**Beszerzési modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell csomagolt méri, számítási, tárolási és i/o-erőforrások alapján. A Dtu az önálló adatbázisok és a rugalmas database transaction unitok (Edtu) rugalmas készletek számítási méretek ki. További információ a dtu-król és edtu-k: [Mik a dtu-król és edtu-k?](sql-database-purchase-models.md#dtu-based-purchasing-model).|A legjobb erőforrás egyszerű, előre konfigurált beállítások használni.|
|vCore-alapú modell|Ez a modell lehetővé teszi a számítási és tárolási erőforrások egymástól függetlenül kiválasztását. A Virtuálismag-alapú vásárlási modell azt is lehetővé teszi, hogy [SQL Serverhez készült Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) költségmegtakarítást biztosítanak.|Legjobb ügyfelek esetében, akik a rugalmasságot, a szabályozhatóság, és értékét.|
||||  

![díjszabási modellek összehasonlítása](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Számítási költségeit

### <a name="provisioned-compute-costs"></a>Kiépített számítási költségei

A kiépített számítási kapacitás a számítási költségeket az összes számítási kapacitás, amely ki van építve az alkalmazás jeleníti meg.

Az üzleti kritikus szolgáltatási rétegben hogy automatikusan lefoglalni a legalább 3 replika. A számítási erőforrások a további lefoglalási megfelelően, a körülbelül 2.7 x kritikus fontosságú üzleti szolgáltatásszinten magasabb, mint az általános célú szolgáltatásszinten lévő szolgáltatás díja az a Virtuálismag-alapú vásárlási modell. Hasonlóképpen a magasabb tárolási GB-onként kritikus fontosságú üzleti szolgáltatásszinten díjak a nagy i/o és alacsony késést az SSD-tárhelyet.

A költség, a biztonsági mentési tár megegyezik az üzleti kritikus fontosságú szolgáltatási szint és az általános célú szolgáltatási rétegben, mert mindkét csomag a standard szintű tárolást használnak.

### <a name="serverless-compute-costs"></a>Kiszolgáló nélküli számítási költségei

A kiszolgáló nélküli számítási kapacitás hogyan van definiálva a számítási kapacitást leírását és a költségek kiszámítása, lásd: [SQL Database kiszolgáló nélküli (előzetes verzió)](sql-database-serverless.md).

## <a name="storage-costs"></a>Tárolási költségek

Különböző típusú tárolóhelyek eltérően számítjuk fel. Az adattárolás függetlenül díjkötelesek a felhasznált tárterület alapján választja maximális adatbázis vagy készlet méretét. Díja nem változik, kivéve, ha csökkentse vagy növelje a legnagyobb. Biztonsági mentési tár a példány automatizált biztonsági mentések társított, és dinamikusan le van foglalva. A biztonsági mentési tár a példány által felhasznált biztonsági mentési-megőrzési időszakának kiterjesztésével nő.

Alapértelmezés szerint 7 napi automatikus biztonsági másolatokat az adatbázisok írásvédett georedundáns tárolás (RA-GRS) standard szintű Blob storage-fiók lesz másolva. Ezt a tárolót a heti teljes biztonsági mentésekhez, a napi differenciális biztonsági mentésekhez és a tranzakciós naplók biztonsági mentését, amelyeket a rendszer másol át 5 percenként használják. A tranzakciónapló mérete attól függ, hogy az adatbázis a változási gyakoriság. Egy az adatbázis méretének 100 %-os egyenlő minimális tárterület keletkezett külön díjfizetés nélkül van megadva. További használati a biztonsági mentési tár díjszabásának GB / hó.

Tárolási díjszabás kapcsolatos további információkért lásd: a [díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/) lapot.

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

Virtuális mag (más néven vCore) egy logikai CPU, és felajánlja, hogy hardver generációja és fizikai jellemzők a hardvert (például a magok, memória és a tárméret száma) közül választhat. A Virtuálismag-alapú vásárlási modell lehetővé teszi, rugalmasság, a vezérlő, egyéni erőforrás-használat átláthatósága, és a közvetlen módon lefordítani a helyszíni tevékenységprofil követelményeinek a felhőben. Ez a modell lehetővé teszi, hogy válassza ki a munkaterhelés igényei alapján számítási, memória és tárolási erőforrásokat.

A Virtuálismag-alapú vásárlási modell, választhat a [általános célú](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) és [üzletileg kritikus](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) a szolgáltatásszintek [önálló adatbázisok](sql-database-single-database-scale.md), [ rugalmas készletek](sql-database-elastic-pool.md), és [felügyelt példányai](sql-database-managed-instance.md). Az önálló adatbázisokhoz is választhat a [nagy kapacitású szolgáltatásszint](sql-database-service-tier-hyperscale.md).

A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül válassza ki a számítási és tárolási erőforrások, a helyszíni teljesítmény és ár optimalizálása. A Virtuálismag-alapú vásárlási modell fizet:

- Számítási erőforrások (a szolgáltatási rétegben + virtuális magok száma és a memória és a hardver generációja mennyisége).
- A típus és adat- és naplófájlok tárterület mérete.
- Biztonsági mentési tárolás (RA-GRS).

> [!IMPORTANT]
> A számítási erőforrások, i/o és adat- és naplófájlok tárolási adatbázisához vagy rugalmas készletéhez díját tartalmazza. Biztonsági másolati tárhely számlázása az egyes adatbázisok. Felügyelt példány díjak kapcsolatos további információkért lásd: [felügyelt példányai](sql-database-managed-instance.md).
> **Régió korlátozások:** A támogatott régiók aktuális listájáért lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Felügyelt példány létrehozása, amely jelenleg nem támogatott régióban [küldjön egy támogatási kérést az Azure Portalon keresztül](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Ha az önálló adatbázis vagy a rugalmas készletet használ fel, több mint 300 dtu-k, a Virtuálismag-alapú vásárlási modell konvertálása előfordulhat, hogy csökkentse a költségeket. A választott API-val vagy az Azure Portallal, leállás nélkül konvertálhatja. Átalakítás azonban nincs szükség, és nem történik meg automatikusan. Ha a DTU-alapú vásárlási modell felel meg a teljesítmény- és üzleti követelményeinek, akkor továbbra is használja azt.

A DTU-alapú vásárlási modell konvertálható a Virtuálismag-alapú vásárlási modell, válasszon a következő szabályok költségcsökkenést eredményezzen az a számítási méret:

- A standard szintű minden 100 dtu-k az általános célú szolgáltatási rétegben legalább 1 virtuális mag szükséges.
- A prémium szintű minden 125 dtu-k az üzleti kritikus szolgáltatási rétegben legalább 1 virtuális mag szükséges.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Egy adatbázis-tranzakciós egységek (DTU) a CPU, memória, olvasási és írási műveletek összesített mérésén jelöli. A DTU-alapú vásárlási modell előre konfigurált kötegek számítási erőforrások olyan készletét kínálja, és belefoglalt tárterület teljesítményszintek különböző alkalmazásteljesítmény. Igény szerint az egyszerűség, rögzített nagyságú befizetések és a egy előre konfigurált csomaggal minden hónapban a DTU-alapú modell bizonyára jobban megfelel az igényeinek lehet.

A DTU-alapú vásárlási modell, választhat a basic, standard és prémium szintű szolgáltatási csomagok esetében is [önálló adatbázisok](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). A DTU-alapú vásárlási modell nem érhető el a [felügyelt példányai](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Adatbázis-tranzakciós egységek (Dtu)

Egy önálló adatbázis egy adott számítási méret belül egy [szolgáltatásszint](sql-database-single-database-scale.md), a Microsoft garantálja, hogy az adatbázis-erőforrások bizonyos szintű (függetlenül a minden más adatbázis az Azure-felhőben). Ez garantálja a kiszámítható szintű teljesítményt biztosít. Az adatbázishoz lefoglalt erőforrások mennyisége számítjuk ki a dtu-k számát, hogy, és a egy csomagolt számítási, tárolási és i/o-erőforrások mértékegysége.

Ezek a források között aránya eredetileg határozza- [online tranzakciófeldolgozás (OLTP) számítási](sql-database-benchmark-overview.md) tipikus valós OLTP-munkaterhelések. Ha a számítási feladat a következő erőforrások mennyisége meghaladja, az átviteli sebességet szabályozva, és eredményez a lassabb teljesítménnyel és időtúllépésekkel kapcsolatban.

A számítási feladatok által használt erőforrások nem érinti az Azure-felhőben más SQL-adatbázisok számára elérhető erőforrások. Hasonlóképpen a más számítási feladatok által használt erőforrások ne befolyásolják az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu-k leghasznosabbak a relatív különböző számítási méretekre és szolgáltatási szintet, az Azure SQL Database-adatbázisok számára lefoglalt erőforrások megértésében. Példa:

- A Dtu megduplázása egy adatbázis számítási méretének növelését-adatbázisnak felel meg az adatbázis számára elérhető erőforrások készletét kétszeresére.
- Prémium szintű szolgáltatási szint P11 adatbázis 1750 Dtu-val biztosít 350 x több DTU számítási teljesítményt, mint egy alapszintű szolgáltatási szintű adatbázis 5 Dtu-val.  

Mélyebb betekintést nyerhet a (DTU) erőforrás-használat, a számítási feladat, használja a [lekérdezés Teljesítményelemzés](sql-database-query-performance.md) való:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtások száma, amely potenciálisan hangolásával a jobb teljesítmény érdekében. Ha például az I/O-igényes lekérdezések előnyös lehet [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) , hogy jobban kihasználja a rendelkezésre álló memória, egy adott szolgáltatásszintet, a számítási méret.
- Megtekintheti az szöveg- és az erőforrások használati előzményeit, a lekérdezés a részletek feltárásához.
- Teljesítmény-finomhangolási ajánlásait által végzett műveleteket megjelenítő eléréséhez [az SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>A rugalmas database transaction unitok (edtu-k)

SQL-adatbázisok, amelyek mindig elérhető, hanem dedikált erőforráskészletek (dtu-k), előfordulhat, hogy nem mindig lehet szükséges, adja meg, mint elhelyezhet ezeknek az adatbázisoknak az egy [rugalmas készlet](sql-database-elastic-pool.md). A rugalmas készletben található adatbázisok egy Azure SQL Database-kiszolgálón, és megoszthatja az erőforrásokat.

A rugalmas készletek megosztott erőforrásainak rugalmas database transaction unitok (edtu-k) vannak megadva. Rugalmas készletek egyszerű és költséghatékony megoldást, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul változik a használati minták több adatbázisok teljesítménybeli céljainak kezelésére nyújtanak. Rugalmas készlet garantálja, hogy minden erőforrás nem tudják használni a készletben egy adatbázis biztosítva, hogy a készletben lévő minden egyes adatbázishoz mindig rendelkezik egy minimálisan szükséges erőforrások rendelkezésre.

A készlethez adott számú edtu-k rögzített áron. A rugalmas készlet különálló adatbázisok az automatikus méretezés a konfigurált határok is. Egy adatbázis egy nagyobb terhelés alatt további edtu-t, igény szerint fog felhasználni. Világosabb terhelések adatbázisoknak kevesebb edtu-k. Semmilyen terhelést az adatbázisok edtu-k nem használnak fel. Erőforrások kiépítése a teljes készlet számára, nem pedig adatbázisonként, mert a rugalmas készletek a kezelési feladatok egyszerűsítéséhez, és adja meg a készlet kiszámítható költségekkel.

Leállnának, és nincs hatással a készletben található adatbázisok a létező készletekhez további edtu-k is hozzáadhat. Hasonlóképpen ha már nincs szüksége Edtu, távolítsa el őket a meglévő készletekből bármikor. Akkor is is adatbázisok adatbázisok hozzáadhatók vagy kivonhatók készletből bármikor. Edtu-kat más adatbázisok számára lefoglalni egy adatbázist egy nagy terhelés alatt használható edtu-k számának korlátozásához. Ha az adatbázis konzisztens módon underuses erőforrásokat, kiveheti a készlet, és azt konfigurálja egy önálló adatbázist egy előre meghatározott szükséges erőforrásokat.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>A munkaterhelés szerint szükséges dtu-k számának meghatározása

Ha szeretne áttelepíteni egy meglévő helyszíni vagy SQL Server virtuális gépeken futó számítási feladatokat az Azure SQL Database használata a [DTU-kalkulátor](https://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges dtu-k számát. Egy meglévő Azure SQL Database-munkaterhelések esetében használjon [lekérdezés Teljesítményelemzés](sql-database-query-performance.md) az adatbázis-erőforrás-használat (dtu-k) és mélyebb betekintést a számítási feladatok optimalizálásához. A [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dinamikus felügyeleti nézet (DMV) megtekintheti az erőforrás-használat az elmúlt egy óra. A [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) katalógusnézet erőforrás-használat az elmúlt 14 napra vonatkozó, de átlagosan öt perces átlagolással jeleníti meg.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Számítási feladatok, amelyek rugalmas készletek az erőforrások

Készletek olyan olyan adatbázisok esetén egy alacsony erőforrás-használat átlagos és viszonylag rendszertelen kiugró kihasználtság jellemzi. SQL Database automatikusan kiértékeli az erőforrások használati előzményeit, egy meglévő SQL Database-kiszolgálón lévő adatbázisokhoz, és javasolja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [során kell végiggondolni egy SQL Database rugalmas készlet?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Kell az alkalmazásom átalakítása Virtuálismag-alapú szolgáltatásréteg DTU-alapú szolgáltatásréteg?

Nem. Az alkalmazás kapcsolat nélküli üzemmódra nincs szükségünk. Az új szolgáltatási szintekről kínálnak a szabványostól, a prémium szintű szolgáltatási rétegben, és fordítva frissítésének adatbázisok meglévő folyamata hasonló egyszerű online konverziós módját. Ez a konverzió elkezdheti az Azure portal, PowerShell, az Azure CLI, T-SQL vagy a REST API használatával. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Átválthatók egy adatbázist a Virtuálismag-alapú vásárlási modell a szolgáltatásszint, a szolgáltatási szinten a DTU-alapú vásárlási modell?

Igen, egyszerűen átválthat az adatbázis bármely támogatott teljesítmény célja az Azure portal, PowerShell, az Azure CLI, T-SQL vagy a REST API használatával. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="next-steps"></a>További lépések

- A Virtuálismag-alapú vásárlási modell kapcsolatos további információkért lásd: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- A DTU-alapú vásárlási modell kapcsolatos további információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
