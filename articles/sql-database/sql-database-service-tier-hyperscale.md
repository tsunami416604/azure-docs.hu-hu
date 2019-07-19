---
title: Azure SQL Database nagy kapacitású – áttekintés | Microsoft Docs
description: Ez a cikk ismerteti a nagy kapacitású szolgáltatási rétegét a Azure SQL Database virtuális mag-alapú vásárlási modellben, és elmagyarázza, hogy miben különbözik a általános célú és a üzletileg kritikus szolgáltatási rétegtől.
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
ms.openlocfilehash: 707f7f47b050d22d0bd225cf412181902ae84f50
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974463"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Nagy kapacitású szolgáltatási szintet akár 100 TB-ig

A Azure SQL Database a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. A Azure SQL Database három építészeti modellt használ:
- Általános célú/standard 
-  Rugalmas skálázás
-  Üzletileg kritikus/prémium

A Azure SQL Database nagy kapacitású szolgáltatási szintje a legújabb szolgáltatási réteg a virtuális mag-alapú vásárlási modellben. Ez a szolgáltatási réteg egy rugalmasan méretezhető tárolási és számítási teljesítményi réteg, amely az Azure-architektúrát használja a általános célú és a vállalat számára elérhető korlátokon túlmenően a Azure SQL Database tárolási és számítási erőforrásainak méretezésére. Kritikus szolgáltatási szintek.

> 
> [!NOTE]
> A virtuális mag-alapú vásárlási modell általános célú és üzletileg kritikus szolgáltatási szintjeivel kapcsolatos részletekért lásd: [általános célú](sql-database-service-tier-general-purpose.md) és [üzletileg kritikus](sql-database-service-tier-business-critical.md) szolgáltatási szintek. A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>A nagy kapacitású képességei

A Azure SQL Database nagy kapacitású szolgáltatási szintje a következő kiegészítő képességeket nyújtja:

- Akár 100 TB-os adatbázis-méret támogatása
- Szinte azonnali adatbázis-biztonsági másolatok (az Azure Blob Storage-ban tárolt fájl-Pillanatképek alapján), függetlenül a számítási erőforrásokra vonatkozó IO-hatás nélküli mérettől  
- Gyors adatbázis-visszaállítások (fájl-Pillanatképek alapján) percek és napok helyett (nem az adatműveletek mérete)
- Nagyobb általános teljesítmény a nagyobb naplózási átviteli sebesség és a gyorsabb tranzakció-végrehajtási idő miatt, függetlenül az adatmennyiségtől
- Gyors kibővítés – kiépíthető egy vagy több írásvédett csomópont az olvasási számítási feladat kiszervezéséhez és a meleg készenléti használatra
- Gyors vertikális felskálázás – állandó időben, a számítási erőforrások vertikális felskálázásával nagy mennyiségű számítási feladathoz is alkalmazkodhat, és szükség esetén a számítási erőforrások méretezése nem szükséges.

A nagy kapacitású szolgáltatási szintje eltávolítja a Felhőbeli adatbázisokban hagyományosan látható gyakorlati korlátokat. Ahol a legtöbb más adatbázist az egyetlen csomópontban elérhető erőforrások korlátozzák, a nagy kapacitású szolgáltatási rétegében lévő adatbázisoknak nincsenek ilyen korlátai. Rugalmas tárolási architektúrája esetén a tárterület igény szerint növekszik. Valójában a nagy kapacitású-adatbázisok nem a megadott maximális mérettel jönnek létre. A nagy kapacitású-adatbázisok igény szerint növekednek, és csak a használt kapacitásért kell fizetni. Az olvasási igényű számítási feladatokhoz a nagy kapacitású szolgáltatási réteg gyors méretezést biztosít az olvasási feladatok kiszervezéséhez szükséges további olvasási replikák kiosztásával.

