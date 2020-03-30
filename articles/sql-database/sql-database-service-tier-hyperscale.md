---
title: Azure SQL Database – nagy léptékű áttekintés | Microsoft dokumentumok
description: Ez a cikk ismerteti a nagy kapacitású szolgáltatási szint a virtuálismag-alapú vásárlási modell az Azure SQL Database-ben, és bemutatja, hogyan különbözik az általános cél és az üzleti legkritikusabb szolgáltatási szintek.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: efb6cd1a45ac14dcbd5b2b6d8e70f5ee096ddbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255833"
---
# <a name="hyperscale-service-tier"></a>Rugalmas skálázás szolgáltatási szint

Az Azure SQL Database az SQL Server Database Engine architektúrán alapul, amely a felhőkörnyezethez van igazítva, hogy még infrastruktúra-hibák esetén is 99,99%-os rendelkezésre állást biztosítson. Az Azure SQL Database három architekturális modellt használ:
- Általános cél/szabvány 
-  Rugalmas skálázás
-  Üzleti kritikus /Prémium

Az Azure SQL Database nagy kapacitású szolgáltatási szintje a virtuálismag-alapú vásárlási modell legújabb szolgáltatási szintje. Ez a szolgáltatási szint egy jól méretezhető tárolási és számítási teljesítményszint, amely az Azure architektúrát kihasználva az Azure SQL-adatbázis tárolási és számítási erőforrásait jelentősen túllépi az általános célú és üzleti célokra rendelkezésre álló korlátokat. Kritikus szolgáltatási szintek.

> 
> [!NOTE]
> Az általános célú és üzleti legkritikusabb szolgáltatási szintek a virtuálismag-alapú vásárlási modell, [lásd: Általános cél](sql-database-service-tier-general-purpose.md) és [üzleti legkritikusabb](sql-database-service-tier-business-critical.md) szolgáltatási szintek. A virtuálismag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását az [Azure SQL Database modellek és erőforrások vásárlása](sql-database-service-tiers.md)című témakörben ismeri.


## <a name="what-are-the-hyperscale-capabilities"></a>Mik azok a nagy kapacitású képességek

Az Azure SQL Database nagy kapacitású szolgáltatási szintje a következő további képességeket biztosítja:

- Akár 100 TB adatbázisméret támogatása
- Szinte azonnali adatbázis-biztonsági mentések (az Azure Blob storage-ban tárolt fájlpillanatképek alapján), függetlenül a mérettől, és nincs i/o-hatás a számítási erőforrásokra  
- Gyors adatbázis-visszaállítás (fájlpillanatképek alapján) percek ben, nem pedig órákban vagy napokban (nem az adatművelet mérete)
- Nagyobb általános teljesítmény a nagyobb naplózási átviteli teljesítménynek és az adatkötetektől független gyorsabb tranzakciós véglegesítési időnek köszönhetően
- Gyors horizontális felskálázás – létrehozhat egy vagy több csak olvasható csomópontot az olvasási munkaterhelés kiszervezéséhez és a készenléti állapotban való használatra
- Gyors felskálázás – állandó idő alatt felskálázhatja a számítási erőforrásokat, hogy szükség esetén nagy számítási feladatoknak is megfeleljen, majd szükség esetén csökkentse a számítási erőforrásokat.

A nagy kapacitású szolgáltatási szint eltávolítja a felhőalapú adatbázisokban hagyományosan látható gyakorlati korlátok nagy részét. Ahol a legtöbb más adatbázisok egyetlen csomópontban elérhető erőforrások korlátozzák, a nagy kapacitású szolgáltatási szint adatbázisai nem rendelkeznek ilyen korlátokkal. Rugalmas tárolási architektúrájával a tárolás szükség szerint növekszik. Valójában a nagy kapacitású adatbázisok nem egy meghatározott maximális mérettel jönnek létre. A nagy kapacitású adatbázisok szükség szerint növekszik – és csak a használt kapacitásért kell fizetnie. Az olvasási igényű számítási feladatok hoz, a nagy kapacitású szolgáltatási szint gyors horizontális felskálázás további olvasási replikák kiépítése szükséges az olvasási számítási feladatok kiszervezéséhez.

