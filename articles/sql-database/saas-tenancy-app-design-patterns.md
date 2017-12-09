---
title: "Több-bérlős Szolgáltatottszoftver-minták - Azure SQL Database |} Microsoft Docs"
description: "Ismerje meg és a közös adatok architektúra minták több-bérlős szoftver, egy szolgáltatott szoftverként (SaaS) adatbázis-alkalmazások, amelyek az Azure felhőalapú környezetben futnak."
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen,srinia
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: billgib
ms.openlocfilehash: 1b6c780000d8c5e31a78f7f83ae74c002e8f8349
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Több-bérlős SaaS adatbázis bérleti minták

Egy több-bérlős SaaS-alkalmazáshoz tervezésekor ki kell választania a bérlőhöz modellt, amely az alkalmazás igényeinek legjobban megfelelő gondosan.  Egy bérlős modell határozza meg, hogyan mindegyik bérlő adatok tárolására van leképezve.  A választott bérlős modell hatással van az alkalmazás tervezési és kezelése.  Egyes esetekben költséges később átvált egy másik modellhez.

A következő alternatív bérleti modellek tárgyalja.

## <a name="a-how-to-choose-the-appropriate-tenancy-model"></a>A. A megfelelő bérlős modell kiválasztása

Általában a vállalat kiszolgálása modell nem befolyásolja a függvény egy alkalmazás, de ez valószínűleg hatással van a teljes megoldás más aspektusait.  Az alábbi feltételek segítségével mérheti fel minden egyes modellek:

- **Méretezhetőség:**
    - Bérlők száma.
    - Tárolási /-bérlő.
    - Tárolási összesítés.
    - Munkaterhelés.

- **Bérlők elkülönítésének:** &nbsp; adatok elkülönítési és a teljesítmény (hogy egy bérlő munkaterhelés hatással van mások).

- **Egy bérlő költség:** &nbsp; -adatbázis költségeit.

- **Fejlesztési összetettsége:**
    - Séma módosítása.
    - A módosítások lekérdezések (szükséges minta).

- **Működési összetettsége:**
    - Figyelés és a teljesítmény kezelése.
    - Séma kezelése.
    - A bérlő visszaállítását.
    - Vészhelyreállítás.

- **Testreszabhatóság miatt:** &nbsp; könnyű séma egyéni beállításokat, amelyek vagy támogató vonatkozó bérlői vagy bérlői osztály-specifikus.

A bérleti vitafórum arra irányul, hogy a *adatok* réteg.  Érdemes lehet egy kis ideig, de a *alkalmazás* réteg.  Az alkalmazási rétegre rendszer egységes egységként kezeli.  Ha a sok kisméretű összetevők alkalmazást, a választott bérlős modell változhatnak.  Néhány összetevőt működnek, mint a többi sikerült kezelni is bérlős és a tárolótechnológián vagy a használt platformra.

## <a name="b-standalone-single-tenant-app-with-single-tenant-database"></a>B. Önálló single-bérlő alkalmazás egyetlen-bérlő adatbázis

#### <a name="application-level-isolation"></a>Alkalmazáselszigetelés szint

Ebben a modellben a teljes alkalmazás egyszer lesz telepítve ismételten, az egyes bérlők számára.  Az alkalmazás egyes példányainak egy önálló példányát, ezért soha ne hatással van az egyéb önálló példányát.  Az alkalmazás egyes példányainak csak egy bérlői, és ezért csak egy adatbázis.  A bérlő van az adatbázis összes önmagához.

![Pontosan egy egyetlen-bérlő adatbázis önálló alkalmazás tervét.][image-standalone-app-st-db-111a]

Minden egyes alkalmazás-példány telepítve van egy külön Azure erőforráscsoport.  Az erőforráscsoport olyan előfizetést, amelyet a szoftver gyártójához vagy a bérlő által birtokolt is tartozhatnak.  Mindkét esetben a szállító kezelheti a szoftver a bérlő számára.  Minden alkalmazás példány úgy van beállítva, a megfelelő adatbázishoz való kapcsolódáshoz.

