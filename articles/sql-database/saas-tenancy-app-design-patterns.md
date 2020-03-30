---
title: Több-bérlős SaaS-minták
description: Ismerje meg a több-bérlős szoftverek szolgáltatásként (SaaS) adatbázis-alkalmazások követelményeit és közös adatarchitektúramintáit, amelyek az Azure felhőkörnyezetében futnak.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 956d74467c69d9924d26f9cae8d902a6ddd84496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067489"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Több-bérlős SaaS-adatbázis-bérleti minták

Ez a cikk ismerteti a több-bérlős SaaS-alkalmazások elérhető különböző bérlői modellek.

Több-bérlős SaaS-alkalmazás tervezésekor gondosan válassza ki a bérleti modell, amely a legjobban megfelel az alkalmazás igényeinek.  A bérlői modell határozza meg, hogy az egyes bérlői adatok le vannak képezve a tárolóhoz.  A választott bérleti modell hatással van az alkalmazás tervezésére és kezelésére.  Később egy másik modellre való áttérés néha költséges.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS fogalmak és terminológia

A Szoftver mint Szolgáltatás (SaaS) modellben az Ön vállalata nem értékesít *licenceket* az Ön szoftveréhez. Ehelyett minden ügyfél fizet bérleti díjat a vállalat, így minden ügyfél a *bérlő* a cég.

A bérleti díj fizetéséért cserébe minden bérlő hozzáférést kap a SaaS-alkalmazás-összetevőkhöz, és az adatait a SaaS-rendszerben tárolja.

A *bérlői modell* kifejezés a bérlők tárolt adatainak rendszerezésére utal:

- *Egyszeri bérleti:* &nbsp; Minden adatbázis csak egy bérlő adatait tárolja.
- *Több-bérlős:* &nbsp; Minden adatbázis tárolja az adatokat több külön bérlő (az adatvédelmi mechanizmusok).
- Hibrid bérleti modellek is rendelkezésre állnak.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Hogyan válasszuk ki a megfelelő bérleti modell

Általában a bérleti modell nem befolyásolja az alkalmazás funkcióját, de valószínűleg hatással van a teljes megoldás más aspektusaira.  Az egyes modellek értékeléséhez a következő kritériumokat kell alkalmazni:

- **Skálázhatóság:**
    - Bérlők száma.
    - Bérlőnkénti tárhely.
    - Tárolás összesítve.
    - Munkateher.

- **Bérlői elkülönítés:** &nbsp; Adatok elkülönítése és a teljesítmény (hogy egy bérlő számítási feladatok hatással van-e másokra).

- **Bérlőnkénti költség:** &nbsp; Adatbázis-költségek.

- **A fejlesztés összetettsége:**
    - Séma módosítása.
    - A lekérdezések módosítása (a minta megköveteli).

- **Működési összetettség:**
    - A teljesítmény figyelése és kezelése.
    - Sémakezelés.
    - Bérlő helyreállítása.
    - Vészhelyreállítás.

- **Testreszabhatóság:** &nbsp; A séma-testreszabások támogatásának egyszerűsége, amelyek bérlő- vagy bérlői osztályspecifikusak.

A bérleti vitaaz *adatrétegre* összpontosít.  De fontolja meg egy pillanatra az *alkalmazási* réteget.  Az alkalmazásréteg monolitikus entitásként lesz kezelve.  Ha az alkalmazást sok kis összetevőre osztja fel, a választott bérleti modell változhat.  Egyes összetevőket eltérően kezelhet, mint másokat mind a bérleti szerződés, mind a használt tárolási technológia vagy platform tekintetében.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Önálló egybérlős alkalmazás egybérlős adatbázissal

#### <a name="application-level-isolation"></a>Alkalmazásszint elkülönítése

Ebben a modellben az egész alkalmazás telepítése többször, minden egyes bérlő egyszer.  Az alkalmazás minden példánya önálló példány, így soha nem lép kölcsönhatásba más önálló példányokkal.  Az alkalmazás minden példánya csak egy bérlővel rendelkezik, ezért csak egy adatbázisra van szüksége.  A bérlő az adatbázis önmagában.

