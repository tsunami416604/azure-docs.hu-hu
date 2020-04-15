---
title: Ajánlott eljárások az adatszinkronizáláshoz
description: Ismerje meg az Azure SQL Data Sync konfigurálásának és futtatásának gyakorlati tanácsait.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 2b72d52463164c2a059fce316cc11a63aad62e7c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380948"
---
# <a name="best-practices-for-sql-data-sync"></a>Az SQL Data Synchez ajánlott eljárások 

Ez a cikk az Azure SQL Data Sync ajánlott eljárásokat ismerteti.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

> [!IMPORTANT]
> Az Azure SQL Data Sync jelenleg **nem** támogatja az Azure SQL Database felügyelt példánya.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Biztonság és megbízhatóság

### <a name="client-agent"></a>Ügyfélügynök

-   Telepítse az ügyfélügynököt a hálózati szolgáltatáshoz hozzáféréssel rendelkező legkevésbé kiemelt jogosultságú felhasználói fiók használatával.  
-   Telepítse az ügyfélügynököt olyan számítógépre, amely nem a helyszíni SQL Server-számítógép.  
-   Ne regisztráljon egy helyszíni adatbázist egynél több ügynökkel.    
    -   Ezt akkor is kerülje el, ha különböző táblákat szinkronizál különböző szinkronizálási csoportokhoz.  
    -   A helyszíni adatbázis regisztrálása több ügyfélügynökök kihívást jelent, ha törli az egyik szinkronizálási csoportok.

### <a name="database-accounts-with-least-required-privileges"></a>A legkevésbé szükséges jogosultságokkal rendelkező adatbázisfiókok

-   **Szinkronizálási beállításhoz.** Tábla létrehozása/módosítása; Adatbázis módosítása; Eljárás létrehozása; Séma kiválasztása/ módosítása; Felhasználó által definiált típus létrehozása.

-   **A folyamatos szinkronizáláshoz.** Válassza ki/ Beszúrás/ Frissítés/ Törlés a szinkronizálásra kijelölt táblákon, valamint a szinkronizálási metaadatokon és a követési táblákon; A szolgáltatás által létrehozott tárolt eljárásokra vonatkozó engedély végrehajtása; Engedély végrehajtása a felhasználó által definiált táblatípusokon.

-   **A megszüntetéshez.** A szinkronizálás részét képezi a táblákon; Kijelölés/ törlés szinkronizálási metaadattáblákon; A szinkronizáláskövetési táblák, a tárolt eljárások és a felhasználó által definiált típusok vezérlése.

Az Azure SQL Database csak egyetlen hitelesítő adatokat támogat. Ha ezeket a feladatokat ezen a korláton belül szeretné elvégezni, vegye figyelembe a következő lehetőségeket:

-   Módosítsa a különböző fázisok hitelesítő adatait (például a *telepítéshez szükséges hitelesítő adatok1 és* a folyamatban lévő *hitelesítő adatok2).*  
-   Módosítsa a hitelesítő adatok engedélyét (azaz módosítsa az engedélyt a szinkronizálás beállítása után).

## <a name="setup"></a>Telepítés

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Adatbázis-szempontok és -megkötések

#### <a name="sql-database-instance-size"></a>SQL-adatbázis példányának mérete

Új SQL Database-példány létrehozásakor állítsa be a maximális méretet úgy, hogy az mindig nagyobb legyen, mint a telepített adatbázis. Ha nem állítja a maximális méretet a telepített adatbázisnál nagyobbra, a szinkronizálás sikertelen lesz. Bár az SQL Data Sync nem kínál automatikus `ALTER DATABASE` növekedést, futtathatja a parancsot az adatbázis méretének növeléséhez a létrehozása után. Győződjön meg arról, hogy az SQL Database-példány méretkorlátain belül marad.

> [!IMPORTANT]
> Az SQL Data Sync további metaadatokat tárol az egyes adatbázisokban. Győződjön meg arról, hogy figyelembe veszi ezeket a metaadatokat, amikor kiszámítja a szükséges helyet. A többletterhelés mértéke a táblák szélességéhez (például a keskeny táblák több rezsitöbbletet igényelnek) és a forgalom mennyiségéhez kapcsolódik.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>A táblázatokkal kapcsolatos szempontok és korlátozások

#### <a name="selecting-tables"></a>Táblák kijelölése

Nem kell az összes táblát egy szinkronizálási csoportban lévő adatbázisban szerepelni. A szinkronizálási csoportban lévő táblák befolyásolják a hatékonyságot és a költségeket. A táblákat és azokat a táblákat, amelyektől függenek, csak akkor vegyen fel egy szinkronizálási csoportba, ha azt üzleti igények megkövetelik.