Az egyes bérlői adatbázisok önálló adatbázisként van telepítve.  Ez a modell a legnagyobb adatbázis elkülönítést is biztosít.  Azonban az elkülönítési szükséges, hogy elegendő erőforrással lefoglalni az egyes adatbázis, a maximális terhelés kezelésére.  Itt számít, hogy a rugalmas készletek adatbázisok telepítve, a különböző erőforráscsoportokra, illetve különböző előfizetésekhez nem használható.  Ez a korlátozás az önálló single-bérlő alkalmazás egy általános adatbázis költség szempontjából legköltségesebb megoldás modell lehetővé teszi.

#### <a name="vendor-management"></a>Gyártói felügyeleti

A szállító hozzáférhet az önálló app példányainak, az adatbázisok, még akkor is, ha az app-példányok különböző bérlői előfizetések vannak telepítve.  A hozzáférés az SQL-kapcsolatokon keresztül érhető el.  A kereszt-példány hozzáférés engedélyezéséhez a szállító séma felügyelet és jelentéskészítés és elemzés céljából közötti adatbázis-lekérdezés.  Ha ilyen típusú központi kezelésére van szükség, a katalógus kell telepíteni, amely leképezhető bérlői azonosítók adatbázis URI-azonosítók.  Az Azure SQL Database biztosít egy horizontális skálázási könyvtár, amellyel egy SQL-adatbázis együtt katalógusát.  A horizontális könyvtár hivatalosan nevű a [Elastic Database ügyféloldali kódtár][docu-elastic-db-client-library-536r].

## <a name="c-multi-tenant-app-with-database-per-tenant"></a>C. Adatbázis-/-bérlő több-bérlős-alkalmazás

A következő mintát használja egy több-bérlős alkalmazás számos más adatbázis, az összes folyamatban egy bérlői adatbázisok.  Új adatbázis minden új bérlő lett kiépítve.  Az alkalmazás számítógépréteg *mentése* függőleges csomópontonként több erőforrást hozzáadásával.  Vagy az alkalmazás méretezett *kimenő* vízszintesen további csomópontok hozzáadásával.  A méretezés munkaterhelés alapul, és független az a számát és méretét, és az egyes adatbázisokat.

