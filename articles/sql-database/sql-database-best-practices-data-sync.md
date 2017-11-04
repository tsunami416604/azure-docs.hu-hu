---
title: "Az Azure SQL-adatok szinkronizálása gyakorlati tanácsok |} Microsoft Docs"
description: "Ismerje meg az ajánlott eljárások az Azure SQL adatszinkronizálás fut és konfigurálása"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 6101dfa4bc74acf5045975f6513886fa135fe833
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="best-practices-for-sql-data-sync"></a>Ajánlott eljárások az SQL-adatok szinkronizálása 

Ez a cikk ismerteti a gyakorlati tanácsok az SQL adatszinkronizálás (előzetes verzió).

## <a name="security-and-reliability"></a>Biztonsága és megbízhatósága

### <a name="client-agent"></a>Ügyfélügynök

-   Az ügyfélügynök a legalacsonyabb jogosultsági fiók használata a hálózati hozzáférés telepítéséhez.

-   Célszerű, ha az ügyfél-ügynök telepítve van a számítógép külön, és a helyszíni SQL Server-számítógépen.

-   Egy helyi adatbázist nincs regisztrálása egynél több ügynököt.

-   Akkor is, ha más szinkronizálási csoportok számára különböző táblákhoz szinkronizálása.

-   A szinkronizálási csoportok egyikéhez törlésekor több ügyfél ügynökök kockázatot kérdés regisztrálása egy helyi adatbázist.

### <a name="database-accounts-with-least-required-privilege"></a>Legkevesebb szükséges jogosultsággal rendelkező adatbázis-fiókok

-   **A szinkronizálás beállítása**. Tábla létrehozása/Alter, adatbázis módosítására, létrehozni, jelölje ki / Alter séma, felhasználó által definiált típus létrehozásához.

-   **A folyamatban lévő szinkronizálás**. Válassza ki / Insert / frissítése / törlése szinkronizálásra kijelölt táblák és a szinkronizálási metaadatok, és nyomon követni a táblák, végrehajtási engedélyt kapjon a felhasználó által definiált táblatípusokban vonatkozó Execute engedély szolgáltatás által létrehozott tárolt eljárásokat.

-   **A megszüntetést**. Az ALTER szinkronizálási táblák részen válassza ki, vagy a szinkronizálási metaadat-táblát, Sync figyelemmel kíséri táblák, tárolt eljárások, és felhasználó által definiált típusok követési szinkronizáló vezérlő törlése.

**Hogyan használhatók ezeket az információkat csak egyetlen hitelesítő adatokat a szinkronizálási csoport adatbázisok esetén?**

-   Módosítsa a hitelesítő adatokat különböző szakaszokra (például üzembe helyezési és a credential2 credential1 folyamatos).

-   A hitelesítő adatok az engedély módosítása (Ez azt jelenti, hogy az engedély módosítása után a szinkronizálás be van állítva).

## <a name="locate-hub"></a>Hol található a központi adatbázis

### <a name="enterprise-to-cloud-scenario"></a>Vállalati felhő forgatókönyv

Késés csökkentése érdekében érdemes a központ adatbázis megközelíti a szinkronizálási csoport adatbázis-forgalom a legnagyobb koncentráció megtartása.

### <a name="cloud-to-cloud-scenario"></a>Felhő felhő forgatókönyv

-   Ha egy szinkronizálási csoportban lévő összes adatbázis egy adatközpontban, a központ ugyanabban az adatközpontban kell elhelyezni. Ez a konfiguráció csökkenti a késést és az Adatközpont közti adatátvitel költségét.

-   Ha az adatbázisok szinkronizálása csoport több különböző adatközponthoz, a központ az azonos adatközpontba, ahol az adatbázisok és az adatbázis-forgalom a legtöbb kell elhelyezni.

### <a name="mixed-scenarios"></a>Vegyes forgatókönyvek

A fenti útmutatás összetettebb szinkronizálási csoportok beállításai érvényesek.

## <a name="database-considerations-and-constraints"></a>Adatbázis használata és korlátozások

### <a name="sql-database-instance-size"></a>SQL adatbázis-példány mérete

Amikor létrehoz egy új SQL Database-példányt, adja meg a maximális méretét, hogy az mindig nagyobb, mint az adatbázis központi telepítése. Ha nincs megadva a maximális mérete nagyobb, mint a telepített adatbázis, a szinkronizálás sikertelen. Nincs automatikus növekedés - pedig az adatbázis méretének növeléséhez a létrehozásuk után egy ALTER DATABASE teheti meg. Ellenőrizze, hogy az SQL-adatbázis példány mérete határértékek maradhat.

> [!IMPORTANT]
> SQL-adatok szinkronizálása az egyes adatbázisok további metaadatokat tárol. Győződjön meg arról, hogy figyelembe vegye a metaadatok helyigényt számításakor. Mennyisége hozzáadott terhelés szabályozza a táblák szélességét (például keskeny táblák szükséges további terhelés) és az adatforgalom mennyisége.

## <a name="table-considerations-and-constraints"></a>Tábla szempontok és korlátozások

