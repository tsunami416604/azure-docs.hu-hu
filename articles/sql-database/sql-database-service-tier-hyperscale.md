---
title: Az Azure SQL Database rendkívüli – áttekintés |} A Microsoft Docs
description: Ez a cikk ismerteti a nagy kapacitású szolgáltatási szinten a Virtuálismag-alapú vásárlási modell az Azure SQL Database, és elmagyarázza, hogyan Ez különbözik az általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: a953af3d9cd5a6748b79465a59b4a4284e58714c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070131"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Akár 100 TB kapacitású szolgáltatási szintjei

Az Azure SQL Database az SQL Server adatbázismotor architektúra, amely 99,99 %-os rendelkezésre állását, még akkor is, az infrastruktúra-hibák esetekben biztosítása érdekében a felhőalapú környezet módosul alapul. Nincsenek az Azure SQL Database által használt három architekturális modellek:
- Általános célú/Standard 
-  Rugalmas skálázás
-  Üzleti kritikus/prémium

A nagy kapacitású szolgáltatás az Azure SQL Database szintje a legújabb szolgáltatási szinten a Virtuálismag-alapú vásárlási modell. Szolgáltatási réteg egy rugalmasan méretezhető tárolási és számítási teljesítményszint, amely az Azure-architektúra horizontális felskálázása a tárolási és számítási erőforrások egy Azure SQL Database jelentősen kívül eső érhető el az általános célú és üzleti Kritikus szolgáltatási szinteken.

> 
> [!NOTE]
> A Virtuálismag-alapú vásárlási modell az általános célú és az üzletileg kritikus szolgáltatási csomagokra a részletekért lásd: [általános célú](sql-database-service-tier-general-purpose.md) és [üzletileg kritikus fontosságú](sql-database-service-tier-business-critical.md) szolgáltatáshoz. A Virtuálismag-alapú vásárlási modell a DTU-alapú vásárlási modell összehasonlításáért lásd: [vásárlási modellek és az erőforrások az Azure SQL Database](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Mik azok a nagy kapacitású képességeket

A nagy kapacitású szolgáltatási rétegben az Azure SQL Database az alábbi további képességeket biztosítja:

- Akár 100 TB-os adatbázisméret támogatása
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

## <a name="hyperscale-pricing-model"></a>Nagy kapacitású díjszabási modell

Nagy kapacitású szolgáltatási szinten érhető el csak [Virtuálismag-modell](sql-database-service-tiers-vcore.md). Az új architektúrája igazodni, a díjszabási modell kissé eltérő, az általános célú és az üzletileg kritikus szolgáltatási szinteken:

- **COMPUTE**:

  A rendkívül nagy számítási egységár replikánként van. A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) ár alkalmazott olvassa el automatikusan a méretezési csoport replikáit. Alapértelmezés szerint hozunk létre egy elsődleges replika és a egy csak olvasható replika nagy kapacitású adatbázisonként.  Felhasználók módosíthatja úgy, beleértve az 1 – 5. az elsődleges replika teljes száma.

- **Tárolási**:

  Adja meg az adatbázisonkénti maximális mérete egy nagy kapacitású adatbázis konfigurálása során nem kell. A rugalmas méretezés szintjén az adatbázis tárolási díjai a tényleges használat alapján lesznek kiszámlázva. Automatikusan lefoglalta közötti 10 GB-os és 100 TB-os lépésekben, amely dinamikusan módosulnak, 10 GB-os és 40GB között.  