![Tervezési és adatbázis-/-bérlő több-bérlős alkalmazás.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>A bérlő testreszabása

Az önálló app mintát, például egy bérlői adatbázisok használatát ad erős bérlők elkülönítését.  Bármely olyan alkalmazásban, amelynek a modell csak egyetlen-bérlő adatbázisok határozza meg a séma összes egy adott adatbázis testreszabhatók, és a bérlői optimalizálva.  A Testreszabás ne érintsen más bérlőket az alkalmazásban. Lehet, hogy a bérlő módosítania kell a adatmező egyetlen bérlő számára szükséges adatok.  A további adatok mező további, módosítania kell az index.

Adatbázis-/-bérlővel a séma testreszabása egy vagy több egyes bérlők számára az egyszerű eléréséhez.  Az alkalmazás gyártójának segítségét kell tervezze meg gondosan kezelheti a séma testreszabások léptékű eljárások.

#### <a name="elastic-pools"></a>Rugalmas készletek

Ha adatbázisok ugyanabban az erőforráscsoportban vannak telepítve, akkor a rugalmas adatbáziskészletek csoportosíthatók.  A készletek költséghatékony teszik lehetővé az erőforrások számos adatbázis közti megosztását.  A Készlet beállítás esetén az egyes adatbázis elég nagy a használatának csúcsait, amely során lép fel, hogy olcsóbbak.  Annak ellenére, hogy a készletezett adatbázisok megosztani az erőforrásokhoz való hozzáférés azok továbbra is érhető el magas szintű teljesítmény-elszigetelés érdekében.

![Adatbázis-/-bérlőt, a rugalmas készlet több-bérlős alkalmazás tervét.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database biztosít az eszközök konfigurálásához, megfigyeléséhez és kezeléséhez a megosztás szükséges.  Mindkét készlet- és adatbázis metrikák az Azure-portálon, és Naplóelemzési keresztül érhetők el.  A mérőszámok összesített és bérlői-specifikus teljesítmény nagy betekintést biztosíthat.  Az egyes adatbázisok helyezheti át egy adott bérlő számára fenntartott erőforrások biztosításához készletek között.  Ezek az eszközök lehetővé teszik a megfelelő teljesítmény biztosítása költséghatékony módon.

#### <a name="operations-scale-for-database-per-tenant"></a>Adatbázis-/-bérlő méretezése műveletek

Az Azure SQL Database platform számos felügyeleti szolgáltatást arra tervezték, hogy léptékű, például a is több mint 100 000 adatbázisok adatbázisok felügyeleti nagyszámú rendelkezik.  Ezek a funkciók ellenőrizze az adatbázis-/-bérlő mintát egyértelmű.

Tegyük fel, hogy a rendszer rendelkezik a csak egy adatbázisaként 1000-bérlő adatbázis.  Az adatbázis 20 indexek rendelkezhet.  Ha a rendszer, hogy 1000 single-bérlő adatbázisok alakítja át, az indexek quatity 20 000 nő.  Az SQL-adatbázis részeként [automatikus hangolása][docu-sql-db-automatic-tuning-771a], az automatikus indexelési funkciók alapértelmezés szerint engedélyezettek.  Automatikus indexeléshez meg minden 20 000 indexek és kezeli a folyamatban lévő létrehozása és az vetett optimalizálás.  Ezek a automatizált műveletek történnek belül egyedi adatbázis, és nem koordinált vagy más adatbázisokban hasonló műveletek korlátozza.  Automatikus indexeléshez kezeli indexek eltérő adatbázisban foglalt mint kevésbé foglalt-adatbázisban.  Az ilyen típusú index felügyeleti testreszabási az adatbázis-/-bérlő léptékű nem célszerű lenne, ha hatalmas felügyeleti feladat kellett manuálisan kell elvégezni.

Egyéb szolgáltatásokat, amelyek jól méretezhető a következők:

- Beépített biztonsági mentése.
- Magas rendelkezésre állású.
- A lemez titkosítása.
- Teljesítmény telemetriai adatokat.

#### <a name="automation"></a>Automatizálás

A felügyeleti műveletek parancsprogrammal létrehozva, és -létesítésen keresztül felajánlott egy [devops] [ http-visual-studio-devops-485m] modell.  A műveletek még automatikus, és az alkalmazás elérhetővé.

Például a visszaállítás egyetlen bérlő egy korábbi sikertelen automatizálni időben.  A helyreállítás csak kell, amely tárolja a bérlő egy egyetlen-bérlő-adatbázis helyreállítására.  Ez a helyreállítás ne legyen hatással a többi bérlő, amely megerősíti, hogy vannak-e felügyeleti műveletek minden egyes bérlő finom részletes szintjén van.

## <a name="d-multi-tenant-app-with-multi-tenant-databases"></a>D. Több-bérlős alkalmazás több-bérlős adatbázisok

Egy másik érhető el a mintája, több bérlő több-bérlős adatbázis tárolásához.  Az alkalmazáspéldány állhat tetszőleges számú több-bérlős adatbázishoz.  A séma egy több-bérlős adatbázis egy vagy több bérlői azonosító oszlop rendelkeznie kell, hogy az adatok a panelhez adott szelektív módon lehet beolvasni.  A séma további, szükség lehet néhány táblák vagy a bérlők csak egy részhalmazát által használt oszlop.  Statikus kódot és hivatkozási adatok azonban csak egyszer tárolja, és legyen elosztva a egyetlen bérlő számára.

#### <a name="tenant-isolation-is-sacrificed"></a>Bérlők elszigetelésére elpusztítják van

*Adatok:* &nbsp; egy több-bérlős adatbázis feltétlenül elpusztítást bérlők elkülönítését.  Az adatok több bérlő együtt egy adatbázisban tárolódik.  Fejlesztési győződjön meg arról, hogy a lekérdezések soha nem fedi fel az egynél több bérlő adatait.  Támogatja az SQL-adatbázis [sorszintű biztonsági][docu-sql-svr-db-row-level-security-947w], amely kényszerítheti egy lekérdezés által visszaadott adatok tartozni egyetlen bérlő számára.

*Feldolgozási:* &nbsp; egy több-bérlős adatbázis számítási és tárolási erőforrásokat megosztja a bérlők között.  Az adatbázist egy teljes az alábbiak biztosíthatók győződjön meg arról, hogy akadálytalanul hajt végre.  Azonban az Azure rendszeren csak semmilyen beépített módon nem lehet figyelni, vagy az ezek által használt erőforrások egy adott bérlő kezelése.  Ezért a több-bérlős adatbázis hordoz magában, ha az állásidő megnövekedett kockázata, észlelése zajos szomszédok, ahol egy overactive bérlő munkaterhelését befolyásolja a teljesítményt nyújtanak a többi bérlő ugyanabban az adatbázisban.  További alkalmazásszintű figyelési elérhető bérlő szintű teljesítmény figyelni.

#### <a name="lower-cost"></a>Alacsonyabb költségek

Általában több-bérlős adatbázisok bérlővel rendelkezik, a legalacsonyabb /-költségeket.  Önálló adatbázist erőforrás költségek alacsonyabbak év méretű rugalmas készletek.  Emellett olyan helyzetekben, ahol a bérlők csak korlátozott tárolási kell, potenciálisan bérlők több millió tárolható egyetlen adatbázisban.  Nincs a rugalmas készlet adatbázisok több millió tartalmazhat.  Tartalmazó 1000 adatbázisok készletenként 1000-készletek, a megoldás azonban a skála kockázatára kezeléséhez kezelése nehézkessé válik több millió sikerült elérni.

Egy adatbázis több-bérlős modell két változata az alább, a leginkább rugalmas és méretezhető szilánkos több-bérlős modell ismertetése.

## <a name="e-multi-tenant-app-with-a-single-multi-tenant-database"></a>E. Több-bérlős alkalmazást egy több-bérlős-adatbázis

A legegyszerűbb több-bérlős adatbázis minta az összes bérlőre vonatkozó adatok egyetlen önálló adatbázist használ.  További bérlők hozzáadása, az adatbázis van kiterjesztett további tárolási és számítási erőforrásokat.  A skálától beállítható lehet szükséges, az összes, bár mindig egy végső méretezési korlátot.  Azonban ezt a határértéket az adatbázis elérése előtt hosszú kezeléséhez kezelése nehézkessé válik.

Az egyes bérlők összpontosítanak felügyeleti műveleteket összetettebbek, több-bérlős adatbázisban megvalósításához.  És léptékű elfogadhatatlanul válhatnak ezeket a műveleteket.  Egy példa egy időpontban visszaállítás, az adatok egyetlen bérlő számára.

## <a name="f-multi-tenant-app-with-sharded-multi-tenant-databases"></a>F. Több-bérlős app szilánkos több-bérlős adatbázisok

A legtöbb SaaS-alkalmazásokhoz érik el az adatokat csak egy bérlő egyszerre.  A hozzáférési mintára lehetővé teszi, hogy a bérlő adatok több adatbázis közötti elosztását, vagy egy shard szilánkok, ahol a bérlői vonatkozó összes adat szerepel.  Egy több-bérlős adatbázis mintát kombinálja, a szilánkos modell lehetővé teszi, hogy szinte korlátlan méretezési.

![Több-bérlős alkalmazás szilánkos több-bérlős adatbázisok Tervező.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Szilánkok kezelése

Horizontális összetettségét, mind a tervezési és működési felügyeleti hozzáadja.  A katalógus, amelyben a bérlők és az adatbázisok közötti leképezéseket fenntartásához szükséges.  Kezelheti a szilánkok és a bérlői feltöltését emellett felügyeleti eljárásokra van szükség.  Például eljárásokat kell megtervezni, hozzáadása és eltávolítása a szilánkok, és a bérlői adatok áthelyezése szilánkok között.  Egy méretezési módja egy új shard hozzáadásával, és azt az új bérlők feltöltéséhez.  A többi időszakban a sűrűn ki van töltve a shard előfordulhat, hogy felosztása két kevésbé sűrűn ki van töltve szilánkok.  Több bérlő áthelyezték vagy szakad meg, miután ritkán ki van töltve szilánkok együtt előfordulhat, hogy egyesíteni.  Az egyesítés további költséghatékony erőforrás-használatot eredményezhet.  Bérlők is előfordulhat, hogy át elosztásában szilánkok között.

SQL-adatbázis, amely együttműködik a horizontális és a katalógus-adatbázis vegyes/egyesítés eszközt biztosít.  A megadott alkalmazás fel, és egyesítési szilánkok, és bérlői adatok áthelyezheti szilánkok között.  Az alkalmazás is fenntartja, ezeket a műveleteket, való megjelölése közben a katalógus hatással bérlők, kapcsolat nélküli előtt helyezni őket.  Az áthelyezés után az alkalmazás frissíti a katalógus újra az új leképezés, és ismét online, a bérlő jelölésére.

#### <a name="smaller-databases-more-easily-managed"></a>Kisebb adatbázis további könnyen kezelésére

Bérlők több adatbázis közötti elosztásával a szilánkos több-bérlős megoldást kisebb adatbázisok könnyebben felügyelt eredményez.  Például egy adott bérlő visszaállítása egy korábbi pontra időben most magában foglalja a biztonsági másolat ahelyett, hogy egy nagyobb adatbázist, amely tartalmazza az összes bérlők egyetlen kisebb adatbázis visszaállítása. Az adatbázis mérete, és a bérlők adatbázisonként, száma választható ki a munkaterhelés és a felügyeleti feladatok elosztása érdekében.

#### <a name="tenant-identifier-in-the-schema"></a>A séma bérlő azonosítója

Attól függően, hogy a horizontális megközelítésben további korlátozásokat az adatbázisséma kell kivetett.  Az SQL-adatbázis vegyes/egyesítés alkalmazáshoz az szükséges, hogy a séma tartalmazza-e a horizontális kulcs, amely általában a bérlő azonosítója.  A bérlői azonosító a kezdő elem az összes szilánkos tábla elsődleges kulcsában.  A bérlő azonosítója lehetővé teszi, hogy a vegyes/egyesítés alkalmazás gyorsan keresse meg és áthelyezni az adatokat egy adott bérlő társított.

#### <a name="elastic-pool-for-shards"></a>A szilánkok rugalmas készlet

Horizontálisan skálázott több-bérlős adatbázisok rugalmas készletek helyezhető.  Általában sok bérlői egyetlen adatbázis a készletben, akkor az néhány több-bérlős adatbázisokban több bérlő rendelkezőként hatékony költség.  Több-bérlős adatbázisok előnyösek, ha nagy számú viszonylag inaktív bérlők.

## <a name="g-hybrid-sharded-multi-tenant-database-model"></a>G. Hibrid szilánkos több-bérlős adatbázismodell

A hibrid modell összes adatbázis kell a sémában a bérlő azonosítója.  Az adatbázisok összes képesek több bérlői tárolása, és lehet, hogy az adatbázisok szilánkos.  Ezért a séma értelemben összes több-bérlős adatbázis.  Még a gyakorlatban egy adatbázist tartalmazza csak egy bérlő.  Függetlenül attól egy adott adatbázisban tárolt bérlők mennyisége nem befolyásolja az adatbázisséma.

#### <a name="move-tenants-around"></a>Bérlők Navigálás

Bármikor helyezze át egy adott bérlő több-bérlős adatbázis.  És bármikor, meggondolja magát, helyezze vissza a bérlő több bérlő tartalmazó adatbázis.  Is hozzárendelheti a bérlő új single-bérlő adatbázis amikor az új adatbázis.

A hibrid modell helyezi, amikor a bérlő azonosítható csoportok erőforrásigényeivel nagy különbséget.  Tegyük fel például, hogy részt vesz egy ingyenes próbaverzióra bérlők nem garantált teljesítményéről, amely előfizető bérlőkre azonos magas szintű.  A házirend, amelyet az összes szabad próbabérlőket használ egy több-bérlős adatbázisban tárolja a ingyenes próba fázis lehet a bérlők számára.  Amikor egy ingyenes próba bérlő előfizet az alapvető szolgáltatási szint, a bérlő helyezheti át egy másik olyan több-bérlős adatbázis ajánlott, amelyek kevesebb bérlők lehet.  A saját új single-bérlő adatbázis olyan előfizetője, amelyik a prémium szolgáltatásszintet fizet sikerült áthelyezni.

#### <a name="pools"></a>Készletek

A hibrid modellben az előfizető bérlők single-bérlő adatbázisok helyezhető erőforráskészletek bérlőnként adatbázis költségek csökkentése érdekében.  Az adatbázis-/-bérlős modell is használtuk.

## <a name="h-tenancy-models-compared"></a>H. Bérleti modellek összehasonlítása

A következő táblázat összefoglalja a fő bérleti modellek közötti különbséget.

| Mérési | Önálló alkalmazás | Adatbázis-/-bérlő | Horizontálisan skálázott több-bérlős |
| :---------- | :------------- | :------------------ | :------------------- |
| Méretezés | Közepes<br />1-100-as egység | Nagyon magas<br />1-100,000s | Korlátlan<br />1-1,000,000s |
| Bérlők elszigetelésére | Nagyon magas | Magas | Alacsony; kivéve bármely egypéldányos bérlői (Ez kizárólag egy MT db-ben). |
| Adatbázis költségének bérlőnként | Magas; a csúcsait mérete. | Alacsony; a készletek használt. | Legalacsonyabb, kis bérlők MT adatbázisok esetén. |
| Teljesítmény figyelése és kezelése | /-Bérlőt csak | Összesítés + bérlői | Összesítés; Bár egyes-bérlőt csak singletons van. |
| Fejlesztési összetettsége | Alacsony | Alacsony | Közepes; miatt horizontális. |
| Működési összetettsége | Alacsony magas. Külön-külön egyszerű, összetett méretekben. | Alacsony – közepes. Minták cím összetettsége méretekben. | Alacsony magas. Az egyes bérlői felügyeleti összetett. |
| &nbsp; ||||

## <a name="next-steps"></a>Következő lépések

- [Központi telepítése, és vizsgálja meg az adatbázis-/-bérlő Szolgáltatottszoftver modell - Azure SQL Database egy több-bérlős Wingtip alkalmazás][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Üdvözli a Wingtip jegyek SaaS Azure SQL Database bérleti mintaalkalmazás][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Pontosan egy egyetlen-bérlő adatbázis önálló alkalmazás tervét."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Tervezési és adatbázis-/-bérlő több-bérlős alkalmazás."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Adatbázis-/-bérlőt, a rugalmas készlet több-bérlős alkalmazás tervét."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Több-bérlős alkalmazás szilánkos több-bérlős adatbázisok Tervező."