Emellett az adatbázis biztonsági másolatainak létrehozásához vagy a vertikális felskálázáshoz szükséges idő már nem kötődik az adatbázisban lévő adatmennyiséghez. A nagy kapacitású-adatbázisok biztonsági mentése gyakorlatilag azonnal elvégezhető. Percek alatt akár több, akár több mint terabájtos méretű adatbázis is méretezhető. Ez a funkció felkínálja a kezdeti konfigurációs döntésekből származó, a bekeretezett funkciókkal kapcsolatos problémákat.

A nagy kapacitású szolgáltatási szintjéhez tartozó számítási méretekről további információt a [szolgáltatási szintek jellemzői](sql-database-service-tiers-vcore.md#service-tier-characteristics)című témakörben talál.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kinek érdemes figyelembe vennie a nagy kapacitású szolgáltatási szintet

Az nagy kapacitású szolgáltatási réteg elsődlegesen olyan ügyfelek számára készült, akik a nagyméretű adatbázisokkal rendelkeznek, és a felhőbe vagy a már felhőben lévő ügyfelekre való áttéréssel szeretnék modernizálni az alkalmazásaikat, és az adatbázis maximális mérete korlátozza őket. korlátozások (1-4 TB). Emellett olyan ügyfelek számára is ajánlott, akik nagy teljesítményt és nagy skálázhatóságot keresnek a tároláshoz és a számítási feladatokhoz.

A nagy kapacitású szolgáltatási szintje támogatja az összes SQL Server munkaterhelést, de elsődlegesen a OLTP-re van optimalizálva. A nagy kapacitású szolgáltatási szintje támogatja a hibrid és analitikai (data mart) munkaterheléseket is.

> [!IMPORTANT]
> A rugalmas készletek nem támogatják a nagy kapacitású szolgáltatási szintet.

## <a name="hyperscale-pricing-model"></a>Nagy kapacitású díjszabási modellje

A nagy kapacitású szolgáltatási szintje csak a [virtuális mag modellben](sql-database-service-tiers-vcore.md)érhető el. Az új architektúrához való igazításhoz a díjszabási modell némileg eltér általános célú vagy üzletileg kritikus szolgáltatási szintjeitől:

- **Számítás**:

  A nagy kapacitású számítási egységének díja replika. A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) árát a rendszer automatikusan beolvassa a skálázási replikákat. Alapértelmezés szerint egy elsődleges replikát és egy írásvédett replikát hozunk létre nagy kapacitású-adatbázisban.  A felhasználók a replikák teljes számát módosíthatják, beleértve az elsődleges 1-5-as értéket is.

- **Tárterület**:

  A nagy kapacitású-adatbázis konfigurálásakor nem kell megadnia a maximális adatméretet. A rugalmas méretezés szintjén az adatbázis tárolási díjai a tényleges használat alapján lesznek kiszámlázva. A tárterületet a rendszer automatikusan 10 GB és 100 TB között osztja el a 10 GB és 40 GB közötti dinamikusan igazított növekményekben.  