Nagy kapacitású díjszabással kapcsolatos további információkért lásd: [Azure SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Elosztott funkciók architektúra

Központosított minden egy helyen/folyamat (tehát még ma az éles környezetben nevű elosztott adatbázisok egy monolitikus motor több példányával rendelkezik) a data felügyeleti funkciókat a hagyományos adatbázismotorokat ellentétben egy nagy kapacitású adatbázis elválasztó a lekérdezés-feldolgozás motor, ahol különböző adatmotort szemantikáját tér el, a hosszú távú tárolás és tartósságot biztosítanak az adatok összetevőket. Ezzel a módszerrel a tárolási kapacitás zökkenőmentesen kiterjeszthető szükség szerint (kezdeti cél a 100 TB-ot). Csak olvasható replikák megoszthatja az azonos tárolási összetevők adatok másolata nem szükséges új olvasható replika üzembe helyezését. 

A következő ábra szemlélteti a különböző típusú csomópont egy nagy kapacitású adatbázisban:

![architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

Nagy kapacitású adatbázis a következő különböző típusú csomópontokat tartalmazza:

### <a name="compute-node"></a>Számítási csomópont

A számítási csomóponton, ahol a relációs motorban él, így a nyelvi elemei, lekérdezés-feldolgozás és így tovább, akkor fordulhat elő. Ezek keresztül történik egy nagy kapacitású adatbázis minden felhasználói interakció számítási csomópontokon. Számítási csomópontok (feliratú RBPEX - rugalmas pufferkészlet-bővítmény a fenti ábrán) SSD-alapú gyorsítótárakat kell hálózati számának csökkentése érdekében adatváltások beolvasni az adatokat egy lapján szükséges. Van egy elsődleges számítási csomóponton, amelyen az olvasási és írási számítási feladatok és a tranzakciók feldolgozása. Nincsenek egy vagy több, feladatátvételi célból meleg készenléti csomópontok szerepét, valamint tehermentesítésének, olvassa el a számítási feladatok (Ha ez a funkció be van szükség) csak olvasható számítási csomópontok szerepét másodlagos számítási csomópontok.

### <a name="page-server-node"></a>Kiszolgáló-csomópont lap

Lap kiszolgálók azok a rendszerek jelölő horizontálisan felskálázott motorjába.  Minden lap kiszolgáló felelős az oldalak, az adatbázis egy részét.  Névlegesen minden egyes kiszolgáló Lapvezérlők 128 GB és 1 TB-nyi adatot között. Nincs adat (kívül őrzi meg a redundancia és a rendelkezésre állási replikák) egynél több lapot kiszolgálón van osztva. A feladat oldal kiszolgáló, hogy, a számítási csomópontokra, igény szerinti használhatók adatbázis oldalaihoz, valamint megőrzése a tranzakciók adatainak frissítéséhez frissíteni. Lap kiszolgálók bármelyikük által játszott rekordok naplózása a naplózási szolgáltatás. Lap kiszolgálók is fenn az SSD-alapú gyorsítótárak a teljesítmény növelése érdekében. Lapok hosszú távú tárolása a további megbízhatóság másolatok az Azure Storage-ban.

### <a name="log-service-node"></a>Naplózási szolgáltatás csomópont

A naplózási szolgáltatás csomópont fogadja el az elsődleges számítási csomópontból rekordok naplózása, továbbra is fennáll azokat egy tartós gyorsítótárban, és továbbítja a napló rögzíti a többi, a számítási csomópontok (tehát frissíthetnek gyorsítótárukban) a megfelelő lapon kiszolgáló (ko), valamint, hogy az adatok lehetnek a frissített t itt. Ezzel a módszerrel az elsődleges számítási csomópontból adatmódosítás a naplózási szolgáltatás az összes másodlagos számítási csomópontok és lap kiszolgálók keresztül továbbítja. Végül a naplózási bejegyzések akkor lesznek leküldve a hosszú távú tárolás az Azure Storage, amely egy végtelen tárházaként. Ez a mechanizmus eltávolítja a gyakori rendszernapló csonkolása szükségességét. A naplózási szolgáltatás is rendelkezik a helyi gyorsítótár hozzáférési felgyorsítása érdekében.

### <a name="azure-storage-node"></a>Az Azure storage-csomópont

Az Azure storage csomópontja lap kiszolgálók adatainak végső célja. Ez a tároló biztonsági mentés céljából, valamint az Azure-régiók közötti replikáció meghajtóbetűjeleket szolgál. Biztonsági másolatok adatfájlok pillanatképek állnak. Állítsa vissza a pillanatképek a gyors művelet, és adatokat bármely időpontra állíthatók időben.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Biztonsági másolatok fájlpillanatkép-alap és és az így szinte azonnali. Tárolási és számítási elkülönítésének lehetővé leküldése a biztonsági mentési vagy visszaállítási művelet le a tárolási rétegben az elsődleges számítási csomópont feldolgozási terhelésének csökkentése érdekében. Ennek eredményeképpen az egy nagyméretű adatbázis biztonsági mentését funkciója nem érinti az elsődleges számítási csomópont teljesítményét. Ehhez hasonlóan visszaállítások kell elvégeznie a fájl pillanatképének másolása és mint ilyen nem egy adatművelet méretét. A visszaállítás ugyanazon a tárfiókon belül a visszaállítási művelet gyors.

## <a name="scale-and-performance-advantages"></a>Méretezés és teljesítmény előnyei

Rövid idő alatt működésbe felfelé és lefelé csak olvasható számítási csomópontot lehetővé teszi a nagy kapacitású architektúra lehetővé teszi, hogy jelentős olvassa el a méretezési képességeit, és a további írási kérelmek számára elsődleges számítási csomópontot is felszabadítható. Ezenkívül a számítási csomópontok méretezhetők felfelé és lefelé gyorsan miatt a megosztott tároló-architektúra, a nagy kapacitású architektúra.

## <a name="create-a-hyperscale-database"></a>Nagy kapacitású adatbázis létrehozása

Nagy kapacitású adatbázis hozható létre a [az Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) vagy [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Nagy kapacitású adatbázisok használatával érhetők el csak a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

A következő T-SQL-parancsot egy nagy kapacitású adatbázist hoz létre. Meg kell adnia a edition és a szolgáltatás célja a `CREATE DATABASE` utasítást. Tekintse meg a [erőforráskorlátok](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier) érvényes szolgáltatási célkitűzések listáját.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Ezzel létrehoz egy nagy kapacitású adatbázis 4 maggal Gen5 hardveren.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>A nagy kapacitású szolgáltatásszinthez egy meglévő Azure SQL Database áttelepítése

Nagy kapacitású segítségével áthelyezheti a meglévő Azure SQL-adatbázisok a [az Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Jelenleg ez az egyirányú áttelepítést. Nagy kapacitású adatbázisok egy másik szolgáltatásszinthez nem válthat. Javasoljuk, hogy másolatot készít az éles adatbázisokat, és a nagy kapacitású át a megvalósíthatósági fogalmak (próbaverziójú).

A következő T-SQL parancsot a nagy kapacitású szolgáltatási rétegben, áthelyez egy adatbázist. Meg kell adnia a edition és a szolgáltatás célja a `ALTER DATABASE` utasítást.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Csatlakozás egy olvasási szintű replika nagy kapacitású adatbázis

Nagy kapacitású adatbázisokban a `ApplicationIntent` az ügyfél által megadott kapcsolati karakterlánc argumentum szabja meg, hogy az írható replikával, vagy egy írásvédett másodlagos replikára irányítja-e a kapcsolat. Ha a `ApplicationIntent` beállítása `READONLY` és az adatbázis nem rendelkezik másodlagos replika, a kapcsolat továbbítja az elsődleges replika-és az alapértelmezett érték `ReadWrite` viselkedését.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Nagy kapacitású adatbázisok Vészhelyreállítása
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Nagy kapacitású adatbázis visszaállítása egy másik földrajzi hely
Ha vissza kell állítania egy Azure SQL Database rendkívüli DB, egy régióban jelenleg található, a vész-helyreállítási művelet vagy részletes, az adatáthelyezési vagy bármilyen más okból részeként elsődleges módszere ehhez a geo-visszaállítás, az adatbázis.  Ebbe beletartozik az pontosan ugyanazokat a lépéseket bármely más AZURE SQL Database visszaállítása egy másik régióban szeretné használni:
1. Ha Ön még nem rendelkezik egy megfelelő kiszolgálóra van, hozzon létre egy SQL Database-kiszolgáló a célrégióban.  Ez a kiszolgáló ugyanahhoz az előfizetéshez, amely az eredeti (forrás) kiszolgáló helyvezérlőhöz kell tartoznia.
2. Kövesse az utasításokat a [geo-visszaállítás](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) a témakör az oldal az Azure SQL Database-adatbázisok automatikus biztonsági másolatokból való visszaállítással.

> [!NOTE]
> Mivel a forrás és cél külön régióban, az adatbázis nem oszthat meg pillanatkép-tároláshoz a forrás-adatbázis nem geo visszaállítás, amely rendkívül gyorsan befejeződhessen látható módon.  A geo-visszaállítás rendkívül nagy adatbázisok esetén lesz egy mérete az adatok a művelet akkor is, ha a cél a georeplikált tárolás a párosított régióban.  Ez azt jelenti, hogy ezt a geo-visszaállítás időt vesz igénybe a visszaállított adatbázis méretével arányos.  Ha a cél a párosított régióban, a másolási egy adatközpontban, ami jelentősen gyorsabb, mint a távolsági másolatot az interneten keresztül, de ez továbbra is másolja az összes bit.

## <a name=regions></a>Az elérhető régiók

Jelenleg az Azure SQL Database rendkívüli szinten érhető el a következő régiókban:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- USA középső régiója
- Kelet-Kína 2
- Észak-Kína 2
- Kelet-Ázsia
- USA keleti régiója
- East Us 2
- Közép-Franciaország
- Kelet-Japán
- Nyugat-Japán
- Korea középső régiója
- Korea déli régiója
- USA északi középső régiója
- Észak-Európa
- Dél-Afrika északi régiója
- USA déli középső régiója
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

Ha azt szeretné, nagy kapacitású adatbázis létrehozásához, amely nem szerepel a támogatott régióban, küldhet egy előkészítési kérelmet az Azure Portalon keresztül. Folyamatban van, bontsa ki a listát a támogatott régiók ezért ellenőrizze, hogy vissza legújabb régió listája.

A kérelem nem szereplő régióban nagy kapacitású adatbázisok létrehozása:

1. Navigáljon a [Azure Súgó és támogatás panelen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Kattintson a [ **új támogatási kérelem**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Az Azure Súgó és támogatás panelen](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. A **Issue Type**válassza **szolgáltatás és az előfizetések korlátai (kvóták)**

4. Válassza ki az előfizetést, az adatbázis(ok) létrehozásához használja

5. A **Kvótatípus**válassza **SQL-adatbázis**

6. Kattintson a **tovább: Megoldások**

1. Kattintson a **adja meg az adatokat**

    ![Probléma részletei](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Válasszon **SQL Database kvótatípus**: **Egyéb kvótakérelem**

9. Töltse ki az alábbi sablont:

    ![Kvóta részletei](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    A sablonban adja meg a következő információkat

    > Egy új régióban az Azure nagy kapacitású SQL-adatbázis létrehozására vonatkozó kérelem<br/> Terület: [Töltse ki a kért régióban]  <br/>
    > A számítási többek között a olvasható replikát Termékváltozat összesen magok <br/>
    > TB-os becsült száma 
    >

10. Válassza a **C súlyosság** lehetőséget

11. Válassza ki a megfelelő kapcsolattartási módszert, és adja meg adatokat.

12. Kattintson a **mentése** és **folytatása**

## <a name="known-limitations"></a>Ismert korlátozások
Ezek azok a nagy kapacitású szolgáltatási réteg GA kezdődően az aktuális korlátozások  Aktívan dolgozunk, ezek a korlátozások a lehető legtöbb eltávolítása.

| Probléma | Leírás |
| :---- | :--------- |
| Egy logikai kiszolgáló a biztonsági másolatok kezelése ablak nem jelenik meg a rendkívül nagy adatbázisok az SQL Serverből lesznek szűrve  | Nagy kapacitású rendelkezik egy külön metódusba biztonsági mentések kezeléséhez, és mint ilyen a hosszú távú adatmegőrzési és az idő a biztonsági mentés megőrzési beállításait pont csak akkor érvényesíthetők / érvénytelenné válnak. Ennek megfelelően a nagy kapacitású adatbázisok nem jelennek meg a biztonsági mentés kezelése ablak. |
| Adott időpontnak megfelelő helyreállítás | Adatbázis áttelepítése, a nagy kapacitású szolgáltatási rétegben, miután egy-időponthoz az áttelepítés előtt visszaállítás nem támogatott.|
| Állítsa vissza a nem – nagy kapacitású DB Hypserscale és fordítva | Nagy kapacitású adatbázis nem állítható vissza egy nagy kapacitású nem adatbázisba, és nem is, nem nagy kapacitású adatbázis visszaállítása egy nagy kapacitású adatbázisba.|
| Ha egy adatbázisfájl növekedésével az áttelepítést egy aktív számítási feladatok során, és átlép egy fájl határ az 1 TB-os, az áttelepítés sikertelen | Megoldások: <br> – Ha lehetséges, telepítse át az adatbázis nincs frissítés futó számítási feladat esetén.<br> – Próbálkozzon újra az áttelepítést, a sikeres lesz, amíg az 1 TB-os határ nem áthaladnak a migrálás során.|
| Felügyelt példány | Az Azure SQL Database felügyelt példánya jelenleg nem támogatott a nagy kapacitású adatbázisoknak. |
| Rugalmas készletek |  Rugalmas készletek jelenleg nem támogatottak az SQL Database rendkívüli.|
| Nagy kapacitású való migrálás jelenleg egy egyirányú művelet | Egy adatbázis van történő migrálást követően megismerkedhet nagy kapacitású, azt közvetlenül a nem nagy kapacitású szolgáltatásréteghez nem migrálható. Jelenleg az egyetlen módszer a nagy kapacitású adatbázis áttelepítése nem nagy kapacitású az Exportálás/importálás BACPAC-fájl használatával.|
| Az adatbázisok, memórián belüli objektumok áttelepítése | Memóriában lévő objektumok kell dobni, és nem-In-Memory objektumokként egy adatbázist a nagy kapacitású szolgáltatásszint-ra migrálás előtt újra létrehozza.|
| Adatok a változáskövetés | Nem tudja használni a változáskövetési adatok nagy kapacitású adatbázisok. |
| Georeplikáció  | Az Azure SQL Database rendkívüli még georeplikáció nem konfigurálható.  (A különböző földrajzi, az adatbázis visszaállítása a DR vagy egyéb célból) geo-visszaállítás elvégzése |
| TDE/AKV-integráció | Átlátható adatbázis-titkosítást az Azure Key Vaulttal (gyakran nevezik Bring-Your-saját-Key, azaz BYOK) még nem támogatott az Azure SQL Database rendkívüli, azonban a szolgáltatás által felügyelt kulcsokkal TDE teljes mértékben támogatott. |
|Intelligens adatbázis-szolgáltatások | 1. Hozzon létre az Index, Drop Index adviser modellek nem képzett nagy kapacitású adatbázisok számára. <br/>2. Sémaproblémát, DbParameterization - nemrégiben nagy kapacitású adatbázis nem támogatja a tanácsadók szerint.|



## <a name="next-steps"></a>További lépések

- Egy nagy kapacitású kapcsolatos gyakori kérdések, lásd: [nagy kapacitású kapcsolatos gyakori kérdések](sql-database-service-tier-hyperscale-faq.md).
- További információ a szolgáltatási szintekről: [szolgáltatásszintek](sql-database-service-tiers.md)
- Lásd: [erőforrás áttekintése korlátozza egy logikai kiszolgálón](sql-database-resource-limits-logical-server.md) kapcsolatos korlátozásokat a kiszolgálók és az előfizetés szintjén.
- Beszerzési modell korlátok egy önálló adatbázis, lásd: [Azure SQL Database Virtuálismag-alapú vásárlási modell korlátok egy önálló adatbázis](sql-database-vcore-resource-limits-single-databases.md).
- Az a funkciók és összehasonlító listában, lásd: [általános SQL-szolgáltatások](sql-database-features.md).
