---
title: Az Azure SQL Database rendkívüli – áttekintés |} A Microsoft Docs
description: Ez a cikk ismerteti a nagy kapacitású szolgáltatási szinten a Virtuálismag-alapú vásárlási modell az Azure SQL Database, és elmagyarázza, hogyan Ez különbözik az általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről.
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
ms.date: 10/04/2018
ms.openlocfilehash: 9cba912e99591f175eeff564e8d83e794fb6ad97
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832266"
---
# <a name="hyperscale-service-tier-preview"></a>Nagy kapacitású szolgáltatási szint (előzetes verzió)

A nagy kapacitású szolgáltatás az Azure SQL Database szintje a legújabb szolgáltatási szinten a Virtuálismag-alapú vásárlási modell. Szolgáltatási réteg egy rugalmasan méretezhető tárolási és számítási teljesítményszint, amely az Azure-architektúra horizontális felskálázása a tárolási és számítási erőforrások egy Azure SQL Database jelentősen kívül eső érhető el az általános célú és üzleti Kritikus szolgáltatási szinteken.

> [!IMPORTANT]
> Nagy kapacitású szolgáltatási szint jelenleg nyilvános előzetes verzióban érhető el, és elérhető a korlátozott Azure-régióban. A régió teljes listát lásd: [nagy kapacitású szolgáltatási szinten elérhető régiók](#available-regions)
> [!NOTE]
> A Virtuálismag-alapú vásárlási modell az általános célú és az üzletileg kritikus szolgáltatási csomagokra a részletekért lásd: [általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről](sql-database-service-tiers-general-purpose-business-critical.md). A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-service-tiers.md).
> [!IMPORTANT]
> Nagy kapacitású szolgáltatási szint jelenleg nyilvános előzetes verzióban érhető el. Nem ajánlott semmilyen éles számítási feladatok futtatása a nagy kapacitású adatbázisokban még. Nagy kapacitású adatbázis egyéb szolgáltatási csomagokra nem frissíthető. Tesztelési célra ajánlott másolatot készít az aktuális adatbázisban, és frissíti a példányt nagy kapacitású szolgáltatásszinthez.

## <a name="what-are-the-capabilities-of-the-hyperscale-service-tier"></a>Mik azok a nagy kapacitású szolgáltatásszint képességeit

A nagy kapacitású szolgáltatási rétegben az Azure SQL Database az alábbi további képességeket biztosítja:

- Egy 100 TB-nyi adatbázis mérete legfeljebb támogatása
- Így szinte azonnali biztonsági mentéseket (az Azure Blob storage szolgáltatásban tárolt pillanatképeket alapján) adatbázis-számítási i/o-hatása méretétől függetlenül
- A perc helyett órák vagy napok adatbázis visszaállítás (pillanatképeket alapján) gyors (nem a adatművelet mérete)
- Magasabb szintű általános teljesítményt, nagyobb log átviteli sebességet és a tranzakció véglegesítése gyorsabb függetlenül az adatmennyiség miatt
- Gyors horizontális felskálázás-– telepíthet egy vagy több csak olvasható csomópont a olvasási számítási feladatok kiszervezése és használatra, a ritkáról gyakori elérésű standbys
- Gyors horizontális felskálázás –, is, állandó időben, vertikális felskálázása a számítási erőforrásokat, amelyek nagy terheléseket, és ha szükséges és majd csökkentheti a számítási erőforrások ismét szükség van.

A nagy kapacitású szolgáltatásszint számos, a gyakorlati korlátok hagyományosan látható a felhőalapú adatbázisok eltávolítja. Ahol legtöbb más adatbázisok korlátozza az egyetlen csomópont, rendelkezésre álló erőforrások a nagy kapacitású szolgáltatásszinten lévő adatbázisok nincs ilyen korlátokkal rendelkeznek. A rugalmas tárolási architektúrával a tárolás igény szerint nő. Valójában a nagy kapacitású adatbázisok egy meghatározott maximális méretű hozhatók létre. Nagy kapacitású adatbázis növekszik – igény szerint, és csak a kapacitás használata díjköteles. Olvasási-igényes számítási feladatokhoz a nagy kapacitású szolgáltatás kínál a gyors, horizontális felskálázás tehermentesítési olvasási számítási feladatok igény szerint további olvasási replikák kiépítésével.

Ezenkívül az idő szükséges az adatbázis biztonsági mentését vagy vertikális, vagy le nincs kötve az adatbázisban található adatok mennyisége. Nagy kapacitású adatbázisok gyakorlatilag azonnal készíthető. Is skálázhatja egy adatbázis több terabájtnyi tíz felfelé vagy lefelé percek alatt. Ez a funkció Önnek nem kell folyamatban bevitel által a kezdeti konfigurációs beállításokkal kapcsolatos elvárásainak.

