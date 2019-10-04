---
title: Azure SQL Database beszerzési modellek | Microsoft Docs
description: Ismerje meg a Azure SQL Database számára elérhető vásárlási modelleket.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 98d257c28ab5ff2cf902c0b8205ac8918ccf4d45
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567005"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>A virtuális mag és a DTU beszerzési modelljei közül választhat

Azure SQL Database segítségével egyszerűen vásárolhat egy teljes körűen felügyelt, szolgáltatásként nyújtott platformot, amely megfelel a teljesítményének és a költséghatékonyságnak. A Azure SQL Database kiválasztott üzemi modelltől függően kiválaszthatja az Önnek megfelelő vásárlási modellt:

- [Virtual Core (virtuális mag)-alapú beszerzési modell](sql-database-service-tiers-vcore.md) (ajánlott). Ez a vásárlási modell a kiépített számítási szintek és a kiszolgáló nélküli (előzetes verzió) számítási szintek közötti választást biztosítja. A kiépített számítási szinten kiválaszthatja a számítási erőforrások pontos mennyiségét, amelyek mindig kiépítve vannak a munkaterheléshez. A kiszolgáló nélküli számítási szinten megadhatja a számítási erőforrások automatikus skálázását egy konfigurálható számítási tartományon. Ezzel a számítási szinten automatikusan szüneteltetheti és folytathatja az adatbázist a munkaterhelés-tevékenység alapján. A virtuális mag egység ára (egységenként) alacsonyabb a kiépített számítási szinten, mint a kiszolgáló nélküli számítási szinten.
- [Adatbázis-tranzakciós egység (DTU) alapú beszerzési modell](sql-database-service-tiers-dtu.md). Ez a beszerzési modell a közös számítási feladatokhoz kiegyensúlyozott, kötegelt számítási és tárolási csomagokat biztosít.

Különböző beszerzési modellek érhetők el különböző Azure SQL Database üzembe helyezési modellekhez:

- [Azure SQL Database](sql-database-technical-overview.md) az [önálló adatbázis](sql-database-single-databases-manage.md) és a [rugalmas készlet](sql-database-elastic-pool.md) üzembe helyezési lehetőségei a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)is biztosítanak.
- Azure SQL Database a [felügyelt példány](sql-database-managed-instance.md) központi telepítési lehetősége csak a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)kínálja.
- A [nagy kapacitású szolgáltatási réteg](sql-database-service-tier-hyperscale.md) a [virtuális mag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)használó önálló adatbázisokhoz érhető el.

A következő táblázat és diagram hasonlítja össze a virtuális mag-alapú és a DTU-alapú vásárlási modelleket:

|**Vásárlási modell**|**Leírás**|**Legjobb a következőhöz:**|
|---|---|---|
|DTU-alapú modell|Ez a modell a számítási, tárolási és I/O-erőforrások kötegelt mérőszámán alapul. A számítási méretek az önálló adatbázisok és a rugalmas készletekhez tartozó rugalmas adatbázis-tranzakciós egységek (Edtu) DTU vannak kifejezve. További információ a DTU és a Edtu: [Mi a DTU és a edtu?](sql-database-purchase-models.md#dtu-based-purchasing-model).|A legjobb az egyszerű, előre konfigurált erőforrás-beállításokkal rendelkező ügyfelek számára.|
|vCore-alapú modell|Ez a modell lehetővé teszi a számítási és tárolási erőforrások egymástól független kiválasztását. A virtuális mag-alapú vásárlási modell azt is lehetővé teszi, hogy a megtakarítások megszerzéséhez a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is használja.|A legjobb megoldás a rugalmasságot, a vezérlést és az átláthatóságot biztosító ügyfelek számára.|
||||  

![díjszabási modell összehasonlítása](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Számítási költségek

### <a name="provisioned-compute-costs"></a>Kiépített számítási költségek

A kiépített számítási szinten a számítási díj az alkalmazás számára kiépített teljes számítási kapacitást tükrözi.

Az üzleti szempontból kritikus szolgáltatási szinten a rendszer automatikusan legalább 3 replikát foglal le. A számítási erőforrások további kiosztásának megjelenítéséhez a virtuális mag-alapú vásárlási modell ára körülbelül 2,7 x, ami az üzleti szempontból kritikus szolgáltatási szinten meghaladja az általános célú szolgáltatási szintet. Hasonlóképpen, az üzleti szempontból kritikus szolgáltatási szinten az egyes GB-nál nagyobb tárolási díj az SSD-tároló magas I/O-és kis késését tükrözi.

A biztonsági mentési tár díja ugyanaz, mint az üzleti szempontból kritikus szolgáltatási szint és az általános célú szolgáltatási szint esetében, mivel mindkét szint szabványos tárterületet használ.

### <a name="serverless-compute-costs"></a>Kiszolgáló nélküli számítási költségek

A számítási kapacitás meghatározása és a költségek kiszámításának leírása a kiszolgáló nélküli számítási szinten: [SQL Database kiszolgáló nélküli (előzetes verzió)](sql-database-serverless.md).

## <a name="storage-costs"></a>Tárolási költségek

A különböző típusú tárolók számlázása másképp történik. Az adattárolás esetében a kiosztott tárterületért kell fizetnie, amely a kiválasztott adatbázis vagy készlet maximális mérete alapján történik. A díj nem változik, hacsak nem csökkenti vagy nem emeli a maximális értéket. A biztonsági mentési tár a példányának automatizált biztonsági másolatához van társítva, és dinamikusan van lefoglalva. A biztonsági mentés megőrzési időtartamának növelése növeli a példány által felhasznált biztonsági mentési tárterületet.

Alapértelmezés szerint az adatbázisok 7 napos automatikus biztonsági mentését egy olvasási hozzáférésű geo-redundáns tárolási (RA-GRS) standard blob Storage-fiókba másolja a rendszer. Ezt a tárolót a heti teljes biztonsági mentések, a napi különbözeti biztonsági másolatok, valamint a tranzakciós naplók biztonsági másolatai használják, amelyek 5 percenként másolódnak át. A tranzakciós naplók mérete az adatbázis változásának mennyiségétől függ. Az adatbázis méretének 100%-ának megfelelő minimális tárterület külön díj nélkül elérhető. A biztonsági mentési tár további felhasználásának díja GB/hó.

A tárolási díjszabással kapcsolatos további információkért tekintse [](https://azure.microsoft.com/pricing/details/sql-database/single/) meg az árképzést ismertető oldalt.

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A virtuális mag (virtuális mag) a logikai CPU-t jelöli, és lehetőséget nyújt a hardver és a hardver fizikai jellemzőinek (például a magok, a memória és a tárterület méretének) a kiválasztására. A virtuális mag-alapú vásárlási modell rugalmasságot, irányítást, átláthatóságot biztosít az egyes erőforrások fogyasztása terén, és egy egyszerű módszert kínál a helyszíni munkaterhelés-követelmények felhőbe való lefordítására. Ez a modell lehetővé teszi a számítási, a memória-és a tárolási erőforrások kiválasztását a munkaterhelés igényei alapján.

A virtuális mag-alapú vásárlási modellben választhat az [általános célú](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) és az [üzleti szempontból kritikus](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) szolgáltatási szintek közül az [önálló adatbázisok](sql-database-single-database-scale.md), a [rugalmas készletek](sql-database-elastic-pool.md)és a [felügyelt példányok](sql-database-managed-instance.md)között. Önálló adatbázisok esetén a [nagy kapacitású szolgáltatási szintet](sql-database-service-tier-hyperscale.md)is kiválaszthatja.

A virtuális mag-alapú vásárlási modell lehetővé teszi a számítási és tárolási erőforrások egymástól független kiválasztását, a helyszíni teljesítmény egyeztetését és az árak optimalizálását. A virtuális mag-alapú vásárlási modellben a következőket kell fizetnie:

- Számítási erőforrások (a szolgáltatási szintek és a virtuális mag száma, valamint a memória mennyisége és a hardverek generálása).
- Az adatmennyiség és a naplózási tároló típusa és mennyisége.
- Biztonsági mentési tár (RA-GRS).

> [!IMPORTANT]
> A számítási erőforrások, az I/O-műveletek, valamint az adatok és a naplók tárolása adatbázis vagy rugalmas készlet alapján történik. A biztonsági mentési tárterületet minden adatbázis alapján számítjuk fel. A felügyelt példányok díjaival kapcsolatos további [](sql-database-managed-instance.md)információkért lásd: felügyelt példányok.
> **Régióra vonatkozó korlátozások:** A támogatott régiók aktuális listájáért lásd: [régiók által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Ha felügyelt példányt szeretne létrehozni olyan régióban, amely jelenleg nem támogatott, [küldjön egy támogatási kérést a Azure Portalon keresztül](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Ha az önálló adatbázis vagy a rugalmas készlet több mint 300 DTU használ, akkor a virtuális mag-alapú vásárlási modellre való áttérés csökkentheti a költségeket. A konverziót a választható API-k használatával vagy a Azure Portal használatával végezheti el állásidő nélkül. Azonban a konverzió nem szükséges, és nem történik meg automatikusan. Ha a DTU-alapú vásárlási modell megfelel a teljesítményre és az üzleti követelményekre, akkor továbbra is használja.

A DTU-alapú vásárlási modellről a virtuális mag-alapú vásárlási modellre való áttéréshez válassza ki a számítási méretet a következő hüvelykujj-szabályok használatával:

- A standard szint 100 DTU legalább 1 virtuális mag kell rendelkeznie az általános célú szolgáltatási szinten.
- A prémium szintű csomag 125 DTU legalább 1 virtuális mag kell lennie az üzleti szempontból kritikus szolgáltatási szinten.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Az adatbázis-tranzakciós egység (DTU) a processzor, a memória, az olvasás és az írás kevert mértékét jelöli. A DTU-alapú vásárlási modell a számítási erőforrások előre konfigurált kötegeit és az alkalmazások különböző szintjeinek megadását is biztosítja. Ha az előre konfigurált kötegek és a havonta rögzített kifizetések egyszerűségét részesíti előnyben, a DTU-alapú modell megfelelőbb lehet az Ön igényeinek megfelelően.

A DTU-alapú vásárlási modellben választhat az alapszintű, a standard és a prémium szolgáltatási szint közül mind az [önálló adatbázisok](sql-database-single-database-scale.md) , mind a [rugalmas készletek](sql-database-elastic-pool.md)esetében. A DTU-alapú beszerzési modell nem érhető el [](sql-database-managed-instance.md)a felügyelt példányok számára.

### <a name="database-transaction-units-dtus"></a>Adatbázis-tranzakciós egységek (DTU)

A Microsoft a [szolgáltatási](sql-database-single-database-scale.md)szinten meghatározott számítási méretekben egyetlen adatbázis esetében garantálja az adott adatbázishoz tartozó erőforrások bizonyos szintjét (amely független az Azure-felhőben található többi adatbázistól). Ez a garancia kiszámítható szintű teljesítményt nyújt. Az adatbázishoz lefoglalt erőforrások mennyisége DTU számú, és a számítási, tárolási és I/O-erőforrások kötegelt mérőszáma.

Az ilyen erőforrások arányát eredetileg egy [online tranzakció-feldolgozási (OLTP) teljesítményteszt-számítási](sql-database-benchmark-overview.md) feladat határozza meg, amely a valós OLTP munkaterhelésekre jellemző. Ha a munkaterhelés meghaladja az ilyen erőforrások mennyiségét, az átviteli sebesség szabályozása megtörténik, ami lassabb teljesítményt és időtúllépést eredményez.

A munkaterhelés által használt erőforrások nem érintik az Azure-felhőben más SQL-adatbázisok számára elérhető erőforrásokat. Hasonlóképpen, a más munkaterhelések által használt erőforrások nem érintik az SQL-adatbázis számára elérhető erőforrásokat.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

A DTU leghasznosabb az Azure SQL Database-adatbázisok számára a különböző számítási méreteken és szolgáltatási szinteknél lefoglalt relatív erőforrások megismerése. Példa:

- A DTU megkettőzése az adatbázis számítási méretének növelésével egyenlő az adatbázis számára elérhető erőforrások készletének megkettőzésével.
- A prémium szintű szolgáltatási szint P11-adatbázisa és a 1750 DTU 350x több DTU számítási teljesítményt nyújt, mint az alapszintű szolgáltatási szint adatbázisa 5 DTU.  

A számítási feladatok erőforrás-(DTU-) felhasználásának mélyebb megismeréséhez használja a [lekérdezési teljesítmény](sql-database-query-performance.md) elemzését a következőre:

- A processzor/időtartam/végrehajtás száma alapján azonosíthatja a leggyakoribb lekérdezéseket, amelyek a jobb teljesítmény érdekében megadhatók. Az I/O-igényes lekérdezések például hasznosak lehetnek a memórián belüli [optimalizálási technikákban](sql-database-in-memory.md) , hogy a rendelkezésre álló memória jobban használható legyen bizonyos szolgáltatási szinten és számítási méretekben.
- A lekérdezés részleteinek részletezése a szöveg és az erőforrás-használat előzményeinek megtekintéséhez.
- A [SQL Database Advisor](sql-database-advisor.md)által végrehajtott műveleteket bemutató teljesítmény-hangolási javaslatok elérése.

### <a name="elastic-database-transaction-units-edtus"></a>Rugalmas adatbázis-tranzakciós egységek (Edtu)

Olyan SQL-adatbázisok esetén, amelyek mindig elérhetők, és nem kell olyan dedikált erőforrásokat (DTU) biztosítani, amelyek esetleg nem mindig szükségesek, az adatbázisok [rugalmas készletbe](sql-database-elastic-pool.md)helyezhetők. A rugalmas készletben lévő adatbázisok egyetlen Azure SQL Database-kiszolgálón találhatók, és megoszthatják az erőforrások készletét.

A rugalmas készletben lévő megosztott erőforrásokat rugalmas adatbázis-tranzakciós egységekkel (Edtu) mérjük. A rugalmas készletek egyszerű, költséghatékony megoldást biztosítanak a teljesítménybeli célok kezelésére több, széles körben változó és kiszámíthatatlan használati mintázattal rendelkező adatbázis esetében. A rugalmas készlet garantálja, hogy az összes erőforrást nem lehet felhasználni a készlet egyik adatbázisa, miközben gondoskodik arról, hogy a készletben lévő összes adatbázishoz mindig rendelkezésre álljon legalább egy szükséges erőforrás.

Egy készlethez megadott számú Edtu van megadva. A rugalmas készletben az egyes adatbázisok a beállított határokon belül is méretezhetők. A nehezebb terhelésű adatbázisok több Edtu használnak az igények kielégítése érdekében. Az öngyújtós terhelés alatt álló adatbázisok kevesebb Edtu fognak használni. A terhelés nélküli adatbázisok nem fognak Edtu használni. Mivel az erőforrások a teljes készlethez vannak kiépítve, nem pedig adatbázis helyett, a rugalmas készletek egyszerűbbé teszik a felügyeleti feladatokat, és kiszámítható költségvetést biztosítanak a készlet számára.

Hozzáadhat további Edtu az adatbázis-leállás nélküli meglévő készletekhez, és nem befolyásolhatja a készletben lévő adatbázisokat. Hasonlóképpen, ha már nincs szüksége további Edtu, távolítsa el őket egy meglévő készletből. Az adatbázisokat a készletből bármikor hozzáadhatók vagy kivonhatók. A Edtu más adatbázisokhoz való lefoglalásához korlátozza az adatbázisok nagy terhelés alatt használható Edtu számát. Ha egy adatbázis következetesen erőforrásokat használ, helyezze át a készletből, és konfigurálja egyetlen adatbázisként a szükséges erőforrások kiszámítható mennyiségével.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>A munkaterhelés által igényelt DTU számának meghatározása

Ha meglévő helyszíni vagy SQL Server virtuális gépek munkaterhelését szeretné áttelepíteni Azure SQL Databasere, a [DTU-kalkulátor](https://dtucalculator.azurewebsites.net/) használatával közelítse meg a szükséges DTU számát. Meglévő Azure SQL Database számítási feladatokhoz használja a [lekérdezési teljesítménnyel](sql-database-query-performance.md) kapcsolatos elemzéseket az adatbázis-erőforrás-felhasználás (DTU) megismeréséhez, és mélyebb elemzéseket kaphat a számítási feladatok optimalizálásához. A [sys. DM _db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dinamikus felügyeleti nézet (DMV) lehetővé teszi az elmúlt órában az erőforrás-felhasználás megtekintését. A [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) Catalog nézet az elmúlt 14 napban az erőforrás-felhasználást jeleníti meg, de az átlagosan öt percenkénti átlagban jelenik meg.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Rugalmas készletből kihasználható munkaterhelések

A készletek kiválóan alkalmasak az alacsony erőforrás-kihasználtságú és viszonylag ritka kihasználtsági tüskéket tartalmazó adatbázisok számára. SQL Database automatikusan kiértékeli az adatbázisok korábbi erőforrás-használatát egy meglévő SQL Database-kiszolgálón, és a Azure Portal a megfelelő készlet konfigurációját javasolja. További információ: Mikor érdemes [SQL Database rugalmas készletet figyelembe venni?](sql-database-elastic-pool.md)

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Szükség van-e az alkalmazás offline állapotba való átalakítására egy DTU-alapú szolgáltatási rétegről egy virtuális mag-alapú szolgáltatási rétegre?

Nem. Nem kell offline állapotba hoznia az alkalmazást. Az új szolgáltatási rétegek egy egyszerű online átalakítási módszert kínálnak, amely hasonló a standard és a prémium szolgáltatási szint adatbázisainak frissítéséhez, és fordítva. A konverziót a Azure Portal, a PowerShell, az Azure CLI, a T-SQL vagy a REST API használatával indíthatja el. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Átválthatok egy adatbázist a virtuális mag-alapú vásárlási modellben egy szolgáltatási rétegre a DTU-alapú vásárlási modellben?

Igen, a Azure Portal, a PowerShell, az Azure CLI, a T-SQL vagy a REST API használatával könnyedén átalakíthatja az adatbázist bármely támogatott teljesítménybeli célkitűzésre. Lásd: [önálló adatbázisok kezelése](sql-database-single-database-scale.md) és [rugalmas készletek kezelése](sql-database-elastic-pool.md).

## <a name="next-steps"></a>További lépések

- A virtuális mag-alapú vásárlási modellel kapcsolatos további információkért lásd: [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).
- A DTU-alapú vásárlási modellel kapcsolatos további információkért lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
