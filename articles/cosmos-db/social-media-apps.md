---
title: 'Az Azure Cosmos DB-tervezési minta: közösségimédia-alkalmazások |} A Microsoft Docs'
description: Ismerje meg a kialakítási mintában a közösségi hálózatokkal a storage rugalmas Azure Cosmos DB és más Azure-szolgáltatások kihasználásával.
keywords: közösségimédia-alkalmazások
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maquaran
ms.openlocfilehash: bc31c7ebec7c1f7a02be65b15805fb48b1ef275d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260312"
---
# <a name="going-social-with-azure-cosmos-db"></a>Közösségi tartalom az Azure Cosmos DB használatával
Egy nagy mértékben összekapcsolt társadalom élő azt jelenti, hogy életre valamely pontján, részévé válik a **közösségi hálózati**. Használhat közösségi hálózatokkal, hogy barátai, munkatársai, termékcsalád vagy más néven közös érdeklődési rendelkező személyek a szenvedélyét megosztani.

Szakemberek vagy fejlesztők számára, akkor előfordulhat, hogy rendelkezik testreszabásakor nem tudta hogyan lehet ezeket a hálózatokat tárolja és ezek az adatok, vagy előfordulhat, hogy rendelkezik is lett kiadta létrehozásához vagy egy adott jelentethet piacra új közösségi hálózat yourselves tervezhet. Ha ez a jelentős kérdése merül fel: ezek az adatok tárolási módját?

Tegyük fel, hogy hoz létre egy új és fényes közösségi hálózat, ahol a felhasználók kapcsolódó adathordozók, például képek, videók vagy zene akár cikkeket is közzé. A felhasználók bejegyzések fűzni és pontokat biztosíthat az értékelésekhez. Bejegyzések, amelyet a felhasználók hírcsatornában lesz és fogják tudni használni a fő webhely kezdőlapján. Ez a módszer nem összetett hang-(elsőre), de az egyszerűség kedvéért most állítja le van (jobban is elmélyedne kapcsolatok által érintett egyéni felhasználói hírcsatornák, de ez meghaladja a cél a cikk).

Tehát hogyan tegye tárolja ez, és ahol?