#### <a name="primary-keys"></a>Elsődleges kulcsok

A szinkronizálási csoport minden táblájának elsődleges kulccsal kell rendelkeznie. Az SQL Data Sync szolgáltatás nem tudja szinkronizálni az elsődleges kulccsal nem rendelkező táblát.

Az SQL Data Sync éles környezetben való használata előtt tesztelje a kezdeti és a folyamatos szinkronizálási teljesítményt.

#### <a name="empty-tables-provide-the-best-performance"></a>Az üres asztalok biztosítják a legjobb teljesítményt

Az üres táblák biztosítják a legjobb teljesítményt az inicializálás időpontjában. Ha a céltábla üres, az Adatszinkronizálás tömeges beszúrással tölti be az adatokat. Ellenkező esetben a Data Sync soronkénti összehasonlítást és beszúrást végez az ütközések ellenőrzéséhez. Ha azonban a teljesítmény nem jelent problémát, beállíthatja az adatokat már tartalmazó táblák közötti szinkronizálást.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Céladatbázisok kiépítése

Az SQL Data Sync alapvető adatbázis-kiépítést biztosít.

Ez a szakasz ismerteti az SQL Data Sync kiépítés korlátait.

#### <a name="autoprovisioning-limitations"></a>Automatikus kiépítési korlátozások

Az SQL Data Sync a következő korlátozásokkal rendelkezik az automatikus kiépítéshez:

-   Csak a céltáblában létrehozott oszlopokat jelölje ki. A szinkronizálási csoportban nem található oszlopok nincsenek kiépítve a céltáblákban.
-   Az indexek csak a kijelölt oszlopokhoz jönnek létre. Ha a forrástábla indexe olyan oszlopokat tartalmaz, amelyek nem részei a szinkronizálási csoportnak, ezek az indexek nincsenek kiépítve a céltáblákban.  
-   Az XML-típusoszlopok indexei nincsenek kiépítve.  
-   Check megkötések nincsenek kiépítve.  
-   A forrástáblák meglévő eseményindítói nincsenek kiépítve.  
-   A nézetek és a tárolt eljárások nem jönnek létre a céladatbázisban.
-   FRISSÍTÉSKOR A KASZKÁD ÉS A DELETE KASZKÁD-műveletek az idegen kulcs megkötésein nem jönnek létre újra a céltáblákban.
-   Ha 28-nál nagyobb pontosságú decimális vagy numerikus oszlopokkal rendelkezik, az SQL Data Sync konverziós túlcsordulási problémát tapasztalhat a szinkronizálás során. Javasoljuk, hogy a tizedes vagy numerikus oszlopok pontosságát legfeljebb 28-ra korlátozza.

#### <a name="recommendations"></a>Javaslatok

-   Csak akkor használja az SQL Data Sync automatikus kiépítését, amikor kipróbálja a szolgáltatást.  
-   Az éles környezetben az adatbázisséma kiépítése.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>A központi adatbázis helye

#### <a name="enterprise-to-cloud-scenario"></a>Nagyvállalati felhőalapú forgatókönyv

A késés minimalizálása érdekében tartsa a központi adatbázist a szinkronizálási csoport adatbázis-forgalmának legnagyobb koncentrációjához közel.

#### <a name="cloud-to-cloud-scenario"></a>Felhőből felhőbe irányuló forgatókönyv

-   Ha egy szinkronizálási csoport összes adatbázisa egy adatközpontban található, a hubnak ugyanabban az adatközpontban kell lennie. Ez a konfiguráció csökkenti a késést és az adatközpontok közötti adatátvitel költségét.
-   Ha egy szinkronizálási csoportban lévő adatbázisok több adatközpontban találhatók, a hubnak ugyanabban az adatközpontban kell lennie, mint az adatbázisok és az adatbázis-forgalom többségének.

#### <a name="mixed-scenarios"></a>Vegyes forgatókönyvek

Az előző irányelveket összetett szinkronizálási csoportkonfigurációkra alkalmazza, például a nagyvállalati és felhőalapú és felhőalapú forgatókönyvek keverékére.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Kerülje a lassú és költséges kezdeti szinkronizálást

Ebben a szakaszban egy szinkronizálási csoport kezdeti szinkronizálását tárgyaljuk. Ismerje meg, hogyan akadályozhatja meg, hogy a kezdeti szinkronizálás hosszabb és a szükségesnél költségesebb legyen.

#### <a name="how-initial-sync-works"></a>A kezdeti szinkronizálás működése