Emellett az adatbázis-biztonsági mentések létrehozásához, illetve a fel- vagy leskálázáshoz szükséges idő már nem kapcsolódik az adatbázis ban lévő adatok mennyiségéhez. A nagy méretű adatbázisokról szinte azonnal biztonsági másolatot lehet kapni. Az adatbázisokat több tíz terabájtban fel- vagy leskálán is skálázhatja percek alatt. Ez a funkció megszabadítja Önt az aggodalmaktól, hogy a kezdeti konfigurációs lehetőségek bedobozolják.

A nagy kapacitású szolgáltatási szint számítási méreteiről a [Szolgáltatásiszint-jellemzők című témakörben talál](sql-database-service-tiers-vcore.md#service-tiers)további információt.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kinek kell figyelembe vennie a nagy kapacitású szolgáltatási szintet?

A nagy kapacitású szolgáltatási szint a legtöbb üzleti számítási feladathoz készült, mivel nagy rugalmasságot és nagy teljesítményt biztosít függetlenül méretezhető számítási és tárolási erőforrásokkal. Az akár 100 TB-os tárhely automatikus méretezésével nagyszerű választás azoknak az ügyfeleknek, akik:

- Nagy méretű adatbázisok helyszíni, és szeretné modernizálni az alkalmazások at a felhőbe való áthelyezés
- Már a felhőben vannak, és az egyéb szolgáltatási szintek (1-4 TB) maximális adatbázisméret-korlátozásai korlátozzák őket
- Kisebb adatbázisokkal rendelkezik, de gyors függőleges és vízszintes számítási skálázást, nagy teljesítményt, azonnali biztonsági mentést és gyors adatbázis-visszaállítást igényel.

A nagy kapacitású szolgáltatási szint az SQL Server számítási feladatok széles körét támogatja, a tiszta OLTP-től a tiszta elemzésig, de elsősorban oltp és hibrid tranzakciós és analitikus feldolgozási (HTAP) számítási feladatokra van optimalizálva.

> [!IMPORTANT]
> Rugalmas készletek nem támogatják a nagy kapacitású szolgáltatási szint.

## <a name="hyperscale-pricing-model"></a>Nagy méretű árképzési modell

A nagy kapacitású szolgáltatási szint csak [virtuálismag-modellben](sql-database-service-tiers-vcore.md)érhető el. Az új architektúrához igazodva a díjszabási modell némileg eltér az általános célú vagy az üzleti legkritikusabb szolgáltatási szintektől:

- **Számítás:**

  A nagy kapacitású számítási egységár replikánként. Az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) ár automatikusan a méretezési replikák olvasására vonatkozik. A nagykapacitású adatbázisonként alapértelmezés szerint létrehozunk egy elsődleges replikát és egy csak olvasható kópiát.  A felhasználók módosíthatják a replikák teljes számát, beleértve az elsődleges 1-5.

- **Tárolás**:

  A nagy kapacitású adatbázisok konfigurálásakor nem kell megadnia a maximális adatméretet. A nagy kapacitású rétegben a tényleges foglalás alapján díjat számítunk fel az adatbázis tárolásáért. A tárhely automatikusan 40 GB és 100 TB között lesz lefoglalva, 10 GB-os lépésekben 10 GB-os. Szükség esetén egyszerre több adatfájl is növekedhet. A nagy méretű adatbázis 10 GB-os kezdőmérettel jön létre, és 10 percenként 10 GB-tal növekszik, amíg el nem éri a 40 GB-os méretet.

