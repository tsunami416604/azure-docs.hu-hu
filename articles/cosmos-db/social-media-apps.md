---
title: 'Az Azure Cosmos DB kialakítási mintában: közösségi alkalmazások |} Microsoft Docs'
description: További tudnivalók a kialakítási mintában a közösségi hálózatokkal, ami a tárolás rugalmasságát Azure Cosmos adatbázis és az egyéb Azure-szolgáltatásokhoz.
keywords: Közösségi alkalmazások
services: cosmos-db
author: ealsur
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.openlocfilehash: d223187ab1f8338c57e6aa32903ea9b762393ab5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="going-social-with-azure-cosmos-db"></a>Az Azure Cosmos DB közösségi címen
A nagymértékben összekapcsolt társadalom élő azt jelenti, hogy a életben bármikor lesz része egy **közösségi hálózati**. Közösségi hálózatokkal való kapcsolattartásra ismerősök, munkatársakat, termékcsalád vagy néha nekünk kihívás megosztása élők közös érdekében használjuk.

Mérnökök és a fejlesztőknek, mint azt előfordulhat, hogy rendelkezik már azon, hogy hogyan lehet ezeket a hálózatokat tárolja és kapcsolódnak össze az adatokat, vagy előfordulhat, hogy rendelkezik még lett kiadta létrehozásához, vagy egy adott árkategóriájú piacra új közösségi hálózati yourselves tervezővel. Ha ez a nagy kérdése merül fel: ezek az adatok tárolási módjára?

Tegyük fel, hogy létrehozzuk egy új és csillogó közösségi hálózati, ahol a felhasználók cikkeket,:, képek, videók, vagy még akkor is, zene kapcsolódó adathordozó is közzé. A felhasználók bejegyzéseket fűzni és értékelések pontok biztosítják. A felhasználók által látható bejegyzéseket adatcsatornára lesz, és képesek legyenek kommunikálni a fő webhelyének kezdőlapja. Ez valóban összetett nem hangkártya (elsőre), de az egyszerűség kedvéért most állítja le van (azt egyéni felhasználói hírcsatornák kapcsolatok hatással lehetett elmélyedhet, de ez a cikk célja meghaladja).

Igen hogyan tegye tároljuk ez hol és?

