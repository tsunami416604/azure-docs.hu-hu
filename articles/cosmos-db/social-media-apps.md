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
ms.date: 11/14/2018
ms.author: maquaran
ms.openlocfilehash: a65ec0a92b8fc245c77ce67c80c1202f73a3ec66
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711871"
---
# <a name="going-social-with-azure-cosmos-db"></a>Közösségi tartalom az Azure Cosmos DB használatával

Egy nagy mértékben összekapcsolt társadalom élő azt jelenti, hogy életre valamely pontján, részévé válik a **közösségi hálózati**. Használhat közösségi hálózatokkal, hogy barátai, munkatársai, termékcsalád vagy más néven közös érdeklődési rendelkező személyek a szenvedélyét megosztani.

Szakemberek vagy fejlesztők számára akkor előfordulhat, hogy rendelkezik testreszabásakor nem tudta hogyan lehet ezeket a hálózatokat tárolja és ezek az adatok. Vagy, előfordulhat, hogy rendelkezik is lett kiadta létrehozásához vagy egy adott jelentethet piacra új közösségi hálózat tervezhet. Ha ez a jelentős kérdése merül fel: ezek az adatok tárolási módját?

Tegyük fel, ahol a felhasználók kapcsolódó adathordozók, például képek, videók vagy zene akár cikkeket is közzé az új és fényes közösségi hálózatokon hoz létre. A felhasználók bejegyzések fűzni és pontokat biztosíthat az értékelésekhez. Hírcsatorna felhasználók lesz és fő webhely kezdőlapján használhassa fel lesz. Ez a módszer nem összetett, hang-először, de az egyszerűség, most leállítás van. (Jobban is elmélyedne kapcsolatok által érintett egyéni felhasználói hírcsatornák, de a cél a cikk az ezenkívül.)

Tehát hogyan tegye tárolja ezeket az adatokat, és ahol?