A nagy léptékű díjszabásról az [Azure SQL Database díjszabása című](https://azure.microsoft.com/pricing/details/sql-database/single/) témakörben talál további információt.

## <a name="distributed-functions-architecture"></a>Elosztott függvények architektúrája

Ellentétben a hagyományos adatbázis-motorok, amelyek központosította az összes adatkezelési funkciók egy helyen / folyamat (még az úgynevezett elosztott adatbázisok éles ben ma több példányban egy monolitikus adatmotor), a nagy méretű adatbázis elválasztja a lekérdezésfeldolgozó motor, ahol a szemantika a különböző adatmotorok eltérnek az összetevők, amelyek hosszú távú tárolás és tartósság az adatok. Ily módon a tárolókapacitás szükség szerint zökkenőmentesen kiszélesedhet (a kezdeti cél 100 TB). Az írásvédett kópiák ugyanazokat a tárolási összetevőket osztják meg, így nincs szükség adatmásolatra egy új olvasható kópia felpörgetéséhez. 

Az alábbi ábra a nagy méretű adatbázisok különböző csomóponttípusait mutatja be:

![architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

A nagy kapacitású adatbázisok a következő különböző típusú összetevőket tartalmazzák:

### <a name="compute"></a>Compute

A számítási csomópont, ahol a relációs motor él, így az összes nyelvi elemek, lekérdezésfeldolgozás, és így tovább, fordul elő. A nagy kapacitású adatbázissal végzett összes felhasználói interakció ezeken a számítási csomópontokon keresztül történik. A számítási csomópontok SSD-alapú gyorsítótárakkal rendelkeznek (rbpex - rugalmas pufferkészlet-bővítmény az előző ábrán), hogy minimálisra csökkentsék az adatlapok lekéréséhez szükséges hálózati adatmenetek számát. Van egy elsődleges számítási csomópont, ahol az összes írási és olvasási számítási feladatok és tranzakciók feldolgozása. Vannak egy vagy több másodlagos számítási csomópontok, amelyek feladatátvételi célokra készenléti csomópontokként működnek, valamint csak olvasható számítási csomópontokként működnek az olvasási számítási feladatok kiszervezéséhez (ha ez a funkció szükséges).

### <a name="page-server"></a>Lapkiszolgáló

A lapkiszolgálók olyan rendszerek, amelyek egy kibővített tárolómotort képviselnek.  Minden lapkiszolgáló felelős az adatbázis lapjainak egy részhalmazáért.  Névlegesen minden oldalkiszolgáló 128 GB és 1 TB közötti adatforgalmat vezérel. Egyetlen adat sincs megosztva egynél több oldalkiszolgálón (a redundancia és a rendelkezésre állás érdekében tárolt replikákon kívül). Az oldalkiszolgáló feladata, hogy az adatbázislapokat igény szerint kiszolgálja a számítási csomópontokra, és a lapokat a tranzakciók frissítési adatainak frissítésekor frissítse. A lapkiszolgálók naprakészen maradnak a naplószolgáltatás naplórekordjainak lejátszásával. A lapkiszolgálók ssd-alapú gyorsítótárakat is tartanak a teljesítmény növelése érdekében. Az adatlapok hosszú távú tárolása az Azure Storage-ban tárolódik a további megbízhatóság érdekében.

### <a name="log-service"></a>Naplószolgáltatás

A naplószolgáltatás elfogadja az elsődleges számítási replika naplórekordjait, tartós gyorsítótárban megőrzi azokat, és továbbítja a naplórekordokat a többi számítási replikának (hogy frissíthessék a gyorsítótáraikat), valamint a megfelelő oldalkiszolgáló(ka)t, hogy az adatok frissíthetők legyenek. ott. Ily módon az elsődleges számítási kópia összes adatváltozása a naplószolgáltatáson keresztül az összes másodlagos számítási replikának és lapkiszolgálónak lesz propagálva. Végül a naplórekordok kivannak tolt a hosszú távú tárolás az Azure Storage, amely egy gyakorlatilag végtelen tárház. Ez a mechanizmus szükségtelenül veszi a gyakori naplócsonkolást. A naplószolgáltatás helyi gyorsítótárral is rendelkezik a naplóbejegyzésekhez való hozzáférés felgyorsítása érdekében.

### <a name="azure-storage"></a>Azure Storage-tárterület

Az Azure Storage az adatbázisösszes adatfájlját tartalmazza. A lapkiszolgálók naprakészen tartják az azure storage-ban tárolt adatfájlokat. Ez a tároló biztonsági mentési célokra, valamint az Azure-régiók közötti replikációra szolgál. A biztonsági mentések az adatfájlok tárolási pillanatképeivel valósulnak meg. A pillanatképek használatával végzett visszaállítási műveletek az adatok méretétől függetlenül gyorsak. Az adatok az adatbázis biztonsági mentési megőrzési időszakán belül bármikor visszaállíthatók.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A biztonsági mentések fájlpillanatkép-alapúak, ezért szinte azonnal ek. A tárolás és a számítási elkülönítés lehetővé teszi a biztonsági mentési/visszaállítási művelet lenyomását a tárolási rétegre az elsődleges számítási replika feldolgozási terheinek csökkentése érdekében. Ennek eredményeképpen az adatbázis biztonsági mentése nem befolyásolja az elsődleges számítási csomópont teljesítményét; hasonlóképpen a visszaállítások a fájlpillanatképek visszaállításával történnek, és mint ilyenek, nem az adatműveletek mérete. A visszaállítás állandó idejű művelet, és még a több terabájtos adatbázisok is visszaállíthatók percek ben órák vagy napok helyett. Az új adatbázisok létrehozása egy meglévő biztonsági másolat visszaállításával szintén kihasználja ezt a funkciót: az adatbázis-másolatok létrehozása fejlesztési vagy tesztelési célokra, még a terabájtoméretű adatbázisok esetében is, percek alatt megvalósítható.

## <a name="scale-and-performance-advantages"></a>Méretezési és teljesítménybeli előnyök

A további csak olvasási számítási csomópontok gyors fel- és lepörgetésének képességével a nagy kapacitású architektúra jelentős olvasási méretezési képességeket tesz lehetővé, és felszabadíthatja az elsődleges számítási csomópontot további írási kérelmek kiszolgálásához. Emellett a számítási csomópontok gyorsan skálázhatók felfelé/lefelé a nagy kapacitású architektúra megosztott tárolási architektúrájának köszönhetően.

## <a name="create-a-hyperscale-database"></a>Nagykapacitású adatbázis létrehozása

Az [Azure Portal](https://portal.azure.com), a [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) vagy a [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)használatával nagy kapacitású adatbázis hozható létre. A nagy kapacitású adatbázisok csak a [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)használatával érhetők el.

A következő T-SQL parancs létrehoz egy nagy kapacitású adatbázist. Az utasításban meg kell adnia `CREATE DATABASE` a kiadás és a szolgáltatás célkitűzését is. Az érvényes szolgáltatáscélok listáját az [erőforráskorlátokban](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) található.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Ez létrehoz egy nagy méretű adatbázist a Gen5 hardveren 4 maggal.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Meglévő Azure SQL-adatbázis áttelepítése a nagy kapacitású szolgáltatási szintre

Meglévő Azure SQL-adatbázisait áthelyezheti a Nagykapacitásba az [Azure Portal](https://portal.azure.com), [a T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [CLI használatával.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) Jelenleg ez egyirányú migráció. Az adatbázisok nem helyezhető át a nagy kapacitású szintről egy másik szolgáltatási szintre, kivéve az adatok exportálásával és importálásával. A koncepció igazolása (POCs), azt javasoljuk, hogy készítsen másolatot az éles adatbázisok, és migrálja a másolatot a Nagy kapacitásra. Egy meglévő Azure SQL-adatbázis áttelepítése a nagy kapacitású réteg az adatművelet mérete.

A következő T-SQL parancs áthelyezi az adatbázist a nagy kapacitású szolgáltatási szintre. Az utasításban meg kell adnia `ALTER DATABASE` a kiadás és a szolgáltatás célkitűzését is.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Csatlakozás nagy méretű adatbázis olvasási méretű replikájához

A nagy kapacitású `ApplicationIntent` adatbázisokban az ügyfél által biztosított kapcsolati karakterlánc argumentuma határozza meg, hogy a kapcsolat az írási replika vagy egy írásvédett másodlagos replika felé van-e irányítva. Ha `ApplicationIntent` a `READONLY` készlet és az adatbázis nem rendelkezik másodlagos replikával, a kapcsolat `ReadWrite` az elsődleges kópiához lesz irányítva, és alapértelmezés szerint a viselkedésre lesz irányítva.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

A nagykapacitású másodlagos replikák azonosak, ugyanazt a szolgáltatásiszint-célkitűzést használva, mint az elsődleges replika. Ha egynél több másodlagos replika van jelen, a számítási feladat elosztva az összes rendelkezésre álló másodlagos. Minden másodlagos replika egymástól függetlenül frissül, így a különböző replika lehet különböző adatkésés az elsődleges replika képest.

## <a name="database-high-availability-in-hyperscale"></a>Adatbázis magas rendelkezésre állása nagy kapacitásban

Mint minden más szolgáltatási szint, a nagy kapacitású garantálja az adatok tartósságát a véglegesített tranzakciók hoz, függetlenül a számítási replika rendelkezésre állásától. Az elsődleges replika elérhetetlenné válása miatti állásidő mértéke a feladatátvétel típusától (tervezett és nem tervezett) és legalább egy másodlagos replika jelenlététől függ. Egy tervezett feladatátvétel (azaz egy karbantartási esemény) esetén a rendszer vagy létrehozza az új elsődleges replika a feladatátvétel megkezdése előtt, vagy egy meglévő másodlagos replika a feladatátvételi cél. Nem tervezett feladatátvétel esetén (azaz az elsődleges replika hardverhibájában) a rendszer egy másodlagos replikát használ feladatátvételi célként, ha van ilyen, vagy új elsődleges replikát hoz létre a rendelkezésre álló számítási kapacitás készletéből. Az utóbbi esetben az állásidő időtartama hosszabb az új elsődleges replika létrehozásához szükséges további lépések miatt.

A nagy kapacitású SLA-ról az [Azure SQL Database sla című témakörben található.](https://azure.microsoft.com/support/legal/sla/sql-database/)

## <a name="disaster-recovery-for-hyperscale-databases"></a>Vészhelyreállítás nagy kapacitású adatbázisokhoz

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Nagy kapacitású adatbázis visszaállítása más földrajzi helyre
Ha vissza kell állítania egy Azure SQL Database hyperscale DB-t egy olyan régióba, amely jelenleg egy vész-helyreállítási művelet vagy fúró, áthelyezés vagy bármely más ok ból áll, az elsődleges módszer az adatbázis geo-visszaállítása.  Ez pontosan ugyanazokat a lépéseket foglalja magában, mint amit bármely más AZURE SQL DB visszaállíthat egy másik régióba:
1. Hozzon létre egy SQL Database-kiszolgálót a célrégióban, ha még nem rendelkezik megfelelő kiszolgálóval.  Ennek a kiszolgálónak ugyanannak az előfizetésnek a tulajdonában kell lennie, mint az eredeti (forrás) kiszolgálónak.
2. Kövesse az Azure SQL-adatbázisok automatikus biztonsági mentésből történő visszaállításáról szóló lap [geo-visszaállítási](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) témakörében található utasításokat.

> [!NOTE]
> Mivel a forrás és a cél külön régiókban található, az adatbázis nem oszthat meg pillanatkép-tárolót a forrásadatbázissal, mint a nem földrajzi visszaállításokban, amelyek rendkívül gyorsan befejeződnek. A nagy kapacitású adatbázis geo-visszaállítása esetén adatméret-művelet lesz, még akkor is, ha a cél a georeplikált tároló párosított régiójában található.  Ez azt jelenti, hogy a geo-visszaállítás időbe telik arányos az adatbázis visszaállítása.  Ha a cél a párosított régióban van, a másolat egy régión belül lesz, ami jelentősen gyorsabb lesz, mint a régiók közötti másolás, de továbbra is adatméret-művelet lesz.

## <a name="available-regions"></a><a name=regions></a>Elérhető régiók

Az Azure SQL Database nagy kapacitású réteg jelenleg a következő régiókban érhető el:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- USA középső régiója
- Kína Keleti 2
- Kína Észak 2
- Kelet-Ázsia
- USA keleti régiója
- Kelet-Us 2
- Közép-Franciaország
- Kelet-Japán
- Nyugat-Japán
- Dél-Korea középső régiója
- Dél-Korea déli régiója
- USA északi középső régiója
- Észak-Európa
- Dél-Afrika Észak-Afrika
- USA déli középső régiója
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- Nyugat-Európa
- USA nyugati régiója
- USA nyugati régiója, 2.

Ha olyan régióban szeretne nagy kapacitású adatbázist létrehozni, amely nem támogatottként szerepel, az Azure Portalon keresztül is küldhet bevezetési kérelmet. További információt az [Azure SQL Database-hez tartozó kvótaemelések kérése](quota-increase-request.md) című témakörben talál. A kérelem benyújtásakor kövesse el az alábbi irányelveket:

- Használja az [Egyéb kvótakérelem](quota-increase-request.md#other) SQL adatbáziskvóta típusát.
- A szöveg részleteihez adja hozzá a számítási termékváltozatokat/összes magokat, beleértve az olvasható replikákat is.
- Adja meg a becsült TB-t is.

## <a name="known-limitations"></a>Ismert korlátozások

Ezek a nagy kapacitású szolgáltatási szint jelenlegi korlátai, mint a GA.  Aktívan dolgozunk azon, hogy a lehető legtöbb ilyen korlátozást eltávolítsuk.

| Probléma | Leírás |
| :---- | :--------- |
| A logikai kiszolgáló Biztonsági mentések kezelése ablaktáblája nem jeleníti meg a nagy méretű adatbázisok sql-kiszolgálóról történő szűrését  | A nagy kapacitású biztonsági mentések kezelésére külön módszert alkalmaznak, és mint ilyen, a hosszú távú megőrzési és a pont-in time biztonsági mentés idotartatási beállításai nem érvényesek / érvénytelenítik. Ennek megfelelően a nagy kapacitású adatbázisok nem jelennek meg a Biztonsági másolat kezelése ablaktáblában. |
| Adott időpontnak megfelelő helyreállítás | Az adatbázis nagy kapacitású szolgáltatási szintre való áttelepítése után az áttelepítés t megelőző időpontra való visszaállítás nem támogatott.|
| Nem nagy méretű DB visszaállítása nagy méretűre és fordítva | A nagy kapacitású adatbázisok nem állíthatók vissza nem nagy kapacitású adatbázisba, és nem nagy kapacitású adatbázisokba sem állíthatók vissza.|
| Ha egy adatbázis egy vagy több adatfájlja 1 TB-nál nagyobb, az áttelepítés sikertelen | Bizonyos esetekben előfordulhat, hogy a probléma megkerülése a nagy fájlok 1 TB-nál kisebbre zsugorításával lehetséges. Ha az áttelepítési folyamat során használt adatbázist telepít át, győződjön meg arról, hogy egyetlen fájl sem lesz nagyobb 1 TB-nál. Az adatbázisfájlok méretének meghatározásához használja az alábbi lekérdezést. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Felügyelt példány | Az Azure SQL Database felügyelt példánya jelenleg nem támogatott a nagy kapacitású adatbázisok. |
| Rugalmas készletek |  Rugalmas készletek jelenleg nem támogatott az SQL Database nagykapacitású.|
| A nagy kapacitásra való áttérés jelenleg egyirányú művelet | Az adatbázis nagy kapacitásra való áttelepítése után nem telepíthető át közvetlenül egy nem nagy kapacitású szolgáltatási szintre. Jelenleg az adatbázis nagy kapacitásról nem nagy kapacitásra történő áttelepítésének egyetlen módja bacpac fájl vagy más adatmozgatási technológiák (tömeges másolás, Azure Data Factory, Azure Databricks, SSIS stb.) használatával történő exportálás/importálás.|
| Állandó memórián belüli objektumokkal rendelkező adatbázisok áttelepítése | A nagy kapacitású csak a nem állandó memórián belüli objektumokat (táblatípusokat, natív SP-ket és függvényeket) támogatja.  Az állandó memórián belüli táblákat és egyéb objektumokat el kell dobni, majd újra létre kell hozni nem a memóriában lévő objektumokként, mielőtt egy adatbázist áttelepítenének a nagykapacitású szolgáltatási szintre.|
| Változások követése | A változáskövetés jelenleg nyilvános előzetes verzióban érhető el, és új vagy meglévő nagy kapacitású adatbázisokban engedélyezhető. |
| Geo replikáció  | Az Azure SQL Database hiperpikkelyes georeplikációja még nem konfigurálható. |
| Adatbázis másolása | Az Adatbázis-másolás szolgáltatással még nem hozhat létre új adatbázist az Azure SQL-alapú nagy kapacitású rendszerben. |
| TDE/AKV integráció | Az Azure SQL Database hyperscale még nem támogatott az Azure ACCESS Database Encryption használatával az Azure Key Vault használatával (közismert nevén Bring-Your-Own-Key vagy BYOK) használatával, azonban a TDE szolgáltatás felügyelt kulcsokkal teljes mértékben támogatott. |
|Intelligens adatbázis-szolgáltatások | A "Kényszerítési terv" lehetőség kivételével az összes többi automatikus hangolási lehetőség még nem támogatott a nagy méretezésű skálán: úgy tűnhet, hogy a beállítások engedélyezve vannak, de nem lesznek javaslatok és műveletek. |
|Lekérdezési teljesítmény elemzése | A Query Performance Insights jelenleg nem támogatott a nagy kapacitású adatbázisok. |
| Adatbázis zsugorítása | A DBCC SHRINKDATABASE vagy DBCC SHRINKFILE jelenleg nem támogatott a nagy kapacitású adatbázisok ban. |
| Adatbázis integritásának ellenőrzése | A DBCC CHECKDB jelenleg nem támogatott a nagy kapacitású adatbázisok esetében. [Az Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) Database adatintegritási kezelésével kapcsolatos részletekért tekintse meg az Azure SQL Database adatintegritásának kezelését. |

## <a name="next-steps"></a>További lépések

- A nagy méretarányú gyakori kérdésekről a Gyakori [kérdések a Nagykapacitással kapcsolatban](sql-database-service-tier-hyperscale-faq.md)( Gyakori kérdések) témakörben talál.
- A szolgáltatási szintekről a [Szolgáltatási szintek című témakörben](sql-database-service-tiers.md) talál további információt.
- A kiszolgálóés az előfizetési szintek korlátairól a [logikai kiszolgálón lévő erőforráskorlátok áttekintése](sql-database-resource-limits-logical-server.md) című témakörben olvashat.
- Egyetlen adatbázis modellkorlátainak megvásárlásáról az [Azure SQL Database virtuálismag-alapú vásárlási modell korlátai egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)című témakörben található.
- A szolgáltatások és az összehasonlító lista az SQL általános szolgáltatásai című témakörben [található.](sql-database-features.md)
