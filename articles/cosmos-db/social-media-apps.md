---
title: 'Az Azure Cosmos DB tervezési mintája: Közösségi média alkalmazások'
description: Ismerje meg a közösségi hálózatok tervezési mintáját az Azure Cosmos DB és más Azure-szolgáltatások tárolási rugalmasságának kihasználásával.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827298"
---
# <a name="going-social-with-azure-cosmos-db"></a>Közösségi kapcsolat az Azure Cosmos DB-val

Élet egy masszívan összekapcsolt társadalom azt jelenti, hogy egy bizonyos ponton az életben, akkor részévé válik a **szociális háló**. A közösségi hálózatok segítségével tartja a kapcsolatot barátaival, munkatársaival, családjával, vagy néha megoszthatja szenvedélyét a közös érdeklődésű emberekkel.

Mérnökökként vagy fejlesztőkként talán kíváncsi lehet arra, hogyan tárolják és kapcsolják össze az adatokat. Vagy lehet, hogy még az a feladata, hogy hozzon létre vagy építész egy új szociális háló egy adott piaci rést. Ekkor merül fel a jelentős kérdés: Hogyan tárolják ezeket az adatokat?

Tegyük fel, hogy egy új és fényes közösségi hálózatot hoz létre, ahol a felhasználók cikkeket tehetnek közzé kapcsolódó médiával, például képekkel, videókkal vagy akár zenével. A felhasználók megjegyzéseket fűzhetnek a bejegyzésekhez, és pontokat adhatnak az értékelésekhez. Lesz egy feed álláshely, hogy a felhasználók látni fogják, és kölcsönhatásba lépnek a fő weboldal nyitóoldalon. Ez a módszer nem hangzik bonyolult az első, de az egyszerűség kedvéért, álljunk meg ott. (A kapcsolatok által érintett egyéni felhasználói hírcsatornákba áshat, de ez túlmutat a cikk célján.)

Szóval, hogyan tárolja ezeket az adatokat, és hol?