Szinkronizálási csoport létrehozásakor csak egy adatbázisban lévő adatokkal kezdje. Ha több adatbázisban is vannak adatok, az SQL Data Sync minden sort feloldandó ütközésként kezel. Ez az ütközésfeloldás a kezdeti szinkronizálás lassú dulaktizálását eredményezi. Ha több adatbázisban is vannak adatok, a kezdeti szinkronizálás az adatbázis méretétől függően több napot és több hónapot is igénybe vehet.

Ha az adatbázisok különböző adatközpontokban találhatók, minden sornak a különböző adatközpontok között kell haladnia. Ez növeli a kezdeti szinkronizálás költségét.

#### <a name="recommendation"></a>Ajánlás

Ha lehetséges, csak a szinkronizálási csoport adatbázisainak egyikében kezdje az adatokkal.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>A szinkronizálási hurkok elkerülésére tervezett tervezés

A szinkronizálási ciklus akkor fordul elő, ha körkörös hivatkozások vannak egy szinkronizálási csoporton belül. Ebben az esetben az adatbázis minden egyes módosítása végtelenül és körkörösen replikálódik a szinkronizálási csoport ban lévő adatbázisokon keresztül.   

Győződjön meg arról, hogy elkerüli a szinkronizálási hurkokat, mert teljesítménycsökkenést okoznak, és jelentősen növelhetik a költségeket.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>A propagálást nem okozó módosítások

#### <a name="reasons-that-changes-fail-to-propagate"></a>A módosítások propagálásának okai

Előfordulhat, hogy a módosítások propagálása a következő okok valamelyike miatt sikertelen:

-   Séma/adattípus inkompatibilitás.
-   Null beszúrása nem nullázható oszlopokba.
-   Az idegen kulcs megkötéseinek megsértése.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Mi történik, ha a módosítások propagálása sikertelen?

-   A szinkronizálási csoport azt mutatja, hogy **figyelmeztetési** állapotban van.
-   A részletek a portál felhasználói felületének naplómegjelenítőjében találhatók.
-   Ha a probléma 45 napig nem oldódik meg, az adatbázis elavulttá válik.

> [!NOTE]
> Ezek a változások soha nem terjednek. Ebben a forgatókönyvben csak úgy állítható helyre, ha újra létrehozza a szinkronizálási csoportot.

#### <a name="recommendation"></a>Ajánlás

Rendszeresen figyelje a szinkronizálási csoport és az adatbázis állapotát a portálon és a naplófelületen keresztül.


## <a name="maintenance"></a>Karbantartás

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Az elavult adatbázisok és szinkronizálási csoportok elkerülése

Egy szinkronizálási csoport vagy egy adatbázis elavulttá válhat. Ha egy szinkronizálási csoport állapota **elavult,** leáll. Ha egy adatbázis állapota **elavult,** adatok elveszhetnek. A legjobb, ha elkerüli ezt a forgatókönyvet, ahelyett, hogy megpróbálna felépülni belőle.

#### <a name="avoid-out-of-date-databases"></a>Az elavult adatbázisok elkerülése

Az adatbázis állapota **elavult,** ha 45 napig vagy annál több napja offline állapotban van. Az adatbázis **elavult állapotának** elkerülése érdekében győződjön meg arról, hogy az adatbázisok egyike sem offline állapotban van 45 napig vagy tovább.

#### <a name="avoid-out-of-date-sync-groups"></a>Az elavult szinkronizálási csoportok elkerülése

A szinkronizálási csoport állapota **elavult,** ha a szinkronizálási csoport bármely módosítása 45 napig vagy tovább nem terjed tovább a szinkronizálási csoport többi részére. A szinkronizálási csoport **elavult állapotának** elkerülése érdekében rendszeresen ellenőrizze a szinkronizálási csoport előzménynaplóját. Győződjön meg arról, hogy az összes ütközés fel van oldva, és hogy a módosítások sikeresen propagálva vannak a szinkronizálási csoport adatbázisaiban.

Előfordulhat, hogy egy szinkronizálási csoport nem alkalmazza a módosítást a következő okok valamelyike miatt:

-   Séma inkompatibilitás a táblák között.
-   A táblák közötti adatinkompatibilitás.
-   Null értékű sor beszúrása olyan oszlopba, amely nem engedélyezi a null értékeket.
-   Egy olyan sor frissítése, amelynek értéke sérti az idegenkulcs-megkötést.

Az elavult szinkronizálási csoportok megelőzése:

-   Frissítse a sémát, hogy engedélyezze a sikertelen sorokban lévő értékeket.
-   Frissítse az idegen kulcs értékeit úgy, hogy azok tartalmazzák a sikertelen sorokban lévő értékeket.
-   Frissítse a sikertelen sorban lévő adatértékeket, hogy azok kompatibilisek legyenek a céladatbázis sémájával vagy idegen kulcsaival.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>A megszüntetési problémák elkerülése

