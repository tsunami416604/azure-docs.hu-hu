---
title: Több-bérlős SaaS-minták – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg a követelményeket és a common data több-bérlős szoftver adatarchitektúra-mintázataival mint a szoftverszolgáltatások (SaaS) adatbázis-alkalmazások, amelyek az Azure felhőalapú környezetben futnak.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2775ceb3cf27b6feedfd73cd43855204490ebc31
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471198"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Több-bérlős SaaS-adatbázis bérlős minták

Egy több-bérlős SaaS-alkalmazás tervezésekor alaposan ki kell választania a bérlős modell, amely legjobban megfelel az alkalmazás igényeinek megfelelően.  Bérlős modell meghatározza, hogy hogyan minden egyes bérlő adatai tárolási van leképezve.  A választott bérlős modell hatással van az alkalmazás-tervezés és felügyeleti.  Néha költségesnek számít később átválthat egy másik modellhez.

Ez a cikk ismerteti az alternatív bérlős modell.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-alapelvek és fogalmak

A szoftver, mint a szoftverszolgáltatások (SaaS) modell, a vállalat nem eladni *licencek* a szoftvereket. Ehelyett minden egyes ügyfél lehetővé teszi, hogy a vállalatnál, így minden ügyfél kifizetések bérelhet egy *bérlői* a cég.

Regisztrálásáért bérleti kell fizetnie, minden bérlő az SaaS alkalmazás-összetevők hozzáférést kap, és rendelkezik az adatok tárolva az SaaS-rendszer.

Az előfizetési időszak *bérlős modell* bérlők tárolt adatok hivatkozik:

- *Single-bérlős:*&nbsp; Minden adatbázis csak egyetlen bérlő adatait tárolja.
- *Több-bérlős:*&nbsp; Minden adatbázis tárolja az adatokat külön több bérlő (az adatok védelmét szolgáló mechanizmusok).
- A hibrid bérlős modellek is elérhetők.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. A megfelelő bérlős modell kiválasztása

Általánosságban véve a bérlős modellben nem befolyásolja a függvény egy alkalmazás, de ez valószínűleg hatással van a teljes megoldás más aspektusait.  A következő feltételek segítségével felmérheti az egyes modellek:

- **Méretezhetőség:**
    - Bérlők száma.
    - Tárolási bérlőnkénti.
    - Tárolás az összesítést.
    - Számítási feladatok.

- **Bérlő elszigetelése:**&nbsp; Az adatok elkülönítését és a teljesítmény (akár egy bérlői számítási feladatot befolyásolja mások).

- **Bérlőnkénti költség:**&nbsp; Adatbázis-költségek.

- **Fejlesztési bonyolultsága:**
    - Séma módosításait.
    - Lekérdezések (a minta által igényelt) megváltozik.

- **Üzemeltetés bonyolult:**
    - Teljesítmény figyelése és kezelése.
    - Séma kezelése.
    - Helyreállítás egy bérlőt.
    - Vészhelyreállítás.

- **A testreszabhatóság:**&nbsp; Egyszerű, amelyek vagy séma testreszabásokat alátámasztó bérlőspecifikus vagy osztály-specifikus-bérlőben.

A bérleti vitafórum összpontosít a *adatok* réteg.  Fontolja meg egy kis ideig, de a *alkalmazás* réteg.  Az alkalmazási rétegre egy monolitikus entitás számít.  Ha az alkalmazás sok kis összetevőből, Ön által választott bérlős modell módosulhat.  Néhány összetevőt, mint a többi eltérően sikerült kezelnie egyaránt bérlős és a tárolási technológiát vagy a használt platform.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Egybérlős adatbázis önálló egybérlős alkalmazás

#### <a name="application-level-isolation"></a>Alkalmazás-szintű elkülönítés

Ebben a modellben a teljes alkalmazást egyszer lesz telepítve ismételten, az egyes bérlők számára.  Az alkalmazás minden példánya egy különálló példány, így a soha nem együttműködik más önálló-példánnyal.  Az alkalmazás minden példánya kizárólag egy bérlővel rendelkezik, és ezért a csak egy adatbázis szükséges.  A bérlő rendelkezik az adatbázis összes magát.