További információ a nagy kapacitású díjszabásáról: [Azure SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Elosztott függvények architektúrája

Az egy helyen/folyamatban lévő összes adatkezelési függvényt központosító, hagyományos adatbázis-kezelővel ellentétben (még az éles környezetben lévő elosztott adatbázisok esetében is) a nagy kapacitású-adatbázis elkülönül. a lekérdezés-feldolgozó motor, ahol a különböző adatmotorok szemantikaa eltér az adatok hosszú távú tárolását és tartósságát biztosító összetevőktől. Így a tárolási kapacitás igény szerint zökkenőmentesen méretezhető (a kezdeti cél 100 TB). Az írásvédett replikák ugyanazokat a tárolási összetevőket használják, ezért nincs szükség adatmásolásra egy új, olvasható replika kipörgetéséhez. 

A következő ábra a nagy kapacitású-adatbázisok különböző típusait szemlélteti:

![architektúra](./media/sql-database-hyperscale/hyperscale-architecture.png)

A nagy kapacitású-adatbázisok a következő különböző típusú csomópontokat tartalmazzák:

### <a name="compute-node"></a>Számítási csomópont

A számítási csomópont az a hely, ahol a viszonyítási motor él, így minden nyelvi elem, lekérdezés feldolgozása és így tovább történik. A nagy kapacitású-adatbázissal folytatott összes felhasználói interakció ezen számítási csomópontokon keresztül történik. A számítási csomópontok SSD-alapú gyorsítótárral rendelkeznek (az előző ábrán megcímkézett RBPEX-rugalmasságú puffer-bővítmény), így minimálisra csökkenthető az adatoldalak lekéréséhez szükséges hálózati kör-átutazások száma. Létezik egy elsődleges számítási csomópont, amelyben az összes írási és olvasási feladat feldolgozásra kerül. Van egy vagy több olyan másodlagos számítási csomópont, amely gyors készenléti csomópontként működik feladatátvételi célokra, valamint írásvédett számítási csomópontként működik az olvasási feladatok kiszervezéséhez (ha ez a funkció szükséges).

### <a name="page-server-node"></a>Page Server-csomópont

A kiszolgálóoldali kiszolgálók egy kibővített tárolási motort jelképező rendszerek.  Az egyes lapozófájlok felelősek az adatbázis lapjainak egy részhalmaza számára.  Az egyes lapok kiszolgálói névlegesen 128 GB és 1 TB adat között vezérlik. A rendszer egyetlen, a redundancia és a rendelkezésre állás érdekében megőrzött replikán kívül nem osztja meg az adatmegosztást. Az oldal-kiszolgáló feladata az, hogy az adatbázis-lapokat a számítási csomópontok igény szerint kiszolgálják, és hogy a lapok frissítve legyenek a tranzakciók frissítési adataiként. A rendszer naprakészen tartja a lapok kiszolgálóit a log szolgáltatás naplófájljainak lejátszásával. Az oldal-kiszolgálók a teljesítmény növelése érdekében SSD-alapú gyorsítótárat is tartanak. Az adatlapok hosszú távú tárolása az Azure Storage-ban további megbízhatóságot biztosít.

### <a name="log-service-node"></a>Naplózási szolgáltatás csomópontja

A naplózási szolgáltatás csomópontja fogadja az elsődleges számítási csomópont naplófájljait, tartós gyorsítótárban tárolja őket, és továbbítja a napló rekordjait a számítási csomópontok többi részébe (a gyorsítótárak frissítéséhez), valamint a kapcsolódó kiszolgáló (k) et, hogy az adatok frissíthetők legyenek. itt. Így az elsődleges számítási csomópont összes adatváltozása a naplózási szolgáltatáson keresztül továbbítódik a másodlagos számítási csomópontok és a lapok kiszolgálójára. Végül a rendszer leküldi a naplóbejegyzések (ka) t a hosszú távú tárolásra az Azure Storage-ban, amely egy végtelen tárolási tárház. Ez a mechanizmus eltávolítja a gyakori naplózási csonkítás szükségességét. A log szolgáltatás emellett helyi gyorsítótárral is rendelkezik a hozzáférés felgyorsításához.

### <a name="azure-storage-node"></a>Azure Storage-csomópont

Az Azure Storage csomópont az adatok végső rendeltetése az oldal kiszolgálóiról. Ez a tároló biztonsági mentési célokra, valamint az Azure-régiók közötti replikálásra szolgál. A biztonsági mentések az adatfájlok pillanatképeit tartalmazzák. A visszaállítási művelet gyorsan elvégezhető ezekről a pillanatképekről, az adatok pedig bármikor visszaállíthatók.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A biztonsági mentések a fájl-pillanatkép alapja, ezért majdnem azonnal elérhetők. A tárolás és a számítási elkülönítés lehetővé teszi a biztonsági mentési/visszaállítási művelet lenyomását a tárolási rétegre, hogy csökkentse az elsődleges számítási csomópont feldolgozási terhelését. Ennek eredményeképpen a nagyméretű adatbázisok biztonsági mentése nem befolyásolja az elsődleges számítási csomópont teljesítményét. Hasonlóképpen, a visszaállítás a fájl pillanatképének másolásával történik, és nem az adatműveletek mérete. Ugyanazon a Storage-fiókon belül a visszaállítási művelet gyorsan elvégezhető.

## <a name="scale-and-performance-advantages"></a>Méretezési és teljesítménybeli előnyök

A további írásvédett számítási csomópontok gyors üzembe helyezésének képessége lehetővé teszi, hogy a nagy kapacitású architektúra jelentős olvasási méretezési képességeket biztosítson, és felszabadítsa az elsődleges számítási csomópontot a további írási kérelmek kiszolgálásához. A számítási csomópontokat a nagy kapacitású architektúra megosztott tárolási architektúrája miatt gyorsan fel-és leskálázással is elvégezheti.

## <a name="create-a-hyperscale-database"></a>Nagy kapacitású-adatbázis létrehozása

Nagy kapacitású-adatbázis hozható létre a [Azure Portal](https://portal.azure.com), a [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) vagy a [parancssori](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)felület használatával. A nagy kapacitású-adatbázisok csak a [virtuális mag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)használatával érhetők el.

A következő T-SQL-parancs egy nagy kapacitású-adatbázist hoz létre. Az `CREATE DATABASE` utasításban meg kell adni a kiadás és a szolgáltatás célját is. Tekintse át az érvényes szolgáltatási célkitűzések listáját az [erőforrás-korlátok között](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier) .

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Ez egy nagy kapacitású-adatbázist hoz létre a Gen5 hardveren 4 maggal.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Meglévő Azure SQL Database migrálása a nagy kapacitású szolgáltatási szintjére

A meglévő Azure SQL-adatbázisokat a [Azure Portal](https://portal.azure.com), a [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), a [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) vagy a [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)használatával helyezheti át a nagy kapacitású. Ebben az esetben ez egy egyirányú áttelepítés. Az adatbázisok nem helyezhetők át a nagy kapacitású egy másik szolgáltatási szintjére. Javasoljuk, hogy készítsen másolatot az éles adatbázisokról, és váltson át a nagy kapacitású-re a fogalmak igazolására (Pócs).

A következő T-SQL parancs egy adatbázist helyez át a nagy kapacitású szolgáltatási szintjére. Az `ALTER DATABASE` utasításban meg kell adni a kiadás és a szolgáltatás célját is.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Kapcsolódás nagy kapacitású-adatbázis egy olvasási léptékű replikához

A nagy kapacitású-adatbázisokban `ApplicationIntent` az ügyfél által megadott kapcsolódási karakterlánc argumentuma azt határozza meg, hogy a Kapcsolódás az írási replikához vagy egy írásvédett másodlagos replikához van-e irányítva. Ha a `ApplicationIntent` `READONLY` beállítása és az adatbázis nem rendelkezik másodlagos replikával, a rendszer a kapcsolódást az elsődleges `ReadWrite` replikához irányítja, és az alapértelmezett viselkedést adja meg.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Vész-helyreállítási nagy kapacitású-adatbázisokhoz
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Nagy kapacitású-adatbázis visszaállítása más földrajzi helyekre
Ha egy Azure SQL Database nagy kapacitású ADATBÁZISát egy olyan régióba kívánja visszaállítani, amely nem az aktuálisan üzemel, akkor a vész-helyreállítási művelet vagy a részletezés, az áthelyezés vagy más ok miatt az elsődleges módszer az adatbázis geo-visszaállításának elvégzése.  Ez pontosan ugyanazokat a lépéseket foglalja magában, mint amelyeket az egyéb AZURE SQL-adatbázisok más régióba való visszaállításához használ:
1. Ha még nem rendelkezik megfelelő kiszolgálóval, hozzon létre egy SQL Database kiszolgálót a célként megadott régióban.  Ennek a kiszolgálónak ugyanahhoz az előfizetéshez kell tartoznia, mint az eredeti (forrás) kiszolgálónak.
2. Kövesse az Azure SQL Database-adatbázisok automatikus biztonsági mentésből való visszaállítására szolgáló oldal [geo-visszaállítás](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) témakörében található utasításokat.

> [!NOTE]
> Mivel a forrás és a cél különálló régiókban található, az adatbázis nem tudja megosztani a pillanatkép-tárolót a forrás-adatbázissal a nem geo-visszaállítások során, ami rendkívül gyorsan elvégezhető.  Ha a nagy kapacitású-adatbázis geo-visszaállítást végez, akkor akkor is adatmennyiség-adatművelet lesz, ha a cél a földrajzilag replikált tároló párosított régiójában található.  Ez azt jelenti, hogy a Geo-visszaállítás a visszaállított adatbázis méretével arányos időt vesz igénybe.  Ha a cél a párosított régióban található, a másolás egy adatközpontban fog megjelenni, ami jóval gyorsabb, mint az interneten keresztüli távolság, de az összes bitet továbbra is másolja.

## <a name=regions></a>Elérhető régiók

A Azure SQL Database nagy kapacitású szintje jelenleg a következő régiókban érhető el:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- USA középső régiója
- Kelet-Kína 2
- Észak-Kína 2
- Kelet-Ázsia
- East US
- USA 2. keleti régiója
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

Ha olyan régióban szeretné létrehozni a nagy kapacitású-adatbázist, amely nem támogatottként van felsorolva, Azure Portal használatával küldhet bevezetési kérést. Dolgozunk a támogatott régiók listájának kibontásán, ezért kérjük, térjen vissza a legutóbbi régiók listájára.

Nagy kapacitású-adatbázisok létrehozásának lehetősége a felsorolt régiókban:

1. Navigáljon az [Azure Súgó és támogatás](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) paneljére

2. Kattintson az [ **új támogatási kérelem** elemre.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure Súgó és támogatás panel](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. A **probléma típusa**beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták) lehetőséget.**

4. Válassza ki az adatbázis (ok) létrehozásához használni kívánt előfizetést.

5. A **kvóta típusa**beállításnál válassza az **SQL Database** lehetőséget.

6. Kattintson **a Tovább gombra: Megoldások**

1. Kattintson a **részletek megadása** lehetőségre

    ![Probléma részletei](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. **SQL Database kvóta típusának**kiválasztása: **Egyéb kvóta-kérelem**

9. Töltse ki a következő sablont:

    ![Kvóta részletei](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    A sablonban adja meg a következő adatokat:

    > Azure nagy kapacitású-SQL Database új régióban való létrehozásának kérése<br/> Régió: [kitöltés a kért régióban]  <br/>
    > Számítási SKU/összes mag, beleértve az olvasható replikákat <br/>
    > A TB becsült száma 
    >

10. Válassza a **C súlyosság** lehetőséget

11. Válassza ki a megfelelő kapcsolattartási módszert, és adja meg a részleteket.

12. Kattintson a **Mentés** és **Folytatás** gombra.

## <a name="known-limitations"></a>Ismert korlátozások
Ezek a nagy kapacitású szolgáltatási szintjére vonatkozó jelenlegi korlátozások a GA-ban.  Aktívan dolgozunk azon, hogy minél több korlátozást távolítson el.

| Probléma | Leírás |
| :---- | :--------- |
| A logikai kiszolgáló biztonsági mentések ablaktáblája nem jeleníti meg a nagy kapacitású-adatbázisok szűrését az SQL Server rendszerből  | A nagy kapacitású külön módszert biztosít a biztonsági mentések kezeléséhez, így a hosszú távú megőrzési és időponthoz kötött biztonsági mentési adatmegőrzési beállítások nem érvényesek/érvénytelenítve vannak. Ennek megfelelően a nagy kapacitású-adatbázisok nem jelennek meg a biztonsági mentés kezelése ablaktáblán. |
| Adott időpontnak megfelelő helyreállítás | Miután áttelepítette az adatbázist a nagy kapacitású szolgáltatási szintjére, a Migrálás előtti időpontban történő visszaállítás nem támogatott.|
| Nem nagy kapacitású adatbázis visszaállítása Hypserscale és fordítva | A nagy kapacitású-adatbázisok nem állíthatók vissza nem nagy kapacitású adatbázisba, és nem állíthatók vissza egy nem nagy kapacitású adatbázis egy nagy kapacitású-adatbázisba.|
| Ha egy adatbázisfájl egy aktív munkaterhelés miatt növekszik az áttelepítés során, és az 1 TB-ot egy fájl határán keresztül éri el, az áttelepítés meghiúsul | Enyhítését <br> – Ha lehetséges, telepítse át az adatbázist, ha nem fut frissítési feladat.<br> -Próbálja meg újból elvégezni az áttelepítést, amíg az 1 TB-os határ nem halad át az áttelepítés során.|
| Felügyelt példány | Azure SQL Database felügyelt példány jelenleg nem támogatott a nagy kapacitású-adatbázisokban. |
| Rugalmas készletek |  A rugalmas készletek jelenleg nem támogatottak SQL Database nagy kapacitású.|
| A nagy kapacitású-re történő áttelepítés jelenleg egyirányú művelet | Ha egy adatbázis át lett telepítve a nagy kapacitású-be, az nem telepíthető át közvetlenül egy nem nagy kapacitású szolgáltatási rétegre. Jelenleg az egyik lehetőség, hogy az adatbázist a nagy kapacitású-ről a nem nagy kapacitású-re telepítse át, egy BACPAC-fájl használatával exportálja/importálja.|
| Adatbázisok áttelepítése állandó memóriában tárolt objektumokkal | A nagy kapacitású csak a nem állandó memóriabeli objektumokat támogatja (a táblák típusai, a natív SPs és a functions).  Az állandó memóriában lévő táblákat és egyéb objektumokat el kell dobni, és nem memóriában lévő objektumként kell újból létrehozni, mielőtt áttelepíti az adatbázist a nagy kapacitású szolgáltatási szintjére.|
| Adatkövetés módosítása | A nagy kapacitású-adatbázisokkal nem fogja tudni használni az adatváltozások nyomon követését. |
| Georeplikáció  | Azure SQL Database nagy kapacitású esetében még nem konfigurálhatja a Geo-replikációt.  Elvégezheti a Geo-visszaállításokat (az adatbázis visszaállítása más földrajzi helyen, DR vagy más célra) |
| TDE/AKV-integráció | A Azure Key Vault (általában saját kulcsú vagy BYOK) használatával történő transzparens adatbázis-titkosítás még nem támogatott Azure SQL Database nagy kapacitású, azonban a szolgáltatás által felügyelt kulcsokkal való TDE teljes mértékben támogatott. |
|Intelligens adatbázis-funkciók | 1. Index létrehozása, a drop index tanácsadói modelljei nincsenek betanítva a nagy kapacitású-adatbázisok számára. <br/>2. Sémabeli probléma, DbParameterization – a közelmúltban hozzáadott tanácsadók nem támogatottak a nagy kapacitású-adatbázisokban.|



## <a name="next-steps"></a>További lépések

- A nagy kapacitású kapcsolatos gyakori kérdésekért lásd: [nagy kapacitású](sql-database-service-tier-hyperscale-faq.md).
- További információ a szolgáltatási szintekről: [szolgáltatási szintek](sql-database-service-tiers.md)
- A kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért tekintse [meg a logikai kiszolgálók erőforrás-korlátainak áttekintése](sql-database-resource-limits-logical-server.md) című témakört.
- A modellre vonatkozó korlátok egyetlen adatbázishoz való megvásárlásával kapcsolatban lásd: [Azure SQL Database virtuális mag-alapú vásárlási modell korlátai egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](sql-database-features.md).