A nagy kapacitású szolgáltatási szinthez tartozó számítási-méretekkel kapcsolatos további információkért lásd: [szolgáltatási szint jellemzők](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kinek érdemes megfontolnia a nagy kapacitású szolgáltatásszintet

A szolgáltatási rétegben elsősorban nagy méretű adatbázisokhoz rendelkező ügyfelek nagy kapacitású vagy a helyszíni és korszerűsítheti az alkalmazásaik vagy ügyfelek esetében, akik már a felhőben, és az adatbázis maximális mérete korlátozza a felhőbe helyezi szeretné korlátozások (1 – 4 TB). Az ügyfelek esetében, akik nagy teljesítményű és nagy mértékű skálázhatóságot, a tárolási és számítási is szolgál.

A nagy kapacitású szolgáltatásszint támogatja az összes SQL Server számítási feladatok, de OLTP elsősorban van optimalizálva. A nagy kapacitású szolgáltatási szinten is támogatja a hibrid és analitikai (adatpiac) számítási feladatokat.

> [!IMPORTANT]
> Rugalmas készletek nem támogatják a nagy kapacitású szolgáltatási rétegben.

## <a name="understand-hyperscale-pricing"></a>Nagy kapacitású díjszabásával

Nagy kapacitású szolgáltatási szinten érhető el csak [Virtuálismag-modell](sql-database-service-tiers-vcore.md). Az új architektúrája igazodni, a díjszabási modell kissé eltérő, az általános célú és az üzletileg kritikus szolgáltatási szinteken:

- **COMPUTE**:

  A rendkívül nagy számítási egységár replikánként van. A [Azure hibrid Benifit](https://azure.microsoft.com/pricing/hybrid-benefit/) ár alkalmazott olvassa el automatikusan a méretezési csoport replikáit. Nyilvános előzetes verzióban alapértelmezés szerint hozunk létre két replika nagy kapacitású adatbázisonként.

- **Tárolási**:

  Adja meg az adatbázisonkénti maximális mérete egy nagy kapacitású adatbázis konfigurálása során nem kell. A rugalmas méretezés szintjén az adatbázis tárolási díjai a tényleges használat alapján lesznek kiszámlázva. A tárhely dinamikusan van lefoglalva 5 GB és 100 TB között, 1 GB-os növekményekkel.  

Nagy kapacitású díjszabással kapcsolatos további információkért lásd: [Azure SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="architecture-distributing-functions-to-isolate-capabilities"></a>Referenciaarchitektúra: Terjesztése funkciók képességek elkülönítése

Központosított minden egy helyen/folyamat (tehát még ma az éles környezetben nevű elosztott adatbázisok egy monolitikus motor több példányával rendelkezik) a data felügyeleti funkciókat a hagyományos adatbázismotorokat ellentétben egy nagy kapacitású adatbázis elválasztó a lekérdezés-feldolgozás motor, ahol különböző adatmotort szemantikáját tér el, a hosszú távú tárolás és tartósságot biztosítanak az adatok összetevőket. Ezzel a módszerrel a tárolási kapacitás zökkenőmentesen kiterjeszthető szükség szerint (kezdeti cél a 100 TB-ot). Csak olvasható replikák megoszthatja a számítási ugyanazokat az összetevőket, így az adatok másolata nem szükséges új olvasható replika üzembe helyezését.

A következő ábra szemlélteti a különböző típusú csomópont egy nagy kapacitású adatbázisban:

![architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

Nagy kapacitású adatbázis a következő különböző típusú csomópontokat tartalmazza:

### <a name="compute-node"></a>Számítási csomópont

A számítási csomóponton, ahol a relációs motorban él, így a nyelvi elemei, lekérdezés-feldolgozás és így tovább, akkor fordulhat elő. Ezek keresztül történik egy nagy kapacitású adatbázis minden felhasználói interakció számítási csomópontokon. Számítási csomópontok (feliratú RBPEX - rugalmas pufferkészlet-bővítmény a fenti ábrán) SSD-alapú gyorsítótárakat kell hálózati számának csökkentése érdekében adatváltások beolvasni az adatokat egy lapján szükséges. Van egy elsődleges számítási csomóponton, amelyen az olvasási és írási számítási feladatok és a tranzakciók feldolgozása. Nincsenek egy vagy több, feladatátvételi célból meleg készenléti csomópontok szerepét, valamint tehermentesítésének, olvassa el a számítási feladatok (Ha ez a funkció be van szükség) csak olvasható számítási csomópontok szerepét másodlagos számítási csomópontok.

### <a name="page-server-node"></a>Kiszolgáló-csomópont lap

Lap kiszolgálók azok a rendszerek jelölő horizontálisan felskálázott motorjába.  Minden lap kiszolgáló felelős az oldalak, az adatbázis egy részét.  Minden egyes lap vezérlik névlegesen, 1 terabájt adat. Nincs adat (kívül őrzi meg a redundancia és a rendelkezésre állási replikák) egynél több lapot kiszolgálón van osztva. A feladat oldal kiszolgáló, hogy, a számítási csomópontokra, igény szerinti használhatók adatbázis oldalaihoz, valamint megőrzése a tranzakciók adatainak frissítéséhez frissíteni. Lap kiszolgálók bármelyikük által játszott rekordok naplózása a naplózási szolgáltatás. Lap kiszolgálók is fenn az SSD-alapú gyorsítótárak a teljesítmény növelése érdekében. Lapok hosszú távú tárolása a további megbízhatóság másolatok az Azure Storage-ban.

### <a name="log-service-node"></a>Naplózási szolgáltatás csomópont

A naplózási szolgáltatás csomópont fogadja el az elsődleges számítási csomópontból rekordok naplózása, továbbra is fennáll azokat egy tartós gyorsítótárban, és továbbítja a napló rögzíti a többi, a számítási csomópontok (tehát frissíthetnek gyorsítótárukban) a megfelelő lapon kiszolgáló (ko), valamint, hogy az adatok lehetnek a frissített t itt. Ezzel a módszerrel az elsődleges számítási csomópontból adatmódosítás a naplózási szolgáltatás az összes másodlagos számítási csomópontok és lap kiszolgálók keresztül továbbítja. Végül a naplózási bejegyzések akkor lesznek leküldve a hosszú távú tárolás az Azure Storage, amely egy végtelen tárházaként. Ez a mechanizmus eltávolítja a gyakori rendszernapló csonkolása szükségességét. A naplózási szolgáltatás is rendelkezik a helyi gyorsítótár hozzáférési felgyorsítása érdekében.

### <a name="azure-storage-node"></a>Az Azure storage-csomópont

Az Azure storage csomópontja lap kiszolgálók adatainak végső célja. Ez a tároló biztonsági mentés céljából, valamint az Azure-régiók közötti replikáció meghajtóbetűjeleket szolgál. Biztonsági másolatok adatfájlok pillanatképek állnak. Állítsa vissza a pillanatképek a gyors művelet, és adatokat bármely időpontra állíthatók időben.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Biztonsági másolatok fájlpillanatkép-alap és és az így szinte azonnali. Tárolási és számítási elkülönítésének lehetővé leküldése a biztonsági mentési vagy visszaállítási művelet le a tárolási rétegben az elsődleges számítási csomópont feldolgozási terhelésének csökkentése érdekében. Ennek eredményeképpen az egy nagyméretű adatbázis biztonsági mentését funkciója nem érinti az elsődleges számítási csomópont teljesítményét. Ehhez hasonlóan visszaállítások kell elvégeznie a fájl pillanatképének másolása és mint ilyen nem egy adatművelet méretét. A visszaállítás ugyanazon a tárfiókon belül a visszaállítási művelet gyors.

## <a name="scale-and-performance-advantages"></a>Méretezés és teljesítmény előnyei

Rövid idő alatt működésbe felfelé és lefelé csak olvasható számítási csomópontot lehetővé teszi a nagy kapacitású architektúra lehetővé teszi, hogy jelentős olvassa el a méretezési képességeit, és a további írási kérelmek számára elsődleges számítási csomópontot is felszabadítható. Ezenkívül a számítási csomópontok méretezhetők felfelé és lefelé gyorsan miatt a megosztott tároló-architektúra, a nagy kapacitású architektúra.

## <a name="available-regions"></a>Elérhető régiók

Nagy kapacitású szolgáltatási szint jelenleg nyilvános előzetes verzióban érhető el, a következő Azure-régióban érhető el: EastUS1, EastUS2, WestUS2, CentralUS, NorthCentralUS, WestEurope, NorthEurope, UKWest, Kelet-Ausztrália, Délkelet-Ausztrália, SouthEastAsia, JapanEast, KoreaCentral

## <a name="next-steps"></a>További lépések

- További információ a szolgáltatási szintekről: [szolgáltatásszintek](sql-database-service-tiers.md)
- Lásd: [erőforrás áttekintése korlátozza egy logikai kiszolgálón](sql-database-resource-limits-logical-server.md) kapcsolatos korlátozásokat a kiszolgálók és az előfizetés szintjén.
- Beszerzési modell korlátok egy önálló adatbázis, lásd: [Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis](sql-database-vcore-resource-limits-single-databases.md).
- Az a funkciók és összehasonlító listában, lásd: [általános SQL-szolgáltatások](sql-database-features.md).