![Önálló alkalmazás, és pontosan egy egybérlős adatbázis kialakítása.][image-standalone-app-st-db-111a]

Minden egyes alkalmazás-példány telepítve van egy külön Azure-erőforráscsoportban.  Az erőforráscsoport tartozhatnak olyan előfizetést, amelyet a a szoftvergyártók vagy a bérlő tulajdonában van.  Mindkét esetben a szállító kezelheti a szoftver a bérlő számára.  Egyes alkalmazáspéldányokban történik meg a megfelelő adatbázishoz való csatlakozáshoz.

Minden bérlői adatbázis önálló adatbázisként van telepítve.  Ez a modell el vannak különítve a lehető legnagyobb adatbázis.  Azonban az elkülönítés, hogy elegendő erőforrással kiosztását az egyes adatbázisok a csúcsterhelés kezelésére.  Itt számít a dolog, hogy a rugalmas készletek esetében eltérő erőforráscsoportokban vagy eltérő előfizetésben üzembe helyezett adatbázis nem használható.  Ez a korlátozás az önálló egybérlős alkalmazás model adatbázis teljes költsége szempontból a leginkább költséges megoldás lehetővé teszi.

#### <a name="vendor-management"></a>Szállító felügyeleti

A szállító hozzáférhet minden az önálló app-példányokhoz hasonlóan az összes adatbázist, akkor is, ha az alkalmazáspéldány előfizetést másik bérlőhöz vannak telepítve.  A hozzáférés az SQL-kapcsolatok keresztül érhető el.  A cross-példányok eléréséhez a szállító séma felügyeleti és jelentéskészítési és elemzési célokra adatbázisközi lekérdezés is engedélyezheti.  Ha az ilyen típusú központi kezelésére van szükség, a katalógus kell telepíteni, amely azonosítók bérlői adatbázis URI-k.  Az Azure SQL Database, amellyel és a egy SQL-adatbázist adjon meg egy katalógust a horizontális skálázás kódtár biztosít.  A horizontális skálázás könyvtár hivatalosan neve a [Elastic Database-Ügyfélkódtár][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Több-bérlős alkalmazás bérlőnkénti adatbázis

Ezt a következő mintát használja egy több-bérlős alkalmazás számos adatbázis, az összes folyamatban egybérlős adatbázis.  Új adatbázis minden új bérlő van kiépítve.  Az alkalmazásrétegek erőforrások méretezése pedig ettől *mentése* csomópontonként több erőforrást vertikálisan hozzáadásával.  Az alkalmazás méretezése vagy *ki* további fürtcsomópontok hozzáadásával horizontálisan.  A méretezés a számítási feladatok alapul, és független a számát, vagy az egyes adatbázisok méretezési.

![Tervezés több-bérlős alkalmazás bérlőnkénti adatbázis.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Egy bérlő testreszabása

Például az önálló app-minta egybérlős adatbázis használatát biztosít erős bérlők elkülönítését.  Használatát bármely olyan alkalmazásban, amelynek a modell meghatározza csak egybérlős adatbázis a séma bármilyen egy adott adatbázishoz testre szabható és a bérlői optimalizálva.  Ez a Testreszabás ne érintsen más bérlőket, az alkalmazásban. Egy bérlő esetleg szükség lehet meghaladóan alapszintű adatmezőket, az összes bérlőre van szükség.  További az extra adatmező szükség lehet egy indexet.

Az adatbázis-bérlőnkénti testreszabása a séma egy vagy több, az egyes bérlők számára az egyszerű eléréséhez.  Az alkalmazás gyártójától kell tervezési eljárásokat gondosan kezelheti a séma testreszabások ipari méretekben.

#### <a name="elastic-pools"></a>Rugalmas készletek

Adatbázisok ugyanabban az erőforráscsoportban vannak üzembe helyezve, amikor azok rugalmas készletekbe csoportosíthatók.  A készletek adjon meg egy költséghatékony megoldás az erőforrások számos adatbázis közti megosztását.  A Készlet beállítás akkor lehet elég nagy a használati csúcsok, amely során lép fel, az egyes adatbázisok igénylő olcsóbb.  Annak ellenére, hogy a készletezett adatbázisok osztoznak az erőforrásokhoz való hozzáférés, továbbra is érheti el a magas szintű teljesítmény-elszigetelés érdekében.

![Tervezés több-bérlős alkalmazás-adatbázis – bérlőnként felügyelt, rugalmas készlet használata.][image-mt-app-db-per-tenant-pool-153p]

Az Azure SQL Database konfigurálása, monitoringja és a megosztás kezelése szükséges eszközöket biztosít.  Két készlet-szintű és az adatbázisszintű teljesítmény-mérőszámok az Azure Portalon, és a Log Analytics segítségével érhetők el.  A metrikák biztosíthat nagyszerű betekintést összesítő és a bérlő-specifikus teljesítményét.  Az egyes adatbázisok áthelyezhetők egy adott bérlő lefoglalt erőforrások biztosítása a készletek között.  Ezek az eszközök lehetővé teszik a megfelelő teljesítmény biztosítása költséghatékony módon.

#### <a name="operations-scale-for-database-per-tenant"></a>Műveleteket a bérlőnkénti adatbázis méretezése

Az Azure SQL Database platform rendelkezik számos felügyeleti funkciója, amely a számos adatbázist ipari méretekben, például adatbázisok és több mint 100 000 kezelésére szolgál.  Ezek a funkciók egyértelmű ellenőrizze a bérlőnkénti adatbázis minta.

Tegyük fel, hogy a rendszerben található egy 1000-bérlői adatbázis, a csak egy adatbázisaként.  Előfordulhat, hogy az adatbázis 20 indexeket.  1000 egybérlős adatbázis kellene alakítja a rendszer, ha az indexek mennyisége nő, 20 000.  Az SQL Database részeként [az automatikus hangolás][docu-sql-db-automatic-tuning-771a], az Automatikus indexelés funkciók alapértelmezés szerint engedélyezve vannak.  Automatikus indexelés kezeli az Ön számára minden 20 000 indexek és folyamatos létrehozása és a drop optimalizálást.  Automatizált műveleteknek az egyes adatbázisok fordul elő, és nem koordinált vagy az azokban lévő hasonló műveletek korlátozza.  Automatikus indexelés kezeli az indexek eltérően, mint a kevésbé foglalt adatbázis foglalt adatbázis.  Az ilyen típusú index felügyeleti testreszabási a bérlőnkénti adatbázis méretekben ez lenne, ha ez hatalmas feladat kellett manuálisan hajtható végre.

Más felügyeleti funkciók, amelyek jól méretezhető a következők:

- Beépített biztonsági mentése.
- Magas rendelkezésre állás.
- A lemez titkosítása.
- Teljesítmény-telemetria.

#### <a name="automation"></a>Automation

A felügyeleti műveletek parancsprogrammal létrehozva, és keresztül érhető el egy [fejlesztési és üzemeltetési] [ http-visual-studio-devops-485m] modell.  A műveletek még automatizált és érhető el az alkalmazást a.

Például a időben, sikerült automatizálhatja a egyetlen új bérlő helyreállítása egy korábbi időpontra.  A helyreállítás csak a egy egybérlős adatbázis, amely tárolja a bérlő visszaállítása van szüksége.  A visszaállítás nem befolyásolja a többi bérlő, amely megerősíti, hogy felügyeleti műveleteket minden egyes bérlő finom részletes szintjén vannak-e.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Több-bérlős adatbázisok több-bérlős alkalmazást

Egy másik elérhető egyik számos bérlők egy több-bérlős adatbázisban tárolja.  Az alkalmazás tetszőleges számú több-bérlős adatbázishoz is tartozhat.  A séma egy több-bérlős adatbázis egy vagy több bérlői azonosító oszlop rendelkeznie kell, hogy bármely adott bérlő adatait külön-külön is lekérdezhetők.  A séma, szükség lehet néhány táblákat és oszlopokat, a bérlők csak egy részhalmazát használják.  Statikus kód és a referencia-adatok azonban csak egyszer tárolja, és az összes bérlő osztozik.

#### <a name="tenant-isolation-is-sacrificed"></a>Bérlők elkülönítését elpusztítják van

*Adatok:*&nbsp; Egy több-bérlős adatbázis feltétlenül elpusztítást bérlők elkülönítését.  Az adatok több bérlő több adatbázis együtt tárolódik.  Fejlesztési győződjön meg arról, hogy a lekérdezések soha nem teszi közzé az egynél több bérlő adatait.  Az SQL Database támogatja [sorszintű biztonság][docu-sql-svr-db-row-level-security-947w], amely kényszerítheti, hogy egy lekérdezés által visszaadott adatok egyetlen új bérlő tartozni.

*Feldolgozás:*&nbsp; Egy több-bérlős adatbázis számítási és tárolási erőforrások az összes bérlőre kiterjedő fájlmegosztások.  Az adatbázis teljes intézkedéseket végrehajtása biztosításához figyelhető.  Az Azure rendszerben viszont nem beépített lehet figyelni, vagy az ezek által használt erőforrások egy adott bérlő kezelése rendelkezik.  Ezért a több-bérlős adatbázis végzi a fertőzésnek fokozott mértékben kitett, hajt végre, amikor zajos szomszédok, ahol a számítási feladatok egy overactive bérlő hatással van a teljesítmény biztosítása érdekében a többi bérlő ugyanabban az adatbázisban.  További figyelési alkalmazásszintű sikerült a bérlői szintű teljesítmény figyelése.

#### <a name="lower-cost"></a>Alacsonyabb költségek

Általánosságban véve a több-bérlős adatbázisok a legalacsonyabb bérlőnkénti költség rendelkezik.  Egy önálló adatbázis erőforrás-költségek alacsonyabbak, mint év méretű rugalmas készletek.  Emellett forgatókönyvek, ahol a bérlők csak korlátozott tárolási kell, potenciálisan több millió, a bérlők tárolható egyetlen adatbázisban.  Nem található rugalmas készlet adatbázisok millió is tartalmazhat.  Azonban egy készletenként, 1000-készletekkel rendelkező 1000 adatbázisokat tartalmazó megoldás tudta elérni a méretezési csoport több kockázatára kezelése nehézkessé válik.

Mi a következő, a horizontálisan skálázott több-bérlős modell folyamatban van a leginkább rugalmas és skálázható két változata létezik egy több-bérlős adatbázismodell tárgyalja.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Több-bérlős alkalmazás és a egy több-bérlős adatbázis

A több-bérlős adatbázis legegyszerűbb minta összes bérlőre vonatkozó adatok egyetlen adatbázist használ.  További bérlők hozzáadásakor, az adatbázis vertikális felskálázása további tárolási és számítási erőforrások.  A vertikális felskálázási lehet szükséges, az összes, bár minden esetben van egy ultimate korlát.  Azonban ahhoz, hogy korlátot az adatbázis hosszú kezelése nehézkessé válik.

Felügyeleti műveletek, amelyek az egyes bérlők összpontosítanak összetettebbek, egy több-bérlős adatbázisban megvalósításához.  És ipari méretekben ezeket a műveleteket elfogadhatatlanul válhat.  Ez egy egyetlen bérlő számára az adatok időponthoz visszaállítást.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Több-bérlős alkalmazást több-bérlős szilánkokra osztott adatbázisok

A legtöbb SaaS-alkalmazások egyszerre csak egy bérlő adatainak eléréséhez.  Ez hozzáférést a minta lehetővé teszi, hogy a bérlői adatok több adatbázis között elosztani, vagy szegmensek, ahol minden adatát egy bérlőt az egyik adatszilánkba író szerepel.  Egy több-bérlős adatbázis mintával kombinálva, szilánkokra osztott modell lehetővé teszi, hogy szinte korlátlanul skálázható.

![Tervezés több-bérlős szilánkokra osztott adatbázisok több-bérlős alkalmazás.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>A szegmensek kezelése

A horizontális skálázás bonyolultabbá teszi mind a tervezési és üzemeltetési felügyeletet.  A katalógus szükséges, amelyben a bérlők és adatbázisok közötti leképezést fenntartani.  Emellett felügyeleti eljárások szükségesek a szegmensek és a bérlői népesség kezelése.  Ha például eljárások úgy kell megtervezni hozzáadása és eltávolítása a szegmenseket, és a bérlői adatok szegmensek közötti áthelyezése.  Egy méretezhető módja új szegmensek hozzáadásával és a feltöltődni azt az új bérlők számára.  A többi időszakban, előfordulhat, hogy osztani sűrűn lakott két sűrűn kevésbé lakott szegmensekre.  Után több bérlő áthelyezték vagy megszűnt, ritkásan feltöltött szegmensek együtt lehet egyesíteni.  Az egyesítés további költséghatékony erőforrás-használatot eredményez.  Bérlők közötti egyensúly számítási feladatok is előfordulhat, hogy áthelyezni.

SQL Database biztosít a felosztó/egyesítő eszköz, amely a működik együtt a horizontális skálázás erőforrástár és a katalógus-adatbázisban.  Is feloszthatja az alkalmazás a megadott, és az egyesítési szegmensek és bérlői adatok áthelyezheti a szegmensek között.  Az alkalmazás is fenntartják, ezeket a műveleteket, való megjelölése közben a katalógus érintett bérlők esetén offline előtt helyezni őket.  Az áthelyezés után az alkalmazás frissíti a katalógus újra az új hozzárendelést és a bérlő, online állapotba való megjelölése.

#### <a name="smaller-databases-more-easily-managed"></a>Kisebb adatbázis több könnyen kezelhető

Bérlők több adatbázis közötti elosztásával a horizontálisan skálázott több-bérlős megoldást eredményez, kisebb adatbázisok, könnyebben felügyelt.  Például egy adott bérlő mostantól idő egy korábbi időpontra való visszaállítása magában foglalja egy kisebb adatbázis visszaállítása a biztonsági másolat ahelyett, hogy egy nagyobb adatbázist, amely tartalmazza az összes bérlőre vonatkozóan. Az adatbázis mérete és száma adatbázisonként, bérlők választható ki a számítási feladatok és a felügyeleti erőfeszítéseket elosztása érdekében.

#### <a name="tenant-identifier-in-the-schema"></a>A séma bérlőazonosító

A horizontális skálázás használt módszert, attól függően további korlátozások vethető a az adatbázissémát.  Az SQL Database felosztó/egyesítő alkalmazás megköveteli, hogy a séma tartalmazza a horizontális skálázási kulcs, amely általában a bérlőazonosító.  A bérlőazonosító a kezdő elem a horizontálisan particionált táblák elsődleges kulcsot.  A bérlőazonosító lehetővé teszi, hogy gyorsan keresse meg és a egy adott tenanthoz társított adatok áthelyezése a felosztó/egyesítő alkalmazást.

#### <a name="elastic-pool-for-shards"></a>A szegmensek rugalmas készlet

Rugalmas készletek több-bérlős szilánkokra osztott adatbázisok is kell elhelyezni.  Általában számos egybérlős adatbázis kellene a készlet méretezhető, költséghatékony megoldás, hogy több bérlő több több-bérlős adatbázisok.  Több-bérlős adatbázisok előnyösek, ha nagy számú viszonylag inaktív bérlők számára.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hibrid horizontálisan skálázott több-bérlős adatbázismodell

A hibrid modell minden adatbázis rendelkezik a sémában a bérlőazonosító.  Az adatbázis összes képes több bérlő tárolja, és lehet, hogy az adatbázisok horizontálisan skálázott.  Így a séma az értelemben, azok minden több-bérlős adatbázis.  Még a gyakorlatban egy ezeknek az adatbázisoknak tartalmazza kizárólag egy bérlővel.  Függetlenül attól a bérlők egy adott adatbázisban tárolt mennyisége nem befolyásolja az adatbázissémát.

#### <a name="move-tenants-around"></a>Bérlők bejárása

Bármikor helyezze át egy adott bérlő több-bérlős adatbázis.  És bármikor, meggondolja magát, lépjen vissza a bérlői adatbázis több bérlő tartalmazza.  Is hozzárendelheti a bérlő új egybérlős adatbázis az új adatbázis üzembe helyezésekor.

A hibrid modell, amikor a bérlők azonosítható csoportok erőforrásigényeivel közötti nagy különbségek vannak az ragyogó.  Tegyük fel például, hogy, hogy részt vesz egy ingyenes próbaverzióra bérlők esetén nem garantált az azonos magas teljesítményszintet biztosít, amelyek előfizető bérlők.  A szabályzatot, amely az összes ingyenes próba bérlők között megosztott több-bérlős adatbázisban tárolni az ingyenes próbaverziós fázis lehet a bérlők számára.  Az alapszintű szolgáltatásszinten feliratkozik egy ingyenes próbaverziós bérlőben, amikor a bérlő egy másik, előfordulhat, hogy kevesebb bérlővel rendelkezik, több-bérlős adatbázis helyezheti át.  Olyan előfizetőre, amely a prémium szintű szolgáltatási szinthez tartozó fizet a saját új egybérlős adatbázis sikerült áthelyezni.

#### <a name="pools"></a>Készletek

Ez a hibrid modell, az előfizető bérlők számára az egybérlős adatbázisok helyezhető erőforráskészletek bérlőnkénti adatbázis költségek csökkentése érdekében.  Ezt a bérlőnkénti adatbázis modellt is teheti.

## <a name="i-tenancy-models-compared"></a>I. Bérleti modellek összehasonlítása

A következő táblázat összefoglalja a fő bérlős modell közti különbségekkel.

| Mérés | Önálló alkalmazás | Database-per-tenant | Szilánkokra osztott több-bérlős |
| :---------- | :------------- | :------------------ | :------------------- |
| Méretezés | Közepes<br />1-100 db | Nagyon magas<br />1 – 100 000 egység | Korlátlan<br />1 – 1 000 000 db |
| Bérlők elkülönítését | Nagyon magas | Magas | Alacsony; kivéve bármely egybérlős (ez önmagában egy MT db-ben). |
| Bérlőnkénti adatbázis költség | Magas; a maximális szám méretezik. | Alacsony; a készletek használt. | Legalacsonyabb, a fő Célkiszolgáló adatbázisok kisebb bérlőknek. |
| Alkalmazásteljesítmény-figyelés és kezelés | Bérlőnkénti csak | Összesítés + bérlőnkénti | Összesítés; Bár csak kiszűri a bérlőnkénti van. |
| Fejlesztési bonyolultsága | Alacsony | Alacsony | Közepes; miatt a horizontális skálázás. |
| Üzemeltetés bonyolult | Alacsony magas. Külön-külön egyszerű és összetett ipari méretekben. | Low-Medium. Minták cím összetettségét, ipari méretekben. | Alacsony magas. Az egyes bérlői összetett. |
| &nbsp; ||||

## <a name="next-steps"></a>További lépések

- [Üzembe helyezése és megismerése a bérlőnkénti adatbázis SaaS-modell – Azure SQL Database használó több-bérlős Wingtip alkalmazás][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Üdvözli a Wingtip Tickets SaaS Azure SQL Database bérlős mintaalkalmazás][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Önálló alkalmazás, és pontosan egy egybérlős adatbázis kialakítása."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Tervezés több-bérlős alkalmazás bérlőnkénti adatbázis."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Tervezés több-bérlős alkalmazás-adatbázis – bérlőnként felügyelt, rugalmas készlet használata."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Tervezés több-bérlős szilánkokra osztott adatbázisok több-bérlős alkalmazás."