![Önálló alkalmazás tervezése pontosan egyetlen egybérlős adatbázissal.][image-standalone-app-st-db-111a]

Minden egyes alkalmazáspéldány egy külön Azure-erőforráscsoportban van telepítve.  Az erőforráscsoport olyan előfizetéshez tartozhat, amely a szoftver szállítójának vagy a bérlőnek a tulajdonában van.  Mindkét esetben a szállító kezelheti a szoftvert a bérlő számára.  Minden alkalmazáspéldány úgy van konfigurálva, hogy csatlakozzon a megfelelő adatbázishoz.

Minden bérlői adatbázis egyetlen adatbázisként van üzembe helyezve.  Ez a modell biztosítja a legnagyobb adatbázis-elkülönítést.  Az elkülönítés azonban megköveteli, hogy elegendő erőforrást kell hozzárendelni az egyes adatbázisokhoz a csúcsterhelések kezeléséhez.  Itt fontos, hogy rugalmas készletek nem használhatók a különböző erőforráscsoportokban vagy különböző előfizetésekben üzembe helyezett adatbázisokhoz.  Ez a korlátozás teszi ezt az önálló egybérlős alkalmazásmodellt a legdrágább megoldássá az adatbázis teljes költségszempontjából.

#### <a name="vendor-management"></a>Szállítói kezelés

A szállító hozzáférhet az összes adatbázishoz az összes önálló alkalmazáspéldányban, még akkor is, ha az alkalmazáspéldányok különböző bérlői előfizetésekben vannak telepítve.  A hozzáférés SQL-kapcsolatokon keresztül érhető el.  Ez a többpéldányos hozzáférés lehetővé teszi, hogy a szállító központosítsa a sémakezelést és az adatbázisközi lekérdezést jelentéskészítési vagy elemzési célokra.  Ha ilyen típusú központosított felügyeletre van szükség, egy katalógust kell telepíteni, amely leképezi a bérlői azonosítókat az adatbázis URI-ihoz.  Az Azure SQL Database egy olyan szegmensek tára, amely egy SQL-adatbázissal együtt egy katalógus biztosításához használatos.  A szilánkos könyvtár hivatalos neve [a rugalmas adatbázis-ügyfélkönyvtár.][docu-elastic-db-client-library-536r]

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Több-bérlős alkalmazás bérlőnkénti adatbázissal

Ez a következő minta egy több-bérlős alkalmazást használ, amely számos adatbázist használ, amelyek mindegyike egy-bérlős adatbázis.  Minden új bérlőhöz új adatbázis van kiépítve.  Az alkalmazásszint *vertikálisan növekszik* azáltal, hogy csomópontonként további erőforrásokat ad hozzá.  Vagy az alkalmazás horizontálisan *horizontálisan* további csomópontok hozzáadásával.  A skálázás számítási feladatokon alapul, és független az egyes adatbázisok számától vagy skálától.