### <a name="selecting-tables"></a>Táblák kiválasztása

Egy adatbázis nem minden táblája kell lennie egy [szinkronizálású csoport](#sync-group). A kijelölés egy szinkronizálási csoportba felveendő táblák, és amelyek kizárásához (vagy egy másik szinkronizálási csoportba felveendő) befolyásolhatja a hatékonyság és költségek. Csak azokat a táblákat vegye fel egy szinkronizálási csoportot, hogy az üzleti igények igény szerint és a tábla, amelyre függenek.

### <a name="primary-keys"></a>Az elsődleges kulcsok

A szinkronizálás csoport minden tábla elsődleges kulccsal kell rendelkeznie. Az SQL adatszinkronizálás (előzetes verzió) szolgáltatás nem tudja szinkronizálni a táblázat, amely nem rendelkezik elsődleges kulccsal.

Mielőtt éles környezetben, a kezdeti és folyamatos szinkronizálás teljesítményének ellenőrzése.

## <a name="provisioning-destination-databases"></a>Cél adatbázisok kiépítése

Előzetes SQL adatszinkronizálás (előzetes verzió) biztosít alapszintű adatbázis automatikus átadásának.

Ez a szakasz ismerteti, amelyek az SQL adatszinkronizálás vonatkozó korlátozások (előzetes verzió) tartozó kiépítés.

### <a name="auto-provisioning-limitations"></a>Automatikus kiépítés korlátozásai

A következő SQL-adatszinkronizálás (előzetes verzió) automatikus kiépítés korlátozások is.

-   Csak a kijelölt oszlop a céltáblában jönnek létre.
Így ha valamelyik oszlop nem a szinkronizálási csoport része ezek az oszlopok nem törlődnek a céltáblák.

-   Indexek csak a kijelölt oszlopok jön létre.
Ha a forrás tábla indexéből oszlopokat, amelyek nem része a szinkronizálási csoportnak ezen indexek nem törlődnek a céltábla.

-   XML típusú oszlopok indexei nem törlődnek.

-   ELLENŐRZÉSI korlátozásokban nem törlődnek.

-   A forrás táblák meglévő eseményindítók nem törlődnek.

-   Nézetek és tárolt eljárások nem jönnek létre az adatbázist.

### <a name="recommendations"></a>Javaslatok

-   Az Automatikus kiépítés funkció használata csak a közben a szolgáltatás.

-   Termelési környezetben az adatbázisséma kell kiépíteni.

## <a name="avoid-a-slow-and-costly-initial-synchronization"></a>A lassú és költséges kezdeti szinkronizálás elkerülése érdekében

Ez a szakasz ismerteti a kezdeti szinkronizálás szinkronizálási csoport és mit tehet egy kezdeti szinkronizálást a vártnál szükséges és költségszámítás legyen, vagy egynél elkerülése érdekében.

### <a name="how-initial-synchronization-works"></a>Hogyan kezdeti szinkronizálás működik

A szinkronizálás csoport létrehozásakor csak egy adatbázis kezdődik. Ha több adatbázis az adatokat, SQL adatszinkronizálás (előzetes verzió) minden egyes sorára, amelyet a névfeloldási ütközés kezeli. Az ütközések feloldása hatására lassan, nyissa meg a kezdeti szinkronizálás több hónap, az adatbázis méretétől függően több napig véve.

Továbbá ha az adatbázisok különböző adatközpontokban, kezdeti szinkronizálás járó költségek magasabbak szükséges, mivel minden egyes sorára között a különböző adatközpontokban kell keresnie.

### <a name="recommendation"></a>Ajánlás

Amikor lehetséges indítsa el a szinkronizálási csoport adatbázisok csak az egyik az adatokkal.

## <a name="design-to-avoid-synchronization-loops"></a>Tervezési szinkronizálási hurkok elkerülése érdekében

Szinkronizálási ciklust az eredménye, ha a szinkronizálás csoporton belül. a körkörös hivatkozások, hogy egy adatbázis minden változást a szinkronizálási csoport adatbázisok keresztül van replikálva, körkörösen és feldolgozásával a végtelenségig. El szeretné kerülni a szinkronizálási ciklusok, csökkentheti a teljesítményt, és jelentősen növelheti a költségeket.

## <a name="avoid-out-of-date-databases-and-sync-groups"></a>Elavult adatbázisok elkerülése és csoportok szinkronizálása

A szinkronizálás vagy egy adatbázis szinkronizálási csoporton belül elavult válhat. Ha a szinkronizálás csoport állapota "elavult", nem működik. Ha egy adatbázis állapota "elavult", adat elveszhet. A legcélszerűbb elkerülése érdekében az ezekben a helyzetekben ahelyett, hogy őket kijavítani.

### <a name="avoid-out-of-date-databases"></a>Elavult adatbázisok elkerülése érdekében

Egy adatbázis állapota értékűre elavult 45 nap vagy több offline le van. Kerülje a elavult állapot adatbázis biztosításával, hogy az adatbázis legyen offline 45 nap vagy több.

### <a name="avoid-out-of-date-sync-groups"></a>Elavult szinkronizálási csoportok elkerülése érdekében

A szinkronizálási csoport állapotának értéke elavult Ha bármi is módosul a szinkronizálási csoporton belüli propagálása a szinkronizálási csoport többi 45 nap vagy több nem sikerült. A szinkronizálás csoport előzmények naplót rendszeresen ellenőrzésével elkerülése érdekében a szinkronizálási csoporton elavult állapot. Győződjön meg arról, hogy minden ütközések feloldása, és a csoport adatbázisok szinkronizálása során sikeresen propagál változást.

A szinkronizálás csoport sikertelenek lehetnek a módosítás alkalmazására okai:

-   Séma kompatibilitási táblák között.

-   Adatok kompatibilitási táblák között.

-   Egy oszlop, amely nem engedélyezi null értékek beszúrása egy sor null értékű.

-   A sor frissítése, amely egy külső kulcsmegkötés megsérti értékkel.

Megakadályozhatja, hogy elavult szinkronizálási csoportok szerint:

-   A sémafrissítést sikertelen sorokat szereplő értékeket.

-   Frissítse az idegen kulcs értékeket a sikertelen sorok szereplő értékeket tartalmazza.

-   Frissítse az adatértékek sikertelen sorában való kompatibilitás érdekében a séma vagy a céladatbázis külső kulcsokat.

## <a name="avoid-deprovisioning-issues"></a>Kerülje a megszüntetés problémák

Bizonyos körülmények beállításjegyzékből való törlésekor az adatbázis egy ügyfél-ügynökkel rendelkező okozhat a szinkronizálás sikertelen.

### <a name="scenario"></a>Forgatókönyv

1. A szinkronizálási csoport egy SQL Database-példányt és egy helyi SQL Server adatbázis, amely helyi ügynök 1 társított hozták létre.

2. Ugyanaz a helyi adatbázis helyi ügynök 2 (Ez az ügynök nincs társítva egyetlen szinkronizálási csoport) van regisztrálva.

3. A nyomon követési/meta táblák regisztrációját a helyi adatbázis helyi ügynök 2 eltávolítja a szinkronizálási csoport a helyi adatbázis.

4. Most a szinkronizálási csoport A művelet a következő hiba miatt sikertelen: "az aktuális művelet nem sikerült, mert az adatbázis nincs megadva a szinkronizálás, vagy nincs engedélye a szinkronizálási konfiguráció táblákra."

### <a name="solution"></a>Megoldás

A helyzet elkerülését teljesen regisztrálásával soha nem egy adatbázis több ügynökkel.

Ez a helyzet helyreállítás:

1. Az adatbázis eltávolítása minden egyes szinkronizálás csoport tartozik.

2. Az adatbázis vissza minden csak eltávolította a szinkronizálási csoporthoz hozzáadni.

3. Telepítse minden érintett szinkronizálású csoport (amely látja el az adatbázis).

## <a name="handling-changes-that-fail-to-propagate"></a>Propagálása eleget nem tevő változások kezelése

### <a name="reasons-that-changes-fail-to-propagate"></a>Módosítások propagálása eleget nem tevő okok

Módosítások sikertelen propagálása miatt számos oka lehet. Néhány oka a következő lesz:

-   Kompatibilitási séma vagy az adattípus.

-   A rendszer próbál beszúrni null, nem nullázható oszlopban.

-   Külső kulcsra vonatkozó megkötések megsértése.

### <a name="what-happens-when-changes-fail-to-propagate"></a>Mi történik, ha a módosításokat nem sikerült propagálása?

-   Szinkronizálási csoport jeleníti meg, egy figyelmeztetés állapotban van.

-   Részletek szerepelnek, a portál felhasználói felületének naplófájl-megjelenítő.

-   Ha a probléma nem hárul 45 napig, az adatbázis méretének elavult.

> [!NOTE]
> Ezek a változások soha nem propagálása. A helyreállítás egyetlen módja, a szinkronizálási csoport újbóli létrehozása.

### <a name="recommendation"></a>Ajánlás

A portál és a naplófájlok felületen keresztül rendszeresen szinkronizálású csoport és az adatbázis állapotának figyelése.

## <a name="modifying-your-sync-group"></a>A szinkronizálás csoport módosítása

Ne távolítsa el az adatbázis egy szinkronizálási csoportból, és szerkessze a szinkronizálási csoport első központi telepítése egy változtatás nélkül.

Először távolítsa el az adatbázis egy szinkronizálási csoportból. Ezután a változtatás központi telepítésének, és várjon, amíg befejeződik megszüntetést. Ez a művelet befejezése után a szinkronizálási csoport szerkesztéséhez, és telepítheti a módosításokat.

Ha egy adatbázis eltávolítása, és szerkessze a szinkronizálási csoport első telepítése nélkül kísérli meg a módosításokat, egy vagy a másik művelet sikertelen lesz, és a portál felület kaphat inkonzisztens állapotba kerülnek. Ebben az esetben a megfelelő állapot visszaállításához a lap frissítésével.
