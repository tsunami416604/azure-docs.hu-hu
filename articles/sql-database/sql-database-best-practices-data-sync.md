---
title: Ajánlott eljárások az Azure SQL Data szinkronizálás |} Microsoft Docs
description: További információk a ajánlott eljárások az Azure SQL adatszinkronizálás fut és konfigurálása.
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.openlocfilehash: b53c72f1df4f2fc2509d91220d08aff4682b6620
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025823"
---
# <a name="best-practices-for-sql-data-sync"></a>Az SQL Data Synchez ajánlott eljárások 

Ez a cikk ismerteti a gyakorlati tanácsok az Azure SQL Data szinkronizálás.

SQL adatszinkronizálás áttekintését lásd: [adatok szinkronizálásának több felhőalapú és helyszíni adatbázisokat az Azure SQL adatszinkronizálás](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Biztonsága és megbízhatósága

### <a name="client-agent"></a>Ügyfélügynök

-   Az ügyfél-ügynök telepítése a minimális jogosultságokkal rendelkező felhasználói fiókkal, amely a hálózati szolgáltatás hozzáfér.  
-   Az ügyfélügynök telepíthető olyan számítógépre, amely nem a helyszíni SQL Server-számítógépen.  
-   Egy helyi adatbázist nincs regisztrálása egynél több ügynök.    
    -   Ennek elkerülése akkor is, ha más szinkronizálási csoportok számára különböző táblákhoz szinkronizálását végzi.  
    -   A szinkronizálási csoportok törlésekor több ügyfél ügynökök kockázatot kérdés regisztrálása egy helyi adatbázist.

### <a name="database-accounts-with-least-required-privileges"></a>Adatbázis-fiókok legkevesebb szükséges jogosultsággal.

-   **A szinkronizálás beállítása**. Táblázat létrehozása/módosítása; Az ALTER Database; Hozzon létre eljárást; Válassza ki / Alter séma; Felhasználó által definiált típus létrehozásához.

-   **A folyamatban lévő szinkronizálás**. Válassza ki / Insert / frissítése / törli a kijelölt szinkronizálásra, és a szinkronizálási metaadatok, és nyomon követni a táblák; táblák Végrehajtási engedélyt kapjon a tárolt eljárások, a szolgáltatás; Végrehajtási engedélyt kapjon a felhasználó által definiált táblatípusokban.

-   **A megszüntetés**. Az ALTER táblák részére szinkronizálási; Válassza ki, vagy szinkronizálási metaadatok táblákon; törlése Szinkronizálási táblák, a tárolt eljárások és a felhasználó által definiált típusok követési vezérléshez.

Az Azure SQL adatbázis hitelesítő adatai csak egyetlen halmazába támogatja. Ezen feladatokat ennél a határértéknél belül, vegye figyelembe a következő lehetőségeket:

-   Módosítsa a hitelesítő adatokat különböző szakaszokra (például *credentials1* a telepítés és *credentials2* a folyamatban lévő).  
-   A hitelesítő adatok az engedély módosítása (Ez azt jelenti, hogy az engedély módosítása után a szinkronizálás be van állítva).

## <a name="setup"></a>Beállítás

### <a name="database-considerations-and-constraints"></a> Adatbázis használata és korlátozások

#### <a name="sql-database-instance-size"></a>SQL adatbázis-példány mérete

Amikor létrehoz egy új SQL Database-példányt, adja meg a maximális méretét, hogy az mindig nagyobb, mint az adatbázis központi telepítése. Ha nem állít maximális mérete nagyobb, mint a telepített adatbázis, a szinkronizálás sikertelen lesz. Bár SQL adatszinkronizálás automatikus növekedés nem kínál, futtathatja a `ALTER DATABASE` parancs az adatbázis méretének növelése után lett létrehozva. Győződjön meg arról, hogy az SQL-adatbázis mérete példánykorlátozásai belül maradnak.

> [!IMPORTANT]
> SQL-adatok szinkronizálása az egyes adatbázisok további metaadatokat tárol. Fontos számításba ehhez a metaadathoz helyigényt számításakor. Mennyisége hozzáadott terhelést a táblák szélességének kapcsolódik (például keskeny táblák szükséges további terhelés) és az adatforgalom mennyisége.

### <a name="table-considerations-and-constraints"></a> Tábla szempontok és korlátozások

#### <a name="selecting-tables"></a>Táblák kiválasztása

Nem kell egy adatbázis egy szinkronizálási csoportban lévő összes tábla. Olyan táblázatot, amely a szinkronizálási csoportba befoglalni hatással a hatékonyság és költségek. Például a táblázatok, illetve a függenek a, a szinkronizálási csoport csak akkor, ha a üzleti igények működéséhez szükség van azokra.

#### <a name="primary-keys"></a>Az elsődleges kulcsok

A szinkronizálás csoport minden tábla elsődleges kulccsal kell rendelkeznie. Az adatszinkronizálás SQL szolgáltatás nem tudja szinkronizálni a táblázat, amely nem rendelkezik elsődleges kulccsal.

Mielőtt éles SQL adatszinkronizálás használ, a kezdeti és folyamatban lévő szinkronizálás teljesítményének ellenőrzése.

### <a name="provisioning-destination-databases"></a> Cél adatbázisok kiépítése

SQL adatszinkronizálás alapszintű adatbázis autoprovisioning biztosít.

Ez a szakasz ismerteti, amelyek SQL adatszinkronizálás kiépítési vonatkozó korlátozások.

#### <a name="autoprovisioning-limitations"></a>Autoprovisioning korlátozásai

SQL adatszinkronizálás autoprovisioning rendelkezik a következő korlátozások vonatkoznak:

-   Válasszon ki csak a céltábla létrehozott oszlopot.  
    Oszlopot, amely nem része a szinkronizálási csoport a céltábla nem kiépítve.
-   Indexek csak a kijelölt oszlopokban jönnek létre.  
    Ha a forrás tábla indexe, amely nem része a szinkronizálási csoport oszlopok, a céltábla ezen indexek nem kiépítve.  
-   XML típusú oszlopok indexei nem kiépítve.  
-   ELLENŐRZÉSI korlátozásokban nincs kiépítve.  
-   A táblák indítóinak meglévő nincs kiépítve.  
-   Nézetek és tárolt eljárások nem létrehozni az adatbázist.

#### <a name="recommendations"></a>Javaslatok

-   Az SQL adatszinkronizálás autoprovisioning funkció csak akkor használja, a szolgáltatás kimenő próbál.  
-   Termelési környezetben az adatbázisséma kiépítéséhez.

### <a name="locate-hub"></a> Hol található a központi adatbázis

#### <a name="enterprise-to-cloud-scenario"></a>Vállalati felhő forgatókönyv

Késés csökkentése érdekében érdemes a központ adatbázis megközelíti a szinkronizálási csoport adatbázis-forgalom a legnagyobb koncentráció megtartása.

#### <a name="cloud-to-cloud-scenario"></a>Felhő felhő forgatókönyv

-   Ha egy szinkronizálási csoportban lévő összes adatbázis egy adatközpontban, a központ ugyanabban az adatközpontban kell elhelyezni. Ez a konfiguráció csökkenti a késést és az Adatközpont között adatátvitel költségét.
-   Ha az adatbázisok szinkronizálása csoport több adatközpontot, a központ ugyanabban az adatközpontban, a legtöbb adatbázist és adatbázis-forgalom kell elhelyezni.

#### <a name="mixed-scenarios"></a>Vegyes forgatókönyvek

A fenti útmutatás alkalmazása összetett szinkronizálási csoportok beállításai, például azokkal, amelyek a vállalati felhő és a felhő-felhő forgatókönyvek kombinációját.

## <a name="sync"></a>Sync

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Kerülje a lassú és költséges kezdeti szinkronizálás

Ebben a szakaszban arról lesz szó a kezdeti szinkronizálás szinkronizálási csoport. Útmutató: a vártnál tovább tart, és költségesebb, mint a szükséges egy kezdeti szinkronizálást megelőzése érdekében.

#### <a name="how-initial-sync-works"></a>Hogyan kezdeti szinkronizálás működik

A szinkronizálás csoport létrehozásakor csak egy adatbázis kezdődik. Ha több adatbázis az adatokat, SQL adatszinkronizálás minden egyes sorára, fel kell oldani ütközés kezeli. Az ütközések feloldása azt eredményezi, a kezdeti szinkronizálás lassan ugorhat. Ha több adatbázis az adatokat, kezdeti szinkronizálás több napig és több hónap, attól függően, hogy az adatbázis méretének közötti vehet igénybe.

Ha az adatbázisok különböző adatközpontokban, minden egyes sorára között a különböző adatközpontokban kell keresnie. Ez növeli a költségét egy kezdeti szinkronizálást.

#### <a name="recommendation"></a>Ajánlás

Ha lehetséges indítsa el a szinkronizálási csoport adatbázisok csak az egyik az adatokkal.

### <a name="design-to-avoid-synchronization-loops"></a> Tervezési szinkronizálási hurkok elkerülése érdekében

Szinkronizálási ciklust fordul elő, ha a szinkronizálás csoporton belül. a körkörös hivatkozás. Adott esetben egy adatbázis minden módosításakor feldolgozásával a végtelenségig és körkörösen replikálódik a szinkronizálási csoport adatbázisok keresztül.   

Győződjön meg arról, hogy elkerülheti a szinkronizálási ciklusok, mert okozhat teljesítménycsökkenést, és jelentősen növeli a költségeket.

### <a name="handling-changes-that-fail-to-propagate"></a> Propagálása eleget nem tevő változások

#### <a name="reasons-that-changes-fail-to-propagate"></a>Módosítások propagálása eleget nem tevő okok

Módosítások sikertelen propagálására, a következő okok valamelyike lehet:

-   Kompatibilitási séma vagy az adattípus.
-   Beszúrás nem nullázható oszlopban null értékű.
-   Külső kulcsra vonatkozó megkötések megsértése.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Mi történik, ha a módosításokat nem sikerült propagálása?

-   Csoport mutat be, hogy a szinkronizálás egy **figyelmeztetés** állapotát.
-   A portál felhasználói felületének naplófájl-megjelenítő részletei láthatók.
-   Ha a probléma nem hárul 45 napig, az adatbázis méretének elavult.

> [!NOTE]
> Ezek a változások soha nem propagálása. A csak ebben a forgatókönyvben helyreállításához, újra létre kell hoznia a szinkronizálású csoport.

#### <a name="recommendation"></a>Ajánlás

A szinkronizálási csoport és az adatbázis állapotának rendszeresen felületen a portál és a naplófájlok figyeléséhez.


## <a name="maintenance"></a>Karbantartás

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Elavult adatbázisok elkerülése és csoportok szinkronizálása

A szinkronizálás vagy egy adatbázis szinkronizálási csoportban elavult válhat. A sync-csoport állapota esetén **elavult**, hogy nem működik. Ha egy adatbázis állapota van **elavult**, tárolt adatok elvesztésével járhat. A legcélszerűbb elkerülése érdekében ebben a forgatókönyvben helyett a helyreállítás közben.

#### <a name="avoid-out-of-date-databases"></a>Elavult adatbázisok elkerülése érdekében

Az adatbázis állapota **elavult** mikor lett offline 45 nap vagy több. Elkerülése érdekében egy **elavult** állapot adatbázison, gondoskodjon arról, hogy nincs adatbázis kapcsolat nélküli 45 nap vagy több.

#### <a name="avoid-out-of-date-sync-groups"></a>Elavult szinkronizálási csoportok elkerülése érdekében

A szinkronizálás csoport állapota **elavult** Ha bármi is módosul a szinkronizálási csoportban való terjesztése a szinkronizálási csoport többi 45 nap vagy több sikertelen. Elkerülése érdekében egy **elavult** egy szinkronizálású csoport állapotának rendszeresen a naplóban a szinkronizálási csoport előzményeit. Győződjön meg arról, hogy minden ütközések feloldása, és hogy módosítások sikeresen továbbítódnak a csoport adatbázisok szinkronizálása során.

A szinkronizálás csoport lehetséges, hogy nem módosítja az alábbi okok valamelyike miatt:

-   Séma kompatibilitási táblák között.
-   Adatok kompatibilitási táblák között.
-   Null értékű sor beszúrása egy oszlopot, amely nem engedélyezi a null értékeket.
-   A sor frissítése, amely egy külső kulcsmegkötés megsérti értékkel.

Elavult szinkronizálási csoportok megelőzése érdekében:

-   A sémafrissítést az értékeket, amelyeket nem sikerült sorokat tartalmazza.
-   Frissítse az idegen kulcsok értékét nem sikerült sorokat szereplő értékek szerepeljenek.
-   Frissítse a sikertelen sorban levő adatok értékeket, amelyek kompatibilisek a séma vagy a céladatbázis külső kulcsok.

### <a name="avoid-deprovisioning-issues"></a> Kerülje a megszüntetés problémák

Bizonyos esetekben előfordulhat, hogy adatbázis regisztrációját a ügyfél ügynökkel okozhat szinkronizálása sikertelen.

#### <a name="scenario"></a>Forgatókönyv

1. A szinkronizálási csoport egy SQL Database-példányt és egy helyi SQL Server adatbázis, amely helyi ügynök 1 társított használatával lett létrehozva.
2. Ugyanaz a helyi adatbázis helyi ügynök 2 (Ez az ügynök nincs társítva egyetlen szinkronizálási csoport) van regisztrálva.
3. Regisztrációját a helyi adatbázis helyi ügynök 2 eltávolítja a követési és meta táblák a csoportot A helyi adatbázis szinkronizálása.
4. Szinkronizálási csoport egy műveletek sikertelenek, ez a hiba: "az aktuális művelet nem sikerült, mert az adatbázis nincs megadva a szinkronizálás, vagy nincs engedélye a szinkronizálási konfiguráció táblákra."

#### <a name="solution"></a>Megoldás

Ebben a forgatókönyvben elkerülése érdekében nem regisztrálja egy adatbázis több ügynökkel.

Ebben a forgatókönyvben helyreállítás:

1. Az adatbázis eltávolítása minden egyes szinkronizálás csoport tartozik.  
2. Az adatbázis vissza minden, az eltávolított szinkronizálási csoporthoz hozzáadni.  
3. Telepítse minden érintett szinkronizálású csoport (Ez a művelet látja el az adatbázis).  

### <a name="modifying-your-sync-group"></a> A szinkronizálás csoport módosítása

Ne kísérelje meg a adatbázis eltávolítása egy szinkronizálási csoportból, és módosítsa a szinkronizálás csoport első központi telepítése egy változtatás nélkül.

Ehelyett először távolítsa el a adatbázis szinkronizálási csoportból. Ezután a változtatás központi telepítésének, és várjon, amíg befejeződik megszüntetés. Ha megszüntetés befejeződött, a szinkronizálási csoport szerkesztése, és központi telepítése a módosításokat.

Ha egy adatbázis eltávolítása, és szerkessze a egy szinkronizálás csoport első központi telepítése egy változtatás nélkül kísérli meg, egy vagy a másik művelet sikertelen lesz. A portál felület inkonzisztens válhat. Ha ez történik, frissítse a lapot a helyes állapot visszaállításához.

## <a name="next-steps"></a>További lépések
SQL adatszinkronizálás kapcsolatos további információkért lásd:

-   [Több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás az Azure SQL Data Synckel](sql-database-sync-data.md)
-   [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
-   [Az Azure SQL Data Sync monitorozása a Log Analytics használatával](sql-database-sync-monitor-oms.md)
-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)  
-   Teljes PowerShell-példák az SQL Data Sync konfigurálásáról:  
    -   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [Az SQL Data Sync REST API dokumentációjának letöltése](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

SQL-adatbázis kapcsolatos további információkért lásd:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelésére](https://msdn.microsoft.com/library/jj907294.aspx)