Bizonyos körülmények között az adatbázis ügyfélügynökkel való regisztrációjának megszüntetése a szinkronizálás sikertelensítését okozhatja.

#### <a name="scenario"></a>Forgatókönyv

1. Az A szinkronizálási csoport egy SQL Database-példány és egy helyszíni SQL Server-adatbázis használatával jött létre, amely az 1.
2. Ugyanaz a helyszíni adatbázis regisztrálva van a helyi 2-es ügynökkel (ez az ügynök nincs egyetlen szinkronizálási csoporthoz társítva sem).
3. A helyszíni adatbázis helyi ügynök2-ről való regisztrációjának megszüntetése eltávolítja a helyszíni adatbázis A szinkronizálási csoportjának követési és metatábláit.
4. Az A csoport műveletei sikertelenek a következő hibával: "Az aktuális művelet nem hajtható végre, mert az adatbázis nincs kiépítve szinkronizálásra, vagy nincs engedélye a szinkronizálási konfigurációs táblákhoz."

#### <a name="solution"></a>Megoldás

Ennek elkerülése érdekében ne regisztráljon egy adatbázist egynél több ügynökkel.

A forgatókönyv helyreállítása:

1. Távolítsa el az adatbázist minden olyan szinkronizálási csoportból, amelyhez tartozik.  
2. Adja hozzá újra az adatbázist minden olyan szinkronizálási csoporthoz, amelyből eltávolította.  
3. Telepítse az egyes érintett szinkronizálási csoportokat (ez a művelet az adatbázist helyezi).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Szinkronizálási csoport módosítása

Ne kísérelje meg eltávolítani az adatbázist egy szinkronizálási csoportból, majd szerkessze a szinkronizálási csoportot anélkül, hogy először üzembe helyezne egy módosítást.

Ehelyett először távolítsa el az adatbázist egy szinkronizálási csoportból. Ezután telepítse a módosítást, és várja meg a megszüntetés befejezését. Ha a megszüntetés befejeződött, szerkesztheti a szinkronizálási csoportot, és telepítheti a módosításokat.

Ha megpróbál eltávolítani egy adatbázist, majd módosítani egy szinkronizálási csoportot anélkül, hogy először telepítené az egyik módosítást, az egyik vagy a másik művelet sikertelen lesz. A portálfelület inkonzisztenssé válhat. Ebben az esetben frissítse a lapot a megfelelő állapot visszaállításához.

### <a name="avoid-schema-refresh-timeout"></a>A sémafrissítési időmeghosszabbítás elkerülése

Ha egy összetett sémát szinkronizálni, előfordulhat, hogy egy "működési időout" a séma frissítése során, ha a szinkronizálási metaadat-adatbázis alacsonyabb Termékváltozat (például: alapszintű). 

#### <a name="solution"></a>Megoldás

A probléma enyhítése érdekében a szinkronizálási metaadat-adatbázis felskálázása magasabb termékváltozat, például S3. 

## <a name="next-steps"></a>További lépések
Az SQL Data Sync programról további információt a következő témakörben talál:

-   Áttekintés – [Adatok szinkronizálása több felhőbeli és helyszíni adatbázisban az Azure SQL Data Sync segítségével](sql-database-sync-data.md)
-   Adatszinkronizálás beállítása
    - A portálon – [Oktatóanyag: Az SQL Data Sync beállítása az Adatok szinkronizálásához az Azure SQL Database és a helyszíni SQL Server között](sql-database-get-started-sql-data-sync.md)
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatszinkronizálási ügynök – [Adatszinkronizálási ügynök az Azure SQL Data Sync szolgáltatáshoz](sql-database-data-sync-agent.md)
-   Figyelő – [SQL-adatszinkronizálás figyelése az Azure Figyelő naplóival](sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [Az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   A szinkronizálási séma frissítése
    -   Transact-SQL - [A sémamódosítások replikációjának automatizálása az Azure SQL Data Sync-ben](sql-database-update-sync-schema.md)
    -   A PowerShell használatával – [A PowerShell használatával frissítse a szinkronizálási sémát egy meglévő szinkronizálási csoportban](scripts/sql-database-sync-update-schema.md)

Az SQL Database adatbázisról további információt a következő témakörben talál:

-   [SQL-adatbázis – áttekintés](sql-database-technical-overview.md)
-   [Adatbázis életciklusának kezelése](https://msdn.microsoft.com/library/jj907294.aspx)