Előfordulhat, hogy rendelkezik tapasztalattal az SQL-adatbázisokon vagy egy fogalma [relációs adatok modellezése](https://en.wikipedia.org/wiki/Relational_model). Hiba a következő rajzolási kezdheti meg:

![Relatív relációs modell ábrázoló diagram](./media/social-media-apps/social-media-apps-sql.png)

Tökéletesen normalizált és szép... olyan adatstruktúra nem méretezhető.

Nem kap meg helytelen dolgoztam az SQL Database-adatbázisok összes életem. Azok a nagy, de minden mintát, a gyakorlatban és a szoftverek platformhoz hasonló, nem tökéletes mindegyik forgatókönyv esetében.

Miért nem SQL ebben a forgatókönyvben a legjobb választás? Nézzük meg az egyedi közzétételek struktúráját. Ha bármit szeretnék csinálni, a bejegyzés megjelenítése a webhelyek vagy alkalmazások, e kellene do rendelkező lekérdezéshez... csak, egy egyetlen bejegyzés megjelenítése nyolc tables(!) csatlakoztatásával. Most már a bejegyzéseket, amelyek dinamikusan betölteni és jelennek meg a képernyőn egy adatfolyam kép, és láthatja, hogy hol fogom.

Ezer lekérdezést a tartalmat az illesztések megoldásához elég power hatalmas mennyiségű SQL-példány kreditért igénybe. De miért lenne, ha létezik egy egyszerűbb megoldást?

## <a name="the-nosql-road"></a>A nosql-alapú közúti

Ez a cikk végigvezeti Önt az Azure NoSQL-adatbázis a közösségi platform adatok modellezését, [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) költséghatékonyan. Azt is bemutatja, hogyan használja az egyéb Azure Cosmos DB-szolgáltatások, mint a [Gremlin API](../cosmos-db/graph-introduction.md). Használatával egy [nosql-alapú](https://en.wikipedia.org/wiki/NoSQL) megközelítés, adattárolás, a JSON-formátumban, és alkalmazása [denormalizáció](https://en.wikipedia.org/wiki/Denormalization), a korábban bonyolult bejegyzés egy átalakíthatók [dokumentum](https://en.wikipedia.org/wiki/Document-oriented_database):

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

És azt is közben, egyetlen lekérdezést, és nem csatlakozik. Ez a lekérdezés sokkal egyszerű és könnyen érthető megjegyzésblokkok írására, és, budget-wise, legjobb eredmény elérése érdekében kevesebb erőforrást igényel.

Az Azure Cosmos DB gondoskodik róla, hogy az összes tulajdonság automatikus indexelés az indexelt. Az Automatikus indexelés még akkor is lehet [testre szabott](index-policy.md). A séma nélküli megközelítés lehetővé teszi, hogy velünk a kapcsolatot a másik, a dinamikus struktúrák dokumentumok tárolására. Holnap szeretitek hozzászólások van egy listája azokról a kategóriák és a hozzájuk társított hashtageket? A cosmos DB fogja kezelni az új dokumentumok a felvett attribútumok az USA által igényelt további munka nélkül.

Hozzászólás fűzött megjegyzések is viselkedjen, mint a szülő tulajdonságot a többi hozzászólásétól. (Ez az eljárás leegyszerűsíti az objektum leképezésének.)

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

Adatcsatornák létrehozása a következő annyit, amelyet egy adott relevancia alapján végzett sorrendjében azonosítók post listáját tartalmazhat dokumentumok létrehozása:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

A "legutóbbi" stream-létrehozási dátuma szerint rendezve bejegyzések lehet. Vagy lehet-e az elmúlt 24 órában további kedvelések hozzászólásokat "legkeresettebb" adatfolyam. Akkor is Megvalósíthat egy egyéni stream minden egyes felhasználó követőinek és érdeklődése, így például logika alapján. A bejegyzések listájának továbbra is lenne. Gyorsan, hogyan hozhat létre a listák, de az olvasási teljesítmény akadálytalan marad. Ha e listák valamelyikébe szerez be, egyetlen lekérdezést kiadni Cosmos DB használatával a [OPERÁTORBAN](sql-api-sql-query.md#WhereClause) egyszerre bejegyzések lapjain beolvasásához.

Használja a hírcsatorna adatfolyamok sikerült beépített [Azure App Services](https://azure.microsoft.com/services/app-service/) háttér-folyamatok: [Webjobs](../app-service/web-sites-create-web-jobs.md). Bejegyzés létrehozása után a háttérben történő feldolgozás használatával is elindítható [Azure Storage](https://azure.microsoft.com/services/storage/) [üzenetsorok](../storage/queues/storage-dotnet-how-to-use-queues.md) és a Webjobs használatával aktivált a [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)végrehajtási, a Propagálás közzététele a saját egyéni logika alapján Streamek belül.

Pontok és a egy közleményre kedvelések ugyanezzel a módszerrel használatával egy végül konzisztens környezetet hozhat létre, a késleltetett módon lehet feldolgozni.

Követőinek olyan bonyolultabb. A cosmos DB-dokumentum méretkorlátja, és olvasási/írási nagy dokumentumok hatással lehet az alkalmazás teljesítményét. Ezért előfordulhat, hogy gondolja ennél a módszernél dokumentumként követők tárolására:

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

Ez a struktúra működhetnek néhány több ezer felhasználó követőinek. Néhány hírességek csatlakozik a holtversenyben, ha azonban ezt a megközelítést vezet a nagy dokumentumok méretétől, és végül ütközhet, ez a dokumentum mérete kap.

A probléma megoldásához használhatja a vegyes megközelítést. A felhasználói statisztikák dokumentum részeként tárolhatja a követők száma:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Tárolhatja az Azure Cosmos DB használatával követők a tényleges diagram [Gremlin API](../cosmos-db/graph-introduction.md) létrehozása [csúcspontokat](http://mathworld.wolfram.com/GraphVertex.html) minden felhasználó és [élek](http://mathworld.wolfram.com/GraphEdge.html) , amely a "A-követi – B" karbantartása kapcsolat. A Gremlin API-val egy adott felhasználó a követők beolvasása, és javasolja a felhasználók közös összetettebb lekérdezéseket hozzon létre. A a tartalom kategóriák alapmodell, amit, például vagy élvezze gráfhoz hozzáadásakor, amelyek tartalmazzák az intelligens Tartalomkeresés szövödei, amely arra utal, hogy ezek az emberek kövesse hasonlóan, vagy találja azok, akik, amikor tartalmat előfordulhat, hogy nagy részét a common is elindítható.

A felhasználói statisztikák a dokumentum-kártyák létrehozása a felhasználói felületen vagy a gyors profil előnézetek továbbra is használható.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Skála" minta és az adatok duplikálása

Ahogy az a JSON-dokumentum, amely hivatkozik a hozzászólás talán észrevette, nincsenek a felhasználó sok előfordulását. És meg szeretné rendelkezik kitalálni megfelelő, ezeket az ismétlődéseket, az azt jelenti, hogy egy felhasználó adott ez denormalizáció leíró adatokat szerepelhet egynél több helyen.

Ahhoz, hogy a lekérdezések, adatdeduplikáció számítunk fel. Ezt a mellékhatást problémája, hogy ha az egyes műveletekhez, a felhasználói adatok módosításait, meg kell keresnie az összes tevékenység a felhasználó valaha volt és frissíteni őket az összes. Nem megbízható gyakorlati, jobb?

Megoldhatja a problémát egy felhasználó az alkalmazás minden egyes tevékenységhez megjelenítő legfőbb attribútumai azonosításával fog. Ha vizuálisan bejegyzés megjelenítése az alkalmazásban, és csak a létrehozó nevét és a kép megjelenítéséhez, miért tárolja a felhasználói adatok a "createdBy" attribútumot? Minden megjegyzést, csak meg a felhasználó képe, ha az nem feltétlenül szükséges a felhasználói adatok részeit. Ez, ha valami hívhatom meg a "létra minta" lesz szó.

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

A középső lépés neve a felhasználó. A teljes adatokat a Cosmos DB, a leggyakrabban elért és a kritikus fontosságú a legtöbb teljesítmény-függő lekérdezéseket kell használni. Egy UserChunk által képviselt információkat tartalmazza.

A legnagyobb az a kiterjesztett felhasználó. Ez magában foglalja a kritikus felhasználói adatokat és egyéb adatok, amelyhez nincs szükség, gyorsan kell olvasni, vagy végleges használatot, például a bejelentkezési folyamat rendelkezik. Ezeket az adatokat a Cosmos DB, az Azure SQL Database vagy Azure Storage-táblák kívül tárolható.

Miért volna, a felhasználó felosztása és akár különböző helyeken tárolja? Mivel a teljesítmény szempontjából, annál nagyobb a dokumentumokat, a costlier a lekérdezéseket. Dokumentumok a megfelelő információkkal, ehhez minden a teljesítmény-függő lekérdezések a közösségi hálózat slim megtartása. Store végleges forgatókönyvek esetén a másik további információkat, például teljes profil módosításokat, bejelentkezések és használatelemzési információkat és a big Data típusú adatok kezdeményezések adatbányászat. Valóban nem olyan fontos-e az adatgyűjtési data szintű adatbányászatra lassabb, mivel az Azure SQL Database futtat. Ön rendelkezik vonatkoznak azonban, hogy a felhasználók rendelkeznek-e egy gyors és slim élményt. A Cosmos DB tárolja a felhasználó ehhez a kódhoz hasonlóan néz ki:

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

Ha szerkesztési merül fel, ahol egy adattömb attribútum hatással van, megtalálhatja az érintett dokumentumokat. Használja őket, amelyek az indexelt attribútumok, például a lekérdezések `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, majd frissítse az adattömböket.

## <a name="the-search-box"></a>A keresőmezőbe

Felhasználók generál hetünk, sok tartalom. Akkor keressen, és keresse meg a tartalmat, amely nem lehet közvetlenül a saját tartalomfolyamokat talán mert ne hajtsa végre a létrehozói tárolókiszolgálóhoz képesnek kell lennie, és esetleg csak szeretne, hogy régi post tette hat hónapja található.

Azure Cosmos DB használja, mert könnyen valósítható meg a keresési motor használatával [Azure Search](https://azure.microsoft.com/services/search/) bármilyen kód, nem a keresési folyamata és a felhasználói felület nélkül, néhány perc múlva.

Miért van olyan egyszerű, ez a folyamat?

Az Azure Search valósítja meg, milyen hívják meg [indexelők](https://msdn.microsoft.com/library/azure/dn946891.aspx), háttérben dolgozza fel, hogy az adatok tárházakban hook és automagically hozzáadása, frissítése vagy távolítsa el az indexeket az objektumok. Támogatják-e egy [Azure SQL Database-indexelők](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure BLOB-indexelők](../search/search-howto-indexing-azure-blob-storage.md) és Szerencsére [Azure Cosmos DB-hez indexelők](../search/search-howto-index-documentdb.md). Információk Cosmos DB-ből az Azure Search az áttérés rendkívül egyszerű. A két technológia tárolhatók JSON formátumban, így egyszerűen [az Index létrehozása](../search/search-create-index-portal.md) és a a kívánt indexelt dokumentumok származó attribútumok leképezésére. Készen is van. Az adatok méretétől függően minden tartalom alapján percen belül szerint keresni a legjobb keresése –-szolgáltatásként megoldást a felhőalapú infrastruktúrák számára elérhető lesz.

További információ az Azure Search, keresse fel a [Hitchhiker's Guide Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Az alapul szolgáló ismeretek

A tartalom, amely növekszik, és minden nap növekszik rendezést követően gondolkodás találhatja: Mi a teendő az adatok az adatfolyam a felhasználóim?

A válasz nagyon egyszerű:, működik, és ismerje meg, hogy.

De mi is elsajátíthatja? Néhány egyszerű példák [hangulatelemzés](https://en.wikipedia.org/wiki/Sentiment_analysis), tartalom, javaslatokat a felhasználói beállítások alapján, vagy akár egy automatizált biztosítja, hogy a tartalmat, a közösségi hálózat által közzétett a content moderator eszköze biztonságosan a család.

Most, hogy szükségem van csatlakoztatva, valószínűleg gondolja lesz szüksége ezen mintákat és adatokat egyszerű adatbázisok és a fájlok kibontásához a matematikai adatelemzési néhány PhD, de lenne megfelelő.

[Az Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)részeként elérhető a [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), van egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi az algoritmusok használatával egy egyszerű fogd és vidd felületén hozzon létre munkafolyamatokat, a saját algoritmusokhoz code[ R](https://en.wikipedia.org/wiki/R_\(programming_language\)), vagy használja a már létrehozott és készen áll, mint például az API-k használata: [Szövegelemzés](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [a Content Moderator, vagy [javaslatok](https://gallery.azure.ai/Solution/Recommendations-Solution).

A Machine Learning esetekben eléréséhez használható [az Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) , hogy a különböző forrásokból származó adatokat. Is [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) feldolgozni az információkat, és a egy kimeneti Azure Machine Learning által feldolgozható létrehozásához.

Egy másik elérhető lehetőség [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) elemezheti a felhasználók számára tartalom; csak nem is megismeri őket jobban (keresztül elemzése, mi írnak a [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), de sikerült is nemkívánatos vagy érett tartalom észlelése és az azoknak megfelelő cselekvést a [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). A cognitive Services számos out-az-megoldások, amelyek nem igényelnek a Machine Learning-ismeretek használata bármilyen típusú tartalmazza.

## <a name="a-planet-scale-social-experience"></a>Egy globális közösségi élmény

Egy utolsó, de nem utolsósorban fontos cikk kell címezni: **méretezhetőség**. Az architektúrák tervezésekor egyes összetevő önállóan kell méretezhető. Végül kell további adatokat feldolgozni, vagy egy nagyobb földrajzi lefedettségével rendelkezik célszerű. Szerencsére éri el mindkét feladat van egy **kulcsrakész megoldást** a Cosmos DB használatával.

A cosmos DB támogatja a [dinamikus particionálás](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) -a-beépített. Alapján automatikusan létrehoz egy adott **partíciókulcs**, amely a dokumentumok attribútumaként van definiálva. A megfelelő partíciókulcs meghatározása a tervezés során kell elvégezni. További információkért lásd: a [válassza ki a megfelelő partíciókulcs](partitioning-overview.md#choose-partitionkey) cikk.

Közösségi élményt el kell végeznie az írási és lekérdezési módja a particionálási stratégia igazítását. (Például olvasási ugyanazon a partíción belül kívánatos, és kerülje a "hotspotok" osztja szét a írási művelet több partíciót.) Egyes beállítások akkor: tartalom kategória szerint, földrajzi régió vagy felhasználó által a historikus kulcs (nap/hó/hét) alapján partíciókat. Az összes valóban attól függ, hogyan fog kérdezni az adatokat, és az adatok megjelenítése a közösségi tapasztalatait.

A cosmos DB fog futni a lekérdezések (beleértve a [összesítések](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) a partíciókon transzparens módon, így nem kell minden olyan logikai hozzáadása az adatmennyiség növekedésével.

Idővel meg fog végül növekedés a forgalom és az erőforrás-használat (mérve [RUs](request-units.md), vagy a Kérelemegységek) növeli. Akkor lesz olvasási és írási több gyakran a felhasználói bázis növekedésével. A felhasználói bázis létrehozása és olvasása további tartalmat indul el. Ezért a képessége **skálázása az átviteli sebességet** létfontosságú. Növelje a RUs ördöngösség. Ezt megteheti az Azure Portalon vagy a pár kattintással [az API-n keresztül parancsok kiadása](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Vertikális felskálázása és partíciókulcsot](./media/social-media-apps/social-media-apps-scaling.png)

Mi történik, ha a dolgok jobbak? Tegyük fel, hogy egy másik régióba, ország, vagy egy kontinenst a felhasználók figyelje meg, hogy a platformja és használatának megkezdéséhez. Milyen egy nagyszerű meglepetés!

De várja! Hamarosan vegye figyelembe, a platform élményt nem optimális. Azok, amennyiben a működési forrásadatok, hogy a késés Félelmetes-e. Természetesen nem szeretné, lépjen ki. Ha csak egy egyszerű módja volt **kiterjesztése a globális jelenlét**? Van!

A cosmos DB lehetővé teszi, hogy [replikálja adatait globálisan](../cosmos-db/tutorial-global-distribution-sql-api.md) és transzparens módon mindössze pár kattintással, és automatikusan a rendelkezésre álló régiók közül választhat a [Ügyfélkód](../cosmos-db/tutorial-global-distribution-sql-api.md). Ez a folyamat is jelenti, hogy is [több, feladatátvételi régióban](high-availability.md).

Ha az adatok globális replikálása, győződjön meg arról, hogy az ügyfelek kihasználhatják ezt kell. Ha a webes előtérrendszer használata vagy API-k elérése a mobileszközről, telepíthet [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) , illetve klónozza őket az Azure App Service összes kívánt régiókhoz,-teljesítmény konfigurációt támogatása a kiterjesztett globális lefedettség. Amikor az ügyfelek hozzáférnek az előtér- vagy API-k, azok a legközelebbi alkalmazás szolgáltatása, amely ezután kapcsolódik a Cosmos DB helyi replika fog átirányíthatók.

![Globális lefedettség ad hozzá a közösségi platform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Összegzés

Ez a cikk néhány feltárja azokat a lehetőségeket, közösségi hálózatok létrehozásának teljesen az Azure-ban az alacsony költségű szolgáltatásokkal. egy többrétegű tárolási megoldás és az adatok terjesztési "Skála" nevű használatát ösztönzésével eredményeket kínál.

![Azure-közösségi hálózati szolgáltatások közötti interakció ábrája](./media/social-media-apps/social-media-apps-azure-solution.png)

Az igazság az, hogy nincs ilyen helyzetekben a Silver szintű listajele nem létezik. A felhívása hozta létre, amelyek lehetővé teszik számunkra, hogy kiváló felhasználói környezetek készíthetők nagyszerű szolgáltatások együttes használata: a sebesség és szabadságára az Azure Cosmos DB egy kiváló közösségi alkalmazást, az eszközintelligencia mögött egy első osztályú keresési megoldás, mint például az Azure Search szolgáltatásban a a nagy mennyiségű adat tárolására az Azure Machine Learning, az elemzési power Azure App Services alkalmazások nem is nyelvtől független, de hatékony háttérfolyamatok és a bővíthető Azure Storage és Azure SQL Database rugalmas Tudásbázis létrehozása, és intelligenciát, amely visszajelzést küldhet a folyamatok és segítsen a megfelelő tartalom továbbítására a megfelelő felhasználók.

## <a name="next-steps"></a>További lépések

Használati esetek Cosmos DB-bővebben lásd: [közös Cosmos DB használati esetek](use-cases.md).