Előfordulhat, hogy rendelkezik tapasztalattal az SQL-adatbázisokon vagy egy fogalma [relációs adatok modellezése](https://en.wikipedia.org/wiki/Relational_model) és kezdheti rajzolási valamit a következő:

![Relatív relációs modell ábrázoló diagram](./media/social-media-apps/social-media-apps-sql.png) 

Tökéletesen normalizált és szép adatstruktúra... amely nem méretezhető. 

Nem kap meg helytelen dolgoztam az SQL Database-adatbázisok összes életem, nagyszerű azok, de minden mintát, a gyakorlatban és a szoftverek platformhoz, például nem tökéletes mindegyik forgatókönyv esetében.

Miért nem SQL ebben a forgatókönyvben a legjobb választás? Vegyünk egy egyetlen bejegyzés szerkezete Ha szerettem volna a lekérdezésére van egy webhelyet vagy alkalmazást, a bejegyzés véleményezésével megjelenítése... csak, egy egyetlen bejegyzés megjelenítése nyolc tables(!) csatlakoztatásával, kép, amelyekre dinamikusan betölteni, és megjelenik a képernyőn, és egy adatfolyam jelenhet meg hol fogom.

Hatalmas mennyiségű SQL-példány ezer lekérdezést az illesztések kiszolgálására a tartalmat, de valóban, miért érdemes lenne, ha létezik egy egyszerűbb megoldást megoldásához elég power kreditért igénybe?

## <a name="the-nosql-road"></a>A nosql-alapú közúti
Ez a cikk végigvezeti az Azure NoSQL-adatbázis a közösségi platform adatok modellezését, [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) a költséghatékony más az Azure Cosmos DB költséghatékonyhatékonyságából kínál, mint például a [Gremlin API](../cosmos-db/graph-introduction.md). Használatával egy [nosql-alapú](https://en.wikipedia.org/wiki/NoSQL) megközelítés, adattárolás, a JSON-formátumban, és alkalmazása [denormalizáció](https://en.wikipedia.org/wiki/Denormalization), a korábban bonyolult bejegyzés egy átalakíthatók [dokumentum](https://en.wikipedia.org/wiki/Document-oriented_database):


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

És egyetlen lekérdezést, és az illesztések nem szerezhető be. Ez a lekérdezés sokkal egyszerű és könnyen érthető megjegyzésblokkok írására, és, budget-wise, legjobb eredmény elérése érdekében kevesebb erőforrást igényel.

Az Azure Cosmos DB gondoskodik róla, hogy az összes tulajdonság indexelt annak automatikus indexelést, az is lehet, amely [testre szabott](indexing-policies.md). A séma nélküli megközelítés lehetővé teszi, hogy velünk a kapcsolatot a másik, a dinamikus struktúrák, talán holnap azt szeretné, hogy van egy listája azokról a kategóriák és a hozzájuk társított hashtageket hozzászólások dokumentumok tárolására, a Cosmos DB az új dokumentumok nincsenek további munkát a hozzáadott attribútumokat fogja kezelni. szükség szerint velünk a kapcsolatot.

Megjegyzések egy bejegyzést a többi hozzászólásétól (Ez leegyszerűsíti az objektum leképezésének) szülő tulajdonsággal rendelkező is kezelni. 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

És minden közösségi interakciókat, számlálók tárolható egy önálló objektumon:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Adatcsatornák létrehozása a következő annyit, amelyet egy adott relevancia alapján végzett sorrendjében post-azonosítók listáját tartalmazhat dokumentumok létrehozása:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

A létrehozás dátuma szerint rendezve bejegyzések lehet a "legutóbbi" stream, ezek a bejegyzések a "legkeresettebb" stream több kedveli az elmúlt 24 órában, még akkor is Megvalósíthat egy egyéni stream minden egyes felhasználó követőinek és érdeklődése, így például logika alapján és továbbra is lenne listája  bejegyzések. Ezek a listák létrehozása kérdése, de az olvasási teljesítmény akadálytalan marad. Ha e listák valamelyikébe szerez be, a Cosmos DB használatával ki egyetlen lekérdezést a [OPERÁTORBAN](sql-api-sql-query.md#WhereClause) lapjain bejegyzések beszerzéséhez egyszerre.

Használja a hírcsatorna adatfolyamok sikerült beépített [Azure App Services](https://azure.microsoft.com/services/app-service/) háttér-folyamatok: [Webjobs](../app-service/web-sites-create-web-jobs.md). Bejegyzés létrehozása után a háttérben történő feldolgozás használatával is elindítható [Azure Storage](https://azure.microsoft.com/services/storage/) [üzenetsorok](../storage/queues/storage-dotnet-how-to-use-queues.md) és a Webjobs használatával aktivált a [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)végrehajtási, a Propagálás közzététele a saját egyéni logika alapján Streamek belül. 

Pontok és a egy közleményre kedvelések ugyanezzel a módszerrel használatával egy végül konzisztens környezetet hozhat létre, a késleltetett módon lehet feldolgozni.

Követőinek olyan bonyolultabb. A cosmos DB dokumentum maximális méretkorlátot rendelkezik, és olvasási/írási nagy dokumentumok hatással lehet az alkalmazás teljesítményét. Ezért előfordulhat, hogy gondolja ennél a módszernél dokumentumként követők tárolására:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Ez működhetnek néhány több ezer felhasználó követőinek, de ha néhány hírességek csatlakozik-e a rangsorolás, ezzel a megközelítéssel egy dokumentum nagy méretű vezet, és előfordulhat, hogy végül nyomja le a dokumentum mérete kap.

Oldja meg ezt, használhatja a vegyes megközelítést. A felhasználói statisztikák dokumentum részeként tárolhatja a követők száma:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

És a tényleges grafikon, a követőinek tárolhatók az Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md), illetve [csúcspontokat](http://mathworld.wolfram.com/GraphVertex.html) minden felhasználó és [élek](http://mathworld.wolfram.com/GraphEdge.html) , amely a "A-követi – B" karbantartása kapcsolat. A Gremlin API hozzunk, nem csak az egy adott felhasználó követőinek beszerzése, de még akkor is javasolhat a gyakori személyek összetettebb lekérdezéseket hozzon létre. A grafikon, a tartalom kategóriák adott személyek, például vagy igénybevételéhez ad hozzá, ha megkezdése szövödei, hogy intelligens tartalom felderítés, tartalmaznia jelenítsen, hogy azok hasonló követheti, vagy személyek, akikkel lehetséges, hogy nagy részét a gyakori kereséséhez.

A felhasználói statisztikák a dokumentum-kártyák létrehozása a felhasználói felületen vagy a gyors profil előnézetek továbbra is használható.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Skála" minta és az adatok duplikálása
A hozzászólás hivatkozó JSON-dokumentumok talán észrevette, mert nincsenek egy felhasználó több előfordulása. És meg szeretné rendelkezik kitalálni megfelelő, ez azt jelenti, hogy egy felhasználó adott ez denormalizáció képviselő információk szerepelhet egynél több helyen.

Annak érdekében, hogy engedélyezi a lekérdezések, adatdeduplikáció számítunk fel. A probléma merült fel ezt a mellékhatást, ha az egyes műveletekhez, a felhasználói adatok módosításait, meg kell keresnie az összes olyan tevékenységet hogy valaha volt, és frissíteni őket az összes. Nem megbízható gyakorlati, jobb?

Megoldhatja a problémát a felhasználó az alkalmazás minden egyes tevékenységhez megjelenítő kulcs attribútumai azonosításával kívánja. Ha vizuálisan bejegyzés megjelenítése az alkalmazásban, és csak a létrehozó nevét és a kép megjelenítéséhez, miért tárolja a felhasználói adatok a "createdBy" attribútumot? Minden megjegyzést, csak a felhasználó kép megjelenítése, ha nem feltétlenül szükséges a adatait a többi. Ez a hiba a "létra minta" meghívom play hol kerül.

Vegyük példaként a felhasználói adatokat:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Ez az információ megnézzük, gyorsan elvégezheti a kritikus fontosságú információkhoz, amely így létrehozása, amely nem, "Skála":

![A skála minta ábrája](./media/social-media-apps/social-media-apps-ladder.png)

A legkisebb lépést nevezzük regisztrációnak egy UserChunk, a minimális adat, amely azonosítja a felhasználót, és az adatdeduplikáció használják. Csak akkor fog "show" adatait a duplikált adatok méretének csökkentésével nagy frissítések lehetőségének csökkentése.

A középső lépés neve a felhasználónak, hogy a teljes adatokat a Cosmos DB, a leggyakrabban elért és a kritikus fontosságú a legtöbb teljesítmény-függő lekérdezéseket kell használni. Egy UserChunk által képviselt információkat tartalmazza.

A legnagyobb az a kiterjesztett felhasználó. Az összes kritikus felhasználói információkat tartalmaz, és más adatok nem igénylő nagyon gyorsan kell olvasni, vagy a használatát a végleges (például a bejelentkezési folyamat). Ezeket az adatokat a Cosmos DB, az Azure SQL Database vagy Azure Storage-táblák kívül tárolható.

Miért volna, a felhasználó felosztása és akár különböző helyeken tárolja? Mivel a teljesítmény szempontjából, annál nagyobb a dokumentumokat, a costlier a lekérdezéseket. Dokumentumok slim, hajtsa végre az összes a teljesítmény-függő lekérdezések a közösségi hálózat végleges szituációkra, mint, teljes profil módosítások bejelentkezések más extra információk tárolására, és data szintű adatbányászatra használati elemzés és big Data típusú adatok is a megfelelő információkkal megtartása kezdeményezések. Valóban nem fontos, ha az adatgyűjtési data szintű adatbányászatra lassabb lesz, mivel az Azure SQL Database futtat, akkor rendelkezik a következőkre azonban, hogy a felhasználók rendelkeznek-e egy gyors és slim élmény. A felhasználó, a Cosmos dB-ben tárolt következő lenne:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

És a egy hozzászólást néz ki:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

És a egy szerkesztési merül fel, ahol az adattömbök attribútumai egyik érintett, esetén az érintett dokumentumok keresése az indexelt attribútumokat mutató lekérdezések segítségével könnyen (kiválasztása * FROM bejegyzések p WHERE p.createdBy.id == "edited_user_id") és az adattömböket.

## <a name="the-search-box"></a>A keresőmezőbe
Felhasználók generál hetünk, sok tartalom. Akkor keressen, és keresse meg a tartalmat, amely nem lehet közvetlenül a saját tartalomfolyamokat talán mert ne hajtsa végre a létrehozói tárolókiszolgálóhoz képesnek kell lennie, és esetleg csak szeretne, hogy régi post tette hat hónapja található.

Szerencsére és használja az Azure Cosmos DB, mert könnyen valósítható meg a keresési motor használatával [Azure Search](https://azure.microsoft.com/services/search/) néhány perc alatt és a egy egyetlen sor kód (természetesen, mint más, a keresési folyamata és felhasználói felület) beírása nélkül.

Miért van ez olyan egyszerű?

Az Azure Search valósítja meg, milyen hívják meg [indexelők](https://msdn.microsoft.com/library/azure/dn946891.aspx), háttérben dolgozza fel, hogy az adatok tárházakban hook és automagically hozzáadása, frissítése vagy távolítsa el az indexeket az objektumok. Támogatják-e egy [Azure SQL Database-indexelők](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure BLOB-indexelők](../search/search-howto-indexing-azure-blob-storage.md) és Szerencsére [Azure Cosmos DB-hez indexelők](../search/search-howto-index-documentdb.md). Az átállás információk Cosmos DB-ből az Azure Search könnyen érthető megjegyzésblokkok írására, mint mindkét áruházbeli adatokat JSON formátumban, csak meg kell [hozza létre az indexet](../search/search-create-index-portal.md) , mely a kívánt indexelt dokumentumok származó attribútumok leképezésére és ennyi, a percek alatt (az adatok méretétől függ), minden tartalom számára érhetők el, a legjobb keresése –-szolgáltatásként megoldást a felhőalapú infrastruktúrák lehet keresni. 

További információ az Azure Search, keresse fel a [Hitchhiker's Guide Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Az alapul szolgáló ismeretek
A tartalom, amely növekszik, és minden nap növekszik rendezést követően gondolkodás találhatja: Mi a teendő az adatok az adatfolyam a felhasználóim?

A válasz nagyon egyszerű:, működik, és ismerje meg, hogy.

De mi is elsajátíthatja? Néhány egyszerű példák [hangulatelemzés](https://en.wikipedia.org/wiki/Sentiment_analysis)tartalom javaslatok alapján egy felhasználó beállításait, vagy akár egy automatizált gondoskodik róla, hogy a közösségi hálózat által közzétett összes tartalomra a család biztonságosan a content moderator,.

Most, hogy szükségem van csatlakoztatva, valószínűleg gondolja lesz szüksége ezen mintákat és adatokat egyszerű adatbázisok és a fájlok kibontásához a matematikai adatelemzési néhány PhD, de lenne megfelelő.

[Az Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)részeként elérhető a [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), van egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi az algoritmusok használatával egy egyszerű fogd és vidd felületén hozzon létre munkafolyamatokat, a saját algoritmusokhoz code[ R](https://en.wikipedia.org/wiki/R_\(programming_language\)) vagy használja a már létrehozott és készen áll, mint például az API-k használata: [Szövegelemzés](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [a Content Moderator, vagy [javaslatok](https://gallery.azure.ai/Solution/Recommendations-Solution).

A Machine Learning esetekben eléréséhez használható [az Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) képes feldolgozni a különböző forrásokból származó adatokat, és [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) feldolgozni az információkat, és hozzon létre, amelyeket a kimenet az Azure Machine Learning feldolgozni.

Egy másik elérhető lehetőség [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) elemezheti a felhasználók számára tartalom; csak nem is megismeri őket jobban (keresztül elemzése, mi írnak a [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), de sikerült is nemkívánatos vagy érett tartalom észlelése és az azoknak megfelelő cselekvést a [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). A cognitive Services számos out-az-megoldások, amelyek nem igényelnek a Machine Learning-ismeretek használata bármilyen típusú tartalmazza.

## <a name="a-planet-scale-social-experience"></a>Egy globális közösségi élmény
Egy utolsó, de nem utolsósorban fontos cikk kell címezni: **méretezhetőség**. Rendkívül fontos, hogy az egyes összetevők méretezhetők saját maga, vagy mert szüksége lesz a további adatokat feldolgozni az architektúra tervezése során, vagy mert szeretne biztosítani egy nagyobb földrajzi lefedettségével (vagy mindkét!). Szerencsére jövedelmezőbb munkát tesznek lehetővé az összetett feladat van egy **kulcsrakész megoldást** a Cosmos DB használatával.

A cosmos DB támogatja a [dinamikus particionálás](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) -a-beépített alapján automatikusan létrehoz egy adott **partíciókulcs** (egy, a dokumentumok az attribútumok meghatározott). A megfelelő partíciókulcs meghatározása kell elvégezni, már a tervezés során, további tudnivalókért lásd: [válassza ki a megfelelő partíciókulcs](partitioning-overview.md#choose-partitionkey) cikk. Egy közösségi élmény esetén a particionálási stratégia kell igazítani a módon lekérdezheti, ha az (ugyanazon a partíción belül olvasási is kívánatos) és írása ("hotspotok" elkerülheti a írási művelet több partíciót terjedhetnek). Egyes lehetőségeket vannak: a tartalom kategória, a földrajzi régióban, a felhasználó; historikus kulcs (nap/hó/hét) alapján az összes valóban attól függ, hogyan fog kérdezni az adatokat, és jelenítse meg azt a közösségi felhasználói élmény. 

Egyik érdekes pont érdemes megemlíteni, hogy a Cosmos DB futtatja-e a lekérdezések (beleértve a [összesítések](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) a partíciókon transzparens módon, nem kell minden olyan logikai hozzáadása az adatmennyiség növekedésével.

Idővel meg, idővel növekszik a forgalom és az erőforrás-használat (mérve [RUs](request-units.md), vagy a Kérelemegységek) növeli. Lesz és gyakrabban ír, a felhasználói bázis növekedésével, illetve azok indítsa el a létrehozása és több tartalmat; olvasása azon képessége, **skálázása az átviteli sebességet** létfontosságú. Növelje a RUs egyszerű, néhány kattintással az Azure Portalon teheti vagy a [az API-n keresztül parancsok kiadása](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Vertikális felskálázása és partíciókulcsot](./media/social-media-apps/social-media-apps-scaling.png)

Mi történik, ha a dolgok jobbak és a felhasználók egy másik régióba, ország vagy kontinens, figyelje meg, hogy a platformja, és azt, hogy milyen remek meglepő használatához!

Várjon..., de hamarosan kiderülhet a platform élményt nem optimális; azok, amennyiben a működési forrásadatok, hogy a késés Félelmetes, és természetesen nem szeretné, lépjen ki. Ha csak egy egyszerű módja volt **kiterjesztése a globális jelenlét**... de!

A cosmos DB lehetővé teszi, hogy [replikálja adatait globálisan](../cosmos-db/tutorial-global-distribution-sql-api.md) és transzparens módon mindössze pár kattintással, és automatikusan a rendelkezésre álló régiók közül választhat a [Ügyfélkód](../cosmos-db/tutorial-global-distribution-sql-api.md). Ez azt is jelenti, hogy is [több, feladatátvételi régióban](high-availability.md). 

Ha az adatok globális replikálása, győződjön meg arról, hogy az ügyfelek kihasználhatják ezt kell. Ha a webes előtérrendszer használata vagy API-k elérése a mobileszközről, telepíthet [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) , illetve klónozza őket az Azure App Service összes kívánt régiókhoz,-teljesítmény konfigurációt támogatása a kiterjesztett globális lefedettség. Amikor az ügyfelek hozzáférnek az előtér- vagy API-k, azok lesznek átirányítva a legközelebbi alkalmazás szolgáltatása, amely ezután kapcsolódik a Cosmos DB helyi replika.

![Globális lefedettség ad hozzá a közösségi platform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Összegzés
Ebben a cikkben próbálja meg néhány megvilágítja, az alternatív megoldások létrehozása közösségi hálózatokkal teljes egészében az Azure-ban az alacsony költségű services és a egy többrétegű tárolási megoldás és az adatok terjesztési "Skála" nevű használatát ösztönzésével nagyszerű eredményeket biztosít.

![Azure-közösségi hálózati szolgáltatások közötti interakció ábrája](./media/social-media-apps/social-media-apps-azure-solution.png)

Az igazság az, hogy nincs ilyen helyzetekben a Silver szintű listajele van, a felhívása hozta létre, amelyek lehetővé teszik számunkra, hogy kiváló felhasználói környezetek készíthetők nagyszerű szolgáltatások együttes használata: a sebességet és a egy kiváló közösségi alkalmazást, adja meg az Azure Cosmos DB szabadon a intelligencia mögött egy első osztályú keresési megoldás, mint például az Azure Search szolgáltatásban az Azure App Services alkalmazások nem is nyelvtől független, de hatékony háttérfolyamatok és a bővíthető Azure Storage és Azure SQL Database rugalmas tárolásához nagy mennyiségű adat, és ismeretek és intelligenciát, amely visszajelzést küldhet a folyamatok és segítsen létrehozása az Azure Machine Learning elemzési hatékonyságát a megfelelő tartalom továbbítására a megfelelő felhasználók.

## <a name="next-steps"></a>További lépések
Használati esetek Cosmos DB-bővebben lásd: [közös Cosmos DB használati esetek](use-cases.md).