![Több-bérlős alkalmazás tervezése bérlőnkénti adatbázissal.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Testreszabás bérlőhöz

Az önálló alkalmazásmintához hasonlóan az egybérlős adatbázisok használata is erős bérlői elkülönítést biztosít.  Minden olyan alkalmazásban, amelynek modellje csak egybérlős adatbázisokat határoz meg, egy adott adatbázis sémája testreszabható és optimalizálható a bérlőszámára.  Ez a testreszabás nincs hatással az alkalmazás többi bérlőjére. Előfordulhat, hogy egy bérlőnek szüksége lehet az alapvető adatmezőkön túli adatokra, amelyekminden bérlőnek szüksége van.  Továbbá előfordulhat, hogy az extra adatmezőnek indexre van szüksége.

A bérlőnkénti adatbázissal a séma testreszabása egy vagy több egyéni bérlő számára egyszerű.  Az alkalmazás szállítójának eljárásokat kell terveznie a séma testreszabásának nagy méretekben történő gondos kezeléséhez.

#### <a name="elastic-pools"></a>Rugalmas készletek

Ha adatbázisok vannak telepítve ugyanabban az erőforráscsoportban, rugalmas készletek csoportosíthatók.  A készletek költséghatékony módon osztják meg az erőforrásokat számos adatbázis között.  Ez a készlet beállítás olcsóbb, mint az egyes adatbázisok, hogy elég nagy ahhoz, hogy a használati csúcsok, hogy az általa használt.  Annak ellenére, hogy a készletezésű adatbázisok megosztják az erőforrásokhoz való hozzáférést, továbbra is magas szintű teljesítmény-elkülönítést érhetnek el.

![Több-bérlős alkalmazás tervezése bérlőnkénti adatbázissal, rugalmas készlet használatával.][image-mt-app-db-per-tenant-pool-153p]

Az Azure SQL Database biztosítja a megosztás konfigurálásához, figyeléséhez és kezeléséhez szükséges eszközöket.  Mind a készletszintű, mind az adatbázis-szintű teljesítménymutatók elérhetők az Azure Portalon és az Azure Monitor naplóin keresztül.  A metrikák nagyszerű betekintést nyújthatnak mind az összesített, mind a bérlő-specifikus teljesítménybe.  Az egyes adatbázisok áthelyezhetők a készletek között, hogy egy adott bérlő számára fenntartott erőforrásokat biztosítsanak.  Ezek az eszközök lehetővé teszik a jó teljesítmény költséghatékony biztosítását.

#### <a name="operations-scale-for-database-per-tenant"></a>Műveletek méretezése bérlőnkénti adatbázishoz

Az Azure SQL Database platform számos felügyeleti funkcióval rendelkezik, amelyek nagy számú, nagy számú, például több mint 100 000 adatbázis t.  Ezek a szolgáltatások teszik a bérlőnkénti adatbázis-mintát.

Tegyük fel például, hogy egy rendszer rendelkezik egy 1000-bérlős adatbázissal, mint egyetlen egyetlen adatbázis.  Az adatbázis 20 indexet tartalmazhat.  Ha a rendszer 1000 egybérlős adatbázissá alakul át, az indexek mennyisége 20 000-re emelkedik.  Az SQL Database automatikus [hangolása][docu-sql-db-automatic-tuning-771a]részeként az automatikus indexelési szolgáltatások alapértelmezés szerint engedélyezve vannak.  Az automatikus indexelés mind a 20 000 indexet, valamint a folyamatban lévő létrehozási és leadási optimalizálást kezeli.  Ezek az automatizált műveletek egy adott adatbázison belül történnek, és más adatbázisokban nem koordinálják vagy korlátozzák őket.  Az automatikus indexelés eltérően kezeli az indexeket egy foglalt adatbázisban, mint egy kevésbé elfoglalt adatbázisban.  Az ilyen típusú indexkezelés testreszabása nem lenne praktikus az adatbázis-bérlőnkénti skálán, ha ezt a hatalmas felügyeleti feladatot manuálisan kellene elvégezni.

Egyéb felügyeleti funkciók, amelyek jól méretezik a következőket:

- Beépített biztonsági mentések.
- Magas rendelkezésre állás.
- Lemezes titkosítás.
- Teljesítménytelemetria.

#### <a name="automation"></a>Automation

A felügyeleti műveletek parancsfájlt írhatók, és egy [devops-modellen][http-visual-studio-devops-485m] keresztül érhető el.  A műveletek is lehet automatizálni, és ki vannak téve az alkalmazásban.

Például automatizálhatja a helyreállítás egyetlen bérlő egy korábbi időpontban.  A helyreállításcsak vissza kell állítania az egyetlen egybérlős adatbázist, amely tárolja a bérlőt.  Ez a visszaállítás nincs hatással a többi bérlőre, amely megerősíti, hogy a felügyeleti műveletek az egyes bérlők finoman szemcsés szintjén vannak.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Több-bérlős alkalmazás több-bérlős adatbázisokkal

Egy másik rendelkezésre álló minta, hogy tárolja a több-bérlős adatbázis ban sok bérlő.  Az alkalmazáspéldány tetszőleges számú több-bérlős adatbázissal rendelkezhet.  A több-bérlős adatbázis sémájának rendelkeznie kell egy vagy több bérlőazonosító-oszlopkal, hogy az adott bérlőadatai szelektíven lehívhatók legyenek.  Továbbá a séma szükség lehet néhány táblák vagy oszlopok, amelyek csak a bérlők egy részhalmaza által használt.  A statikus kód és a hivatkozási adatok tárolása azonban csak egyszer történik meg, és az összes bérlő megosztja őket.

#### <a name="tenant-isolation-is-sacrificed"></a>A bérlői elkülönítés feláldozva van

*Adatok:* &nbsp; A több-bérlős adatbázis szükségszerűen feláldozza a bérlők elkülönítését.  Több bérlő adatait együtt tárolja egy adatbázisban.  A fejlesztés során győződjön meg arról, hogy a lekérdezések soha nem teszik elérhetővé az adatokat egynél több bérlő.  Az SQL Database támogatja [a sorszintű biztonságot,][docu-sql-svr-db-row-level-security-947w]amely kényszerítheti, hogy a lekérdezésből visszaadott adatok hatóköre egyetlen bérlőre legyen.

*Feldolgozás:* &nbsp; Egy több-bérlős adatbázis megosztja a számítási és tárolási erőforrásokat az összes bérlő között.  Az adatbázis egésze nyomon követhető annak biztosítása érdekében, hogy elfogadhatóan működik-e.  Azonban az Azure-rendszer nem rendelkezik beépített módon ezeket az erőforrásokat egy adott bérlő által történő használatára.  Ezért a több-bérlős adatbázis hordoz nagyobb a kockázata a zajos szomszédok, ahol a munkaterhelés egy túlaktív bérlő hatással van a teljesítmény tapasztalata más bérlők ugyanabban az adatbázisban.  További alkalmazásszintű figyelés figyelheti a bérlői szintű teljesítményt.

#### <a name="lower-cost"></a>Alacsonyabb költség

Általában a több-bérlős adatbázisok rendelkeznek a legalacsonyabb bérlőnkénti költség.  Egyetlen adatbázis erőforrásköltségei alacsonyabbak, mint egy egyenértékű méretű rugalmas készlet esetében.  Emellett olyan esetekben, ahol a bérlők csak korlátozott tárházra van szükség, potenciálisan több millió bérlő tárolható egyetlen adatbázisban.  Egyetlen rugalmas készlet sem tartalmazhat több millió adatbázist.  Azonban egy megoldás, amely 1000 adatbázisok készletenként, 1000 készletek, elérheti a skála milliók kockázata, hogy nehézkes kezelni.

A több-bérlős adatbázis-modell két változata a következő, a szilánkos több-bérlős modell a legrugalmasabb és skálázható.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Több-bérlős alkalmazás egyetlen több-bérlős adatbázissal

A legegyszerűbb több-bérlős adatbázis-minta egyetlen adatbázist használ az összes bérlő adatainak üzemeltetéséhez.  Ahogy egyre több bérlőt ad hozzá, az adatbázis több tárolási és számítási erőforrásokkal van felskálázva.  Ez a skálázás lehet minden, ami szükséges, bár mindig van egy végső skála korlátot.  Azonban jóval azelőtt, hogy a korlát elérése az adatbázis lesz nehézkes kezelni.

Az egyes bérlőkre összpontosító felügyeleti műveletek összetettebbek egy több-bérlős adatbázisban.  És ezek a műveletek méretarányosan lassúak lehetnek.  Egy példa az adatok csak egy bérlő rejésidőhöz parancsára visszaállítása.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Több-bérlős alkalmazás szilánkos több-bérlős adatbázisokkal

A legtöbb SaaS-alkalmazás egyszerre csak egy bérlő adatait éri el.  Ez a hozzáférési minta lehetővé teszi a bérlői adatok több adatbázis vagy szegmens között történő elosztása, ahol egy bérlő összes adat egy szegmensben található.  A több-bérlős adatbázis-mintával kombinálva a szilánkos modell szinte korlátlan skálázást tesz lehetővé.

![Több-bérlős alkalmazás tervezése szilánkos több-bérlős adatbázisokkal.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Szilánkok kezelése

A finomítás a tervezési és az üzemeltetési felügyelet összetettségét is növeli.  A bérlők és az adatbázisok közötti leképezés karbantartásához katalógusra van szükség.  Emellett a szegmensek és a bérlői népesség kezeléséhez felügyeleti eljárásokszükségesek.  Például eljárásokat kell megtervezni, hogy adjunk hozzá és távolítson el szegmenseket, és a bérlői adatok áthelyezése a szegmensek között.  A méretezés egyik módja egy új shard hozzáadása és új bérlők feltöltése.  Máskor előfordulhat, hogy egy sűrűn lakott shard két kevésbé sűrűn lakott szilánkok felosztása.  Több bérlő áthelyezése vagy leállítása után előfordulhat, hogy egyesítheti a ritkán lakott szegmenseket.  Az egyesítés költséghatékonyabb erőforrás-kihasználtságot eredményezne.  Bérlők is áthelyezhetők a szegmensek között a számítási feladatok kiegyensúlyozása.

Az SQL Database egy felosztási/egyesítési eszközt biztosít, amely a szegmenstárral és a katalógusadatbázissal együtt működik.  A megadott alkalmazás feloszthatja és egyesítheti a szegmenseket, és áthelyezheti a bérlői adatokat a szegmensek között.  Az alkalmazás is fenntartja a katalógust a műveletek során, megjelölve az érintett bérlők offline állapotba helyezése előtt.  Az áthelyezés után az alkalmazás ismét frissíti a katalógust az új leképezéssel, és újra online állapotúként jelöli meg a bérlőt.

#### <a name="smaller-databases-more-easily-managed"></a>A kisebb adatbázisok könnyebben kezelhetők

A bérlők több adatbázis közötti elosztásával a szilánkos több-bérlős megoldás kisebb adatbázisokat eredményez, amelyek könnyebben kezelhetők.  Például egy adott bérlő visszaállítása egy korábbi időpontban most már magában foglalja egy kisebb adatbázis visszaállítása egy biztonsági mentésből, nem pedig egy nagyobb adatbázis, amely tartalmazza az összes bérlő. Az adatbázis mérete, és a bérlők száma adatbázisonként, lehet választani, hogy egyensúlyt a munkaterhelés és a felügyeleti erőfeszítéseket.

#### <a name="tenant-identifier-in-the-schema"></a>Bérlőazonosító a sémában

Attól függően, hogy a szilánkos megközelítés használt, további korlátozásokat lehet előírni az adatbázis séma.  Az SQL Database split/merge alkalmazás megköveteli, hogy a séma tartalmazza a szilánkos kulcsot, amely általában a bérlői azonosító.  A bérlői azonosító az összes szilánkos tábla elsődleges kulcsának vezető eleme.  A bérlői azonosító lehetővé teszi, hogy a split/merge alkalmazás gyorsan keresse meg és helyezze át az adott bérlőhöz társított adatokat.

#### <a name="elastic-pool-for-shards"></a>Elasztikus készlet szilánkok számára

Szilánkos több-bérlős adatbázisok rugalmas készletek helyezhető el.  Általában, amelyek sok egy-bérlős adatbázisok egy készletben ugyanolyan költséghatékony, mint a sok bérlő néhány több-bérlős adatbázisok.  Több-bérlős adatbázisok előnyös, ha nagy számú viszonylag inaktív bérlők.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hibrid szilánkos több-bérlős adatbázismodell

A hibrid modellben minden adatbázis rendelkezik a bérlői azonosítóa a sémában.  Az adatbázisok mindegyike több bérlő tárolására is képes, és az adatbázisok skálázhatók.  Tehát a séma értelemben, ezek mind több-bérlős adatbázisok.  A gyakorlatban azonban ezen adatbázisok némelyike csak egy bérlőt tartalmaz.  Ettől függetlenül az adott adatbázisban tárolt bérlők mennyisége nincs hatással az adatbázis sémára.

#### <a name="move-tenants-around"></a>Bérlők mozgatása

Bármikor áthelyezhet egy adott bérlőt a saját több-bérlős adatbázisába.  És bármikor meggondolhatja magát, és áthelyezheti a bérlőt egy adatbázisba, amely több bérlőt tartalmaz.  Az új adatbázis kiépítésekor bérlőt is hozzárendelhet az új egybérlős adatbázishoz.

A hibrid modell akkor világít, ha nagy különbségek vannak a bérlők azonosítható csoportjainak erőforrásigénye között.  Tegyük fel például, hogy az ingyenes próbaverzióban részt vevő bérlők nem garantáltak ugyanolyan magas szintű teljesítmény, mint a bérlők.  A szabályzat lehet a bérlők az ingyenes próbafázisban kell tárolni egy több-bérlős adatbázis, amely az összes ingyenes próba-bérlők között megosztott.  Amikor egy ingyenes próba-bérlő előfizet az alapszintű szolgáltatási szintre, a bérlő áthelyezhető egy másik több-bérlős adatbázisba, amely kevesebb bérlővel rendelkezhet.  A prémium szolgáltatási szintért fizető előfizető áthelyezhető a saját új egybérlős adatbázisába.

#### <a name="pools"></a>Készletek

Ebben a hibrid modellben az egybérlős adatbázisok előfizetői bérlők erőforráskészletekbérlői erőforrás-készletek bérlőnkénti csökkentése érdekében helyezhető el.  Ez is történik az adatbázis-bérlőnkénti modell.

## <a name="i-tenancy-models-compared"></a>I. Bérlői modellek összehasonlítása

Az alábbi táblázat összefoglalja a fő bérlői modellek közötti különbségeket.

| Mérés | Önálló alkalmazás | Bérlőnkénti adatbázis | Szilánkos több-bérlős |
| :---------- | :------------- | :------------------ | :------------------- |
| Méretezés | Közepes<br />1-100-as évek | Nagyon magas<br />1-100 000-es | Korlátlan<br />1-1 000 000-es |
| Bérlők elkülönítése | Nagyon magas | Magasság | Alacsony; kivéve az egyetlen bérlőt (amely egyedül van az MT db-ban). |
| Adatbázis bérlőnkénti költsége | Magas; csúcsokhoz van méretezve. | Alacsony; használt medencék. | Legalacsonyabb, a kis bérlők MT DBs. |
| Teljesítményfigyelés és -kezelés | Csak bérlőnként | Összesítés + bérlőnként | Összesítve; bár bérlőnként csak egyedülállók számára. |
| A fejlesztés összetettsége | Alacsony | Alacsony | Közepes; a szilánkok miatt. |
| Működési összetettség | Alacsony-magas. Egyénileg egyszerű, komplex méretekben. | Alacsony-közepes. A minták nagy méretekben kezelik a komplexitást. | Alacsony-magas. Az egyes bérlők kezelése összetett. |
| &nbsp; ||||

## <a name="next-steps"></a>További lépések

- [Üzembe helyezhet és feltáregy több-bérlős Wingtip alkalmazást, amely a bérlőnkénti adatbázis-bérlős SaaS-modellt használja - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Üdvözli a Wingtip jegyek minta SaaS Azure SQL Database bérleti alkalmazás][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Önálló alkalmazás tervezése pontosan egyetlen egybérlős adatbázissal."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Több-bérlős alkalmazás tervezése bérlőnkénti adatbázissal."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Több-bérlős alkalmazás tervezése bérlőnkénti adatbázissal, rugalmas készlet használatával."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Több-bérlős alkalmazás tervezése szilánkos több-bérlős adatbázisokkal."