Lehet, hogy van tapasztalata az SQL-adatbázisokban, vagy rendelkezik az [adatok relációs modellezésével.](https://en.wikipedia.org/wiki/Relational_model) A rajzot a következőképpen kezdheti el rajzolni:

![Relatív relációs modellt bemutató diagram](./media/social-media-apps/social-media-apps-sql.png)

Egy tökéletesen normalizált és szép adatstruktúra... Ez nem méretarányos.

Ne érts félre, egész életemben SQL adatbázisokkal dolgoztam. Nagyszerűek, de mint minden minta, gyakorlat és szoftverplatform, ez nem tökéletes minden forgatókönyvhöz.

Miért nem az SQL a legjobb választás ebben a forgatókönyvben? Nézzük meg a szerkezet egy post. Ha meg akartam mutatni a post egy honlapon vagy alkalmazás, én volna, hogy csinál egy lekérdezést ... csatlakozva nyolc táblázat (!) csak azért, hogy megjelenjen egy post. Most képzelj el egy olyan hozzászólásfolyamot, amely dinamikusan töltődik be és jelenik meg a képernyőn, és talán látod, hová megyek.

Használhatja egy hatalmas SQL-példány elég erővel, hogy megoldja a lekérdezések ezreit sok csatlakozik, hogy szolgálja a tartalmat. De miért tennéd, ha létezik egy egyszerűbb megoldás?

## <a name="the-nosql-road"></a>A NoSQL út

Ez a cikk bemutatja, hogy miként modellezheti a közösségi platform adatait az Azure NoSQL-adatbázisával, [az Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/) DB-vel. Azt is bemutatja, hogyan használhatja az Azure Cosmos DB egyéb funkcióit, például a [Gremlin API-t.](../cosmos-db/graph-introduction.md) A [NoSQL](https://en.wikipedia.org/wiki/NoSQL) megközelítés, az adatok tárolása, A JSON formátumban és alkalmazása [denormalizáció](https://en.wikipedia.org/wiki/Denormalization), a korábban bonyolult post lehet alakítani egy [dokumentum:](https://en.wikipedia.org/wiki/Document-oriented_database)

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

És lehet ütött egyetlen lekérdezést, és nem csatlakozik. Ez a lekérdezés sokkal egyszerű és egyszerű, és a költségvetés szempontjából kevesebb erőforrást igényel a jobb eredmény eléréséhez.

Az Azure Cosmos DB gondoskodik arról, hogy az összes tulajdonság indexelve az automatikus indexelés. Az automatikus indexelés még testre is [szabható](index-policy.md). A sémamentes megközelítés lehetővé teszi, hogy különböző és dinamikus struktúrákkal rendelkező dokumentumokat tároljunk. Lehet, hogy holnap szeretné álláshely, hogy egy listát a kategóriák vagy hashtagek velük kapcsolatban? Cosmos DB fogja kezelni az új dokumentumok a hozzáadott attribútumok nélkül extra munkát igényel nekünk.

A bejegyzéshez fűzött hozzászólásokat szülőtulajdonsággal rendelkező egyéb bejegyzésekként lehet kezelni. (Ez a gyakorlat leegyszerűsíti az objektumleképezést.)

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

És minden társadalmi interakció tárolható egy külön objektumon számlálóként:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

A hírcsatornák létrehozása csak olyan dokumentumok létrehozása, amelyek egy adott relevancia-sorrenddel rendelkező postaazonosítók listáját is eltudják tartani:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Lehet, hogy egy "legújabb" patak álláshely szerint megrendelt létrehozási dátum. Vagy lehet egy "legforróbb" patak azokkal álláshely több szereti az elmúlt 24 órában. Akár egy egyéni adatfolyamot is megvalósíthat minden felhasználó számára logika, például követők és érdeklődési körök alapján. Ez még mindig egy listát a hozzászólások. Ez a kérdés, hogyan kell építeni ezeket a listákat, de az olvasási teljesítmény marad akadálytalan. Miután megszerezte az egyik ilyen listák, akkor kiad egy lekérdezést Cosmos DB segítségével az [IN kulcsszót](sql-query-keywords.md#in) kap oldalak álláshely egy időben.

A hírcsatorna-adatfolyamok az [Azure App Services](https://azure.microsoft.com/services/app-service/) háttérfolyamatai: [Webjobs](../app-service/webjobs-create.md)segítségével is létrehozhatók. A bejegyzés létrehozása után a háttér-feldolgozás az Azure [Storage-várólisták](../storage/queues/storage-dotnet-how-to-use-queues.md) és az [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)használatával indított webfeladatok használatával indítható el, a saját egyéni logikája alapján megvalósítva a bejegyzéses adatfolyamokon belüli propagálást. [Azure Storage](https://azure.microsoft.com/services/storage/)

Pontok és szereti egy post lehet feldolgozni halasztott módon ezzel a technikával, hogy hozzon létre egy végül következetes környezetben.

A követők trükkösebbek. A Cosmos DB dokumentumméret-korláttal rendelkezik, és a nagy dokumentumok olvasása/írása hatással lehet az alkalmazás méretezhetőségére. Így lehet gondolni tárolására követői, mint egy dokumentum ezzel a szerkezettel:

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

Ez a struktúra működhet a felhasználó néhány ezer követője. Ha azonban néhány híresség csatlakozik a soraihoz, ez a megközelítés nagy dokumentummérethez vezet, és végül megütheti a dokumentum méretkorlátját.

A probléma megoldásához vegyes megközelítést használhat. A Felhasználói statisztika dokumentum részeként tárolhatja a követők számát:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

A követők aktuális grafikonját az Azure Cosmos DB [Gremlin API használatával](../cosmos-db/graph-introduction.md) tárolhatja, hogy [csúcspontokat](http://mathworld.wolfram.com/GraphVertex.html) hozzon létre minden egyes felhasználó és az "A-follows-B" kapcsolatokat fenntartó [élek](http://mathworld.wolfram.com/GraphEdge.html) számára. A Gremlin API-val lekaphatja egy bizonyos felhasználó követőit, és összetettebb lekérdezéseket hozhat létre, hogy javasoljon közös embereket. Ha hozzáadja a grafikonhoz azokat a tartalomkategóriákat, amelyeket az emberek szeretnek vagy élveznek, elkezdheti az intelligens tartalomfelderítést tartalmazó élmények szövését, olyan tartalmakat javasolva, amelyeket az Ön által követett emberek kedvelnek, vagy olyan embereket találhat, akikkel sok közös lehet.

A Felhasználói statisztika dokumentum továbbra is használható kártyák létrehozására a felhasználói felületen vagy a gyorsprofil-előnézetekben.

## <a name="the-ladder-pattern-and-data-duplication"></a>A "Létra" minta és az adatok megkettőzése

Amint azt a JSON-dokumentumban is észrevette, amely egy bejegyzésre hivatkozik, a felhasználónak sok előfordulása van. És akkor volna kitalálni jobb, ezek az ismétlődések azt jelenti, hogy az információ, amely leírja a felhasználó, mivel ez a denormalizáció, lehet jelen több helyen.

A gyorsabb lekérdezések érdekében adatmásolást kell fellépni. A probléma ezzel a mellékhatással az, hogy ha valamilyen művelettel a felhasználó adatai megváltoznak, meg kell találnia az összes tevékenységet, amelyet a felhasználó valaha is végzett, és frissítenie kell őket. Nem hangzik praktikusnak, igaz?

Az alkalmazásban az egyes tevékenységekhez megadott kulcsattribútumok azonosításával fogja megoldani. Ha vizuálisan megjelenít egy bejegyzést az alkalmazásban, és csak az alkotó nevét és képét jeleníti meg, miért tárolja a felhasználó összes adatát a "createdBy" attribútumban? Ha minden egyes megjegyzéshez csak a felhasználó képét jeleníti meg, akkor nem igazán van szüksége a felhasználó többi információjára. Ez az, ahol valami hívom a "Létra minta" lesz szó.

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

Ha megnézzük ezt az információt, akkor gyorsan érzékeli, amely a kritikus információkat, és amely nem, így létrehozva a "Létra":

![Rajz egy létra minta](./media/social-media-apps/social-media-apps-ladder.png)

A legkisebb lépés az úgynevezett UserChunk, a minimális információ, amely azonosítja a felhasználót, és az adatok másolása. Azáltal, hogy a duplikált adatok méretét csak a "show" információra csökkenti, csökkenti a tömeges frissítések lehetőségét.

A középső lépés neve a felhasználó. Ez a teljes adatok, amelyek a legtöbb teljesítményfüggő lekérdezések cosmos DB, a leggyakrabban használt és kritikus. A UserChunk által képviselt információkat tartalmazza.

A legnagyobb a kiterjesztett felhasználó. Ez magában foglalja a kritikus felhasználói adatokat és egyéb adatokat, amelyeket nem kell gyorsan olvasni, vagy a bejelentkezési folyamat, végleges használat. Ezek az adatok a Cosmos DB-n kívül, az Azure SQL Database vagy az Azure Storage Tables rendszerben tárolhatók.

Miért osztaná fel a felhasználót, és tárolná ezeket az információkat különböző helyeken? Mivel a teljesítmény szempontjából, minél nagyobb a dokumentumok, a költségesebb a lekérdezések. Tartsa a dokumentumokat karcsú, a megfelelő információkat, hogy tegyenek meg minden teljesítmény-függő lekérdezések a szociális háló. A további további információkat olyan lehetséges esetekhez tárolhatja, mint a teljes profilszerkesztés, a bejelentkezések és az adatbányászat a használati elemzések hez és a Big Data-kezdeményezésekhez. Tényleg nem érdekel, ha az adatgyűjtés az adatbányászat lassabb, mert fut az Azure SQL Database.You really don't t care if the data gathering for data mining is lower, because it's running on Azure SQL Database. Van azonban aggodalomra ad okot, hogy a felhasználók egy gyors és karcsú tapasztalat. A Cosmos DB-n tárolt felhasználó a következő kódnak tűnik:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

És egy Post nézne ki:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Ha egy szerkesztés olyan szerkesztési lehetőség keletkezik, ahol egy adattömbattribútum érintett, könnyen megtalálhatja az érintett dokumentumokat. Csak használjon olyan lekérdezéseket, amelyek `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`az indexelt attribútumokra mutatnak, például a , majd frissítse az adattömböket.

## <a name="the-search-box"></a>A keresőmező

Használók akarat létrehoz, szerencsésen, sok elégedett. És képesnek kell lennie arra, hogy olyan tartalmakat keressen és találjon, amelyek nem közvetlenül a tartalomfolyamaikban vannak, talán azért, mert nem követi az alkotókat, vagy talán csak megpróbálja megtalálni azt a régi bejegyzést, amelyet hat hónappal ezelőtt tett.

Mivel az Azure Cosmos DB-t használja, az [Azure Cognitive Search](https://azure.microsoft.com/services/search/) használatával néhány perc alatt egyszerűen megvalósíthat egy keresőmotort anélkül, hogy bármilyen kódot beírna, kivéve a keresési folyamatot és a felhasználói felületet.

Miért olyan egyszerű ez a folyamat?

Az Azure Cognitive Search megvalósítja az általuk [indexelőknek](https://msdn.microsoft.com/library/azure/dn946891.aspx)nevezett folyamatokat, amelyek az adattárakban lévő háttérfolyamatokat, valamint az indexekben lévő objektumok automatikus annektálását automatikusan hozzáadódnak, frissítik vagy távolítják el. Támogatják az [Azure SQL Database indexelőit,](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/) [az Azure Blobok indexelőit](../search/search-howto-indexing-azure-blob-storage.md) és szerencsére [az Azure Cosmos DB indexelőit.](../search/search-howto-index-documentdb.md) A Cosmos DB-ről az Azure Cognitive Search szolgáltatásra való áttérés egyszerű. Mindkét technológia JSON formátumban tárolja az információkat, ezért csak létre kell [hoznia az indexet,](../search/search-create-index-portal.md) és le kell képeznie az indexelni kívánt dokumentumok attribútumait. Készen is van. Az adatok méretétől függően az összes tartalom perceken belül elérhető lesz, hogy a legjobb szolgáltatásként keressen megoldás a felhőalapú infrastruktúrában.

Az Azure Cognitive Search szolgáltatással kapcsolatos további információkért látogasson el a [Stopposok keresési útmutatójába.](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)

## <a name="the-underlying-knowledge"></a>Az alapul szolgáló tudás

Tárolása után ez a tartalom, hogy a nő és nő minden nap, akkor lehet találni gondolkodás: Mit tehetek az összes ezt az információt a felhasználók?

A válasz egyszerű: Tedd a munkát, és tanulni belőle.

De mit tanulhatsz? Néhány egyszerű példa a [hangulatelemzésre](https://en.wikipedia.org/wiki/Sentiment_analysis), a felhasználó preferenciáin alapuló tartalomjavaslatokra, vagy akár egy automatikus tartalommoderátorra, amely gondoskodik arról, hogy a közösségi hálózat által közzétett tartalom biztonságos legyen a család számára.

Most, hogy rászoktattam, valószínűleg azt fogod hinni, hogy phD-re van szükséged matekból, hogy kinyerd ezeket a mintákat és információkat egyszerű adatbázisokból és fájlokból, de tévedsz.

[Az Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), amely a [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)része , egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi, hogy algoritmusok használatával hozzon létre munkafolyamatokat egy egyszerű drag-and-drop felületen, kódolja saját algoritmusait az [R-ben,](https://en.wikipedia.org/wiki/R_\(programming_language\))vagy használja a már beépített és használatra kész API-kat, például: Text [Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator vagy [Recommendations](https://gallery.azure.ai/Solution/Recommendations-Solution).

A Machine Learning-forgatókönyvek bármelyikének eléréséhez használhatja az [Azure Data Lake-et](https://azure.microsoft.com/services/data-lake-store/) a különböző forrásokból származó információk betöltéséhez. Az [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) segítségével is feldolgozhatja az adatokat, és létrehozhat egy kimenetet, amelyet az Azure Machine Learning feldolgozhat.

Egy másik elérhető lehetőség az [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) használata a felhasználók tartalmának elemzésére; nem csak jobban megértheti őket (a [Text Analytics API-val](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)írt adatok elemzésével), hanem észlelheti a nem kívánt vagy érett tartalmat is, és ennek megfelelően járhat el [a Computer Vision API-val.](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) A Cognitive Services számos olyan beépített megoldást tartalmaz, amelyek használatához nincs szükség semmilyen Machine Learning-tudáshasználatára.

## <a name="a-planet-scale-social-experience"></a>A bolygó-méretű társadalmi tapasztalat

Van egy utolsó, de nem utolsósorban, fontos cikket kell foglalkozni: **skálázhatóság**. Architektúra tervezésekor minden összetevőnek saját maga kell méreteznie. Előbb-utóbb több adatot kell feldolgoznia, vagy nagyobb földrajzi lefedettséget szeretne. Szerencsére mindkét feladat elérése **kulcsrakész élmény** a Cosmos DB-vel.

Cosmos DB támogatja a dinamikus particionálás out-of-the-box. Automatikusan létrehoz partíciókat egy adott **partíciókulcs**alapján, amely a dokumentumokban attribútumként van definiálva. A megfelelő partíciókulcs meghatározását a tervezés időpontjában kell elvégezni. További információ: [Particionálás az Azure Cosmos DB.For](partitioning-overview.md)more information, see Partiing in Azure Cosmos DB.

A közösségi élmény érdekében össze kell hangolnia a particionálási stratégiát a lekérdezés és az írás módjához. (Például az ugyanazon a partíción belüli olvasások kívánatosak, és elkerülheti a "forró pontokat" azáltal, hogy több partícióra terjeszti az írásokat.) Néhány lehetőség: időbeli kulcson (nap/hónap/hét), tartalomkategória, földrajzi régió vagy felhasználó szerint alapuló partíciók. Mindez attól függ, hogyan fogja lekérdezni az adatokat, és megjeleníteni az adatokat a közösségi élményben.

A Cosmos DB transzparens módon futtatja a lekérdezéseket (beleértve az [összesítéseket](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)is), így az adatok növekedésével nem kell logikát hozzáadnia.

Idővel végül növekszik a forgalom, és az erőforrás-felhasználás [(mérve rt-k,](request-units.md)vagy kérelem egységek) növekedni fog. A felhasználói bázis növekedésével gyakrabban fog olvasni és írni. A felhasználói bázis további tartalmak létrehozását és olvasását kezdi meg. Ezért az **átviteli képesség skálázása** létfontosságú. A rt-k növelése egyszerű. Ezt néhány kattintással megteheti az Azure Portalon, vagy [parancsokat adhat ki az API-n keresztül.](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer)

![Felskálázás és partíciókulcs definiálása](./media/social-media-apps/social-media-apps-scaling.png)

Mi történik, ha a dolgok jobbra fordulnak? Tegyük fel, hogy egy másik régióból, országból vagy kontinensről származó felhasználók észreveszik a platformot, és elkezdik használni. Micsoda meglepetés!

De várjunk csak! Hamarosan rájössz, hogy a platformoddal kapcsolatos tapasztalataik nem optimálisak. Olyan messze vannak a műveleti régiódtól, hogy a késés szörnyű. Nyilvánvalóan nem akarod, hogy kilépjenek. Bárcsak lenne egy egyszerű módja annak, **hogy kiterjesszék a globális elérni?** Ott van!

A Cosmos DB lehetővé teszi az adatok globális és átlátható [replikálását](../cosmos-db/tutorial-global-distribution-sql-api.md) néhány kattintással, és automatikusan kiválaszthatja a rendelkezésre álló régiókat az [ügyfélkódból.](../cosmos-db/tutorial-global-distribution-sql-api.md) Ez a folyamat azt is jelenti, hogy [több feladatátvételi régióval](high-availability.md)is rendelkezhet.

Amikor globálisan replikálja az adatokat, meg kell győződnie arról, hogy az ügyfelek ki tudják használni az adatokat. Ha webes előtér-szolgáltatást használ, vagy mobilügyfelek API-jait használja, üzembe helyezheti [az Azure Traffic Managert,](https://azure.microsoft.com/services/traffic-manager/) és klónozhatja az Azure App Service-t az összes kívánt régióban, teljesítménykonfiguráció használatával a kiterjesztett globális lefedettség támogatásához. Amikor az ügyfelek hozzáférnek az előtér-vagy API-k, azok a legközelebbi App Service, amely viszont csatlakozik a helyi Cosmos DB replika.

![Globális lefedettség hozzáadása a közösségi platformhoz](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Összegzés

Ez a cikk némi fényt derít a közösségi hálózatok teljes körű azure-beli, alacsony költségű szolgáltatásokkal való létrehozásának alternatíváira. a többrétegű tárolási megoldás és a "Létra" nevű adatterjesztés alkalmazásának ösztönzésével eredményeket hoz.

![Az Azure-szolgáltatások közötti interakció diagramja a közösségi hálózatokhoz](./media/social-media-apps/social-media-apps-azure-solution.png)

Az igazság az, hogy nincs ezüst golyó az ilyen helyzetekben. Ez a nagyszerű szolgáltatások kombinációja által létrehozott szinergia, amely lehetővé teszi számunkra, hogy nagyszerű élményeket hozzunk létre: az Azure Cosmos DB sebessége és szabadsága, hogy nagyszerű közösségi alkalmazást biztosítson, az intelligencia mögött egy első osztályú keresési megoldás, mint az Azure Cognitive Search, az Azure App Services rugalmassága, hogy még csak nem is nyelv-agnosztikus alkalmazásokat, hanem hatékony háttérfolyamatokat, valamint a bővíthető Azure Storage és Az Azure SQL Database-t is tároljon a nagy mennyiségű adat tárolásához és az Azure Machine Learning analitikus teljesítményéhez olyan tudást és intelligenciát hozhat létre, amely visszajelzést ad folyamataihoz, és segít abban, hogy a megfelelő tartalmat a megfelelő felhasználók számára juttassuk el.

## <a name="next-steps"></a>További lépések

A Cosmos DB használati eseteiről a [Gyakori Cosmos DB használati esetek et](use-cases.md)olvashat.