Sokan előfordulhat, hogy rendelkezik tapasztalattal az SQL-adatbázisok vagy legalább fogalmát [relációs adatok modellezési](https://en.wikipedia.org/wiki/Relational_model) és indítsa el az alábbihoz hasonló rajzolási csábítónak:

![A relatív relációs modell ábrázoló diagram](./media/social-media-apps/social-media-apps-sql.png) 

A tökéletesen normalizált és közérthető adatszerkezet... nem méretezhető. 

Nem jelenik meg hibás, I korábban már használt SQL-adatbázisok a élettartama,-e nagy, de minden mintát, gyakorlat és szoftverek platform, például tökéletes nem minden forgatókönyvhöz.

Miért nem SQL ebben a forgatókönyvben a legjobb választás? Nézzük szerkezetének egyetlen post, ha az Excel megjelenítése a feladás egy vagy több egy webhelyhez vagy alkalmazáshoz, akkor van teendő tartalmazó lekérdezést... 8 táblákra (!) csak egy egyetlen post, most dinamikusan betöltött és jelennek meg a képernyő, és bejegyzéseket adatfolyam tapasztalhatja, ahol fogom kép megjelenítése.

Sikerült, természetesen használjuk humongous SQL-példány a megfelelő power lekérdezések ezer e illesztés kiszolgálni a tartalmat, de valóban, miért azt szeretné, ha létezik egy egyszerűbb megoldást a megoldására?

## <a name="the-nosql-road"></a>A nosql-alapú közúti
Ez a cikk végigvezeti az Azure NoSQL-adatbázis a közösségi platform adatok modellezését [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) miközben használhatja más Azure Cosmos DB költséghatékony funkciók, például a [Gremlin Graph API](../cosmos-db/graph-introduction.md). Használja a [NoSQL](https://en.wikipedia.org/wiki/NoSQL) módszert használja, JSON formátumban adatok tárolására és [denormalization](https://en.wikipedia.org/wiki/Denormalization), a korábban bonyolult post átalakíthatók egyetlen [dokumentum](https://en.wikipedia.org/wiki/Document-oriented_database):


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

És egyetlen lekérdezést, és nem csatlakozik szerezhető be. Ez sokkal több egyszerű és magától értetődő, és, budget-wise, kevesebb erőforrást a legjobb eredmény elérése érdekében van szükség.

Az Azure Cosmos DB gondoskodik arról, hogy a Tulajdonságok indexelt az automatikus indexeléshez az is lehet, amely [testreszabott](indexing-policies.md). A sémamentes megközelítés teszi lehetővé különböző és dinamikus struktúrák, talán holnap azt szeretnénk, ha a kategóriák és a hozzájuk társított hashtageket listája bejegyzéseket a dokumentumok tárolására, Cosmos DB az új dokumentumok nincs további feladata a hozzáadott attribútumokat fogja kezelni. szükséges lépünk Önnel.

A post fűzött megjegyzések csak más bejegyzéseket (Ez egyszerűbbé teszi az objektum leképezésének) szülő tulajdonsággal is kell kezelni. 

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

És minden közösségi kapcsolati is tárolni egy önálló objektumon számlálókat:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Hírcsatornák létrehozásakor csak egy adott fontossági sorrendben post azonosítók listáját tároló dokumentumok létrehozása:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Azt a létrehozási dátum szerint rendezve bejegyzéseket rendelkezhetnek a "legutóbbi" adatfolyam, ezeket a bejegyzéseket a "hottest" adatfolyam több kedveli az elmúlt 24 órában, akár sikerült megvalósítása az egyes felhasználók például followers és érdeklődési logikán alapuló egyéni adatfolyam és p listáját kell osts. Hogyan hozhat létre a listák kérdése, de az olvasási teljesítmény akadálytalan marad. Ha e listák valamelyikébe szerezni, hogy egyetlen lekérdezést kapni Cosmos DB használatával az [OPERÁTORBAN](sql-api-sql-query.md#WhereClause) bejegyzéseket lapjain beszerzése egyszerre.

Az adatcsatorna adatfolyamokat használatával épülhet [Azure App Services](https://azure.microsoft.com/services/app-service/) háttér-folyamatok: [Webjobs](../app-service/web-sites-create-web-jobs.md). A feladás egy vagy több létrehozása után a háttérben történő feldolgozás használatával is elindítható [Azure Storage](https://azure.microsoft.com/services/storage/) [várólisták](../storage/queues/storage-dotnet-how-to-use-queues.md) és használatával indított Webjobs a [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)végrehajtási, a Propagálás utáni saját egyéni logika alapuló belül. 

Ez a módszer használatával idővel konzisztenssé környezet késleltetett módon pontok és a post keresztül kedveli lehet feldolgozni.

Followers bonyolultabb. Cosmos DB méretkorlátja maximális dokumentum, és olvasási/írási nagy dokumentumok kedvezőtlen hatással lehet az alkalmazás méretezhetőségét. Ezért előfordulhat, hogy gondolja ennél a módszernél dokumentumként followers tárolására:

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

Ez néhány több ezer egy felhasználó működhet followers, de ha néhány celebrity csatlakozik-e az egyes holtversenyekben, a rendszer ezt a módszert előfordulhat, hogy a dokumentum nagy mérete, és előfordulhat, hogy végül elérte a dokumentum mérete kap.

A probléma megszüntetéséhez a vegyes megközelítés is használjuk. A felhasználói statisztikák a dokumentum részeként pedig followers száma tárolhatja:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

És followers aktuális grafikonját Azure Cosmos DB tárolható [Gremlin Graph API](../cosmos-db/graph-introduction.md), létrehozása [csomópontok](http://mathworld.wolfram.com/GraphVertex.html) minden felhasználó és [szélek](http://mathworld.wolfram.com/GraphEdge.html) , amely a "A-követi-B" karbantartása kapcsolatok. A Graph API-val most, nem csak egy adott felhasználó followers beszerzése, de bonyolultabb lekérdezéseket is javasoljuk a közös személyek. Azt adja hozzá a tartalomhoz, amelyet kategóriák, például vagy élvez a diagramhoz, ha is először Szövödei lép, amely tartalmazza az intelligens tartalom felderítési, tartalom arra vonatkozóan, hogy azokat azt kövesse, például vagy kikkel előfordulhat, hogy tudunk nagy közös személyek keresése.

A felhasználói statisztikák a dokumentum kártyák létrehozása a felhasználói felület vagy a gyors profil az előzetes verziójú funkciók továbbra is használható.

## <a name="the-ladder-pattern-and-data-duplication"></a>A "Létra" minta és az adatok párhuzamos
Észrevette, hogy a JSON-dokumentum a feladás egy vagy több hivatkozó, mert nincsenek a felhasználó többször. És meg szeretné rendelkezik kitalál jobb, ez azt jelenti, hogy több helyen jelen lehet-e a képviseli a felhasználót, a denormalization megadott adatokat.

Annak érdekében, hogy a gyorsabb lekérdezésekhez, hogy adat ismétlődik fel Önnek. Ezt a mellékhatást a szempontból problémás, hogy ha néhány művelet, a felhasználói adatok változásait, igazolnia kell a található összes tevékenység ezután legalább egyszer volt, és frissítse azokat az összes. Hang nagyon gyakorlati, jobb nem?

Egy olyan felhasználó, az alkalmazás minden egyes tevékenységhez megmutatjuk kulcs attribútumait azonosításával megoldására fogjuk. Ha azt vizuálisan megjeleníteni a feladás egy vagy több alkalmazás, és csak a létrehozásához használt nevét és a kép megjelenítése, miért tárolja a felhasználói adatok "hozott létre" attribútum? Minden egyes megjegyzés csak megmutatjuk, a felhasználó kép, ha valóban nem szükséges a többi a adatait. Ez a valami "létra minta" jelentkezem honnan szerepet.

Vegyük példaként a felhasználói adatokat:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Ezek az információk megtekintésével is gyorsan észlelünk fontos információkról, amely így létrehozása, amely nem, "Létra":

![A lépcsőzetes minta ábrája](./media/social-media-apps/social-media-apps-ladder.png)

A legkisebb lépés neve a UserChunk, a minimális adat, amely azonosítja a felhasználót, és az adatok duplikálva lettek-e szolgál. A duplikált adatok méretét csak a "bemutatjuk a" információ csökkentésével azt az esélye, nagy frissítések.

A középső lépés neve a felhasználó, a használni kívánt Cosmos DB, a leggyakrabban elért és a kritikus legtöbb teljesítmény-függő lekérdezések teljes adatokat. Egy UserChunk által képviselt információkat tartalmazza.

A legnagyobb az a kiterjesztett felhasználó. Ez magában foglalja az összes kritikus felhasználói adatokat és egyéb adatok valóban nem igényel gyorsan kell olvasni, vagy azok használatának végleges (például a bejelentkezési folyamat). Ezek az adatok tárolhatók kívül Cosmos DB, az Azure SQL Database vagy az Azure Storage-táblákat.

Miért kellene azt ossza fel a felhasználói és akkor is tárolni ezt az információt a különböző helyeken? Mivel a teljesítmény szempontjából, annál nagyobb a dokumentumok, a costlier lekérdezések. A szükséges megfelelő információt szeretne a közösségi hálózat összes a teljesítmény-függő lekérdezését és más adatok végleges forgatókönyvek például teljes profil módosításokat, a bejelentkezési adatok tárolására, akkor is igaz, a Használatelemzés és a Big Data típusú adatok adatbányászat slim dokumentumok megtartása kezdeményezések. Valóban nem ügyelünk esetén adatbányászathoz adatgyűjtési lassabb, mivel az Azure SQL Database-futtat, igazolnia kell vonatkoznak azonban, hogy a felhasználók rendelkeznek-e olyan gyors és slim környezetet. A felhasználó a Cosmos DB tárolt néz ki:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

És a Post alábbihoz hasonlóan fog kinézni:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

És szerkesztési merül fel, ahol a rendszer az attribútumok egyikét van hatással, esetén az érintett dokumentumok keresése, amelyek az indexelt attribútumok lekérdezések segítségével könnyen (válasszon * FROM visszaküldés p WHERE p.createdBy.id == "edited_user_id") és az adattömbök.

## <a name="the-search-box"></a>A keresési mezőbe
Felhasználók hoz létre, Szerencsére, nagy mennyiségű tartalmat. Azt adja meg a keresni, és nem történhet közvetlenül a saját tartalom adatfolyamot, lehet, hogy azt ne hajtsa végre a creators tartalom kereséséhez képesnek kell lennie, és lehet, hogy csak keressük, hogy régi post azt adta 6 hónapja kereséséhez.

Thankfully és az Azure Cosmos DB használjuk, mert azt is könnyen létrehozható egy keresési motor használata [Azure Search](https://azure.microsoft.com/services/search/) néhány percig, és egyetlen sor kódot (nyilvánvalóan eltérő, a keresési folyamata és felhasználói felület) beírása nélkül.

Miért van szó, ezért az easy?

Az Azure Search alkalmazza azok hívás [indexelők](https://msdn.microsoft.com/library/azure/dn946891.aspx), háttérben dolgozza fel, hogy az a adattárolók hook és automagically hozzáadása, módosítása, vagy távolítsa el az indexeket az objektumok. Támogatják-e egy [Azure SQL Database indexelők](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure BLOB indexelők](../search/search-howto-indexing-azure-blob-storage.md) és thankfully, [Azure Cosmos DB indexelők](../search/search-howto-index-documentdb.md). Az áttérés Azure Search Cosmos DB adatok egyszerű, mint mindkét információk tárolása JSON formátumban, csak ellenőriznünk kell [hozza létre az indexet](../search/search-create-index-portal.md) , és képezze le a dokumentumok melyik attribútumok indexelt szeretnénk, és azt, a egy percek (az adatok méretétől függően), a tartalom számára érhetők el, Search,--szolgáltatás a legjobb megoldást a felhőalapú infrastruktúrában lehet keresni. 

További információ az Azure Search fel is keresheti a [Hitchhiker's Guide keresés](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Az alapul szolgáló Tudásbázis
Után a tartalom, amely növekszik, és minden nap növekedésének tárolása, előfordulhat, hogy találtunk ragozott formáival végezni: milyen feladatokat lehet elvégezni az adatok az adatfolyam a felhasználók?

A válasz a kérdésre egyszerű: működik, és ismerje meg, tegye.

De mi azt megtudhatja? Néhány egyszerű példák [véleményeket elemzés](https://en.wikipedia.org/wiki/Sentiment_analysis), tartalom javaslatok a felhasználói beállítások vagy akár egy automatizált biztosítja, hogy a közösségi hálózat által közzétett összes tartalmat a termékcsalád biztonságos tartalom moderátor alapján.

Most, hogy Ön akadályoznia jelent, úgy fogja valószínűleg gondolja, hogy néhány doktori matematikai tudományos ezeket a mintákat és információk egyszerű adatbázisok és a fájlok kibontásához van szüksége, de lenne megfelelő.

[Az Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), része a [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), van a egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy a munkafolyamatokat algoritmusok használatát egy egyszerű felületen fogd és vidd kód a saját algoritmusok [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) , vagy használja a már létrehozott és használatra kész API-k, mint egy részénél: [Szövegelemzések](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [tartalom moderátor](https://www.microsoft.com/moderator) vagy [javaslatok](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Gépi tanulás forgatókönyvekben megvalósítására használhatjuk [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) különböző forrásokból származó adatok betöltési, és használata [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) feldolgozni az információkat, és egy kimenete, amely képes Azure Machine Learning feldolgozni.

Egy másik elérhető lehetőség [Microsoft kognitív szolgáltatások](https://www.microsoft.com/cognitive-services) tartalom; nem csak is tudjuk azok jobban felhasználónkkal elemzéséhez (keresztül elemzése, mi írják a [szöveg Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), de sikerült nemkívánatos vagy érett tartalom észlelése és is ennek megfelelően működjön a [számítógép Látástechnológiai API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Kognitív szolgáltatások közé tartoznak az out-of-az-box megoldások, amelyek nem igényelnek semmilyen használata a Machine Learning Tudásbázis számos.

## <a name="a-planet-scale-social-experience"></a>Egy bolygónk méretű közösségi élmény
A legutóbbi, de nem legalább a témakör fontos kell címezni: **méretezhetőség**. Az architektúrát, rendkívül fontos, hogy minden összetevő méretezheti önállóan, vagy mert igazolnia kell a további adatok feldolgozása tervezésekor, vagy mert azt szeretnénk, hogy egy nagyobb, melyek (vagy mindkét!). Thankfully, az összetett feladat elérése egy **kulcsrakész élmény** a Cosmos DB.

Cosmos DB támogatja [dinamikus particionálást](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of-az-box alapján létrehozott partícióknak automatikusan létrehozásával egy adott **partíciókulcs** (rendelkezésre álló dokumentumok attribútumokat megadva). A megfelelő partíciókulcs elvégzendő tervezési időben definiálása és figyelembe vételével a [ajánlott eljárások](../cosmos-db/partition-data.md#designing-for-partitioning) elérhető; a közösségi élményt, ha a particionálási stratégia igazodnia kell a lekérdezést hajt végre (olvasás belül azonos módon partíció kívánatos) írási és olvasási ("interaktív területek" elkerülheti, ha több partíciót az írási műveletek terjednek). Egyes beállítások akkor: a tartalom kategória, a felhasználó; a földrajzi régióban (nap/hónap/hét), ideiglenes kulcs alapján létrehozott partícióknak az összes valóban attól függ, hogyan fogja a lekérdezést, és szerepel, hogy a közösségi élményt nyújt. 

Érdemes megemlíteni érdekes pont, hogy a Cosmos DB futtatja-e a lekérdezések egy (beleértve a [összesítések](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) a partíciók közötti transzparens módon, akkor nem kell hozzáadnia bármely logika az adatok növekedésével.

Idővel, végül nőhet a forgalom és a hálózatierőforrás-fogyasztás (mért [RUs](request-units.md), vagy kérjen egységek) növeli. Lesz olvasási és írási gyakrabban, ha a userbase növekszik, és akkor indul, létrehozása és további tartalmat; olvasása képességét **az átviteli sebesség skálázás** nélkülözhetetlen. A RUs növelése nagyon egyszerű, azt megteheti az Azure portálon mindössze néhány kattintással vagy a [az API-n keresztül parancsok kiadása](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Vertikális felskálázásával és a partíciókulcs meghatározása](./media/social-media-apps/social-media-apps-scaling.png)

Mi történik, ha a dolgok folyton jobb és a felhasználók egy másik régióban, ország vagy kontinensen, figyelje meg, a platform és használatba, milyen kiváló nyújtható!

Várjon..., de azt hamarosan vegye figyelembe a felhasználói élmény a platformon nincs optimalizálva; Amennyiben távol is működési régiójától, hogy a várakozási Félelmetes, és nyilvánvalóan szeretné letiltani való kilépéshez. Ha csak a egyszerűen történt **kiterjesztése a globális reach**..., de nincs!

Cosmos DB lehetővé teszi, hogy [globálisan replikálja az adatokat](../cosmos-db/tutorial-global-distribution-sql-api.md) és transzparens módon végzett néhány kattintással, és automatikusan válassza ki az elérhető régiók között a [Ügyfélkód](../cosmos-db/tutorial-global-distribution-sql-api.md). Ez azt is jelenti, hogy rendelkezik [feladatátvétel több régióba](regional-failover.md). 

Amikor globálisan replikálja az adatokat, győződjön meg arról, hogy az ügyfelek is igénybe vehet fel kell. Ha egy webes előtér- vagy egypéldányú API-k mobilügyfelek használ, telepítheti [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) és az Azure App Service minden kívánt terület, a teljesítmény segítségével támogatása a kiterjesztett klónozni globális érvényességének. Amikor az ügyfelek hozzáférnek az előtérbeli vagy API-k, akkor továbbítja a, csatlakozik a helyi Cosmos adatbázis-replika legközelebbi App Service.

![A közösségi platform globális érvényességének hozzáadása](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Összegzés
Ez a cikk megpróbálja tisztázása néhány történő létrehozásának közösségi hálózatokkal teljesen Azure alacsony költségű szolgáltatásokkal és "Létra" nevű többrétegű tárolási megoldás és az adatok terjesztési használatának elősegítése nagy eredmények biztosítása alternatívák.

![Azure-közösségi hálózati szolgáltatások közötti interakció ábrája](./media/social-media-apps/social-media-apps-azure-solution.png)

A valóságnak, hogy az ilyen típusú forgatókönyvek nem ezüst listajele van, a hozta létre nagyszerű szolgáltatások, amelyek lehetővé teszik a számunkra hozhat létre nagyszerű lép kombinációja együttműködést: a sebesség és Azure Cosmos DB szabad kiváló közösségi alkalmazás, így a az eszközintelligencia mögött, például az Azure Search üzemeltetéséhez nem még nyelvtől független alkalmazások, de hatékony háttérfolyamatot és a bővíthető Azure Storage Azure App Service szolgáltatások és az Azure SQL Database rugalmasan tárolásához kiváló keresési megoldás nagy mennyiségű adatot és elemzési hatványra emelésének Azure Machine Learning Tudásbázis és az eszközintelligencia, amely visszajelzést a folyamatok és segítsen létrehozásához kézbesítése az arra jogosult felhasználók a megfelelő tartalom.

## <a name="next-steps"></a>További lépések
További információk a használati esetek Cosmos DB, lásd: [közös Cosmos DB használati esetekben](use-cases.md).