---
title: Dokumentumadatok modellezése a NoSQL-adatbázis
titleSuffix: Azure Cosmos DB
description: Ismerje meg a NoSQL-adatbázisok az adatmodellezés, modellezése a relációs adatbázis és a dokumentum-adatbázis adatait közötti különbségeket.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: andrl
ms.custom: seodec18
ms.openlocfilehash: 5f117d51378f895755b4f5a27fe892d85e12074a
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762582"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben adatmodellezés

Bár a sémamentes adatbázisok, például az Azure Cosmos DB, hogy fantasztikusan egyszerű tárolására, és a strukturálatlan és részben strukturált adatok lekérdezése, az adatmodellben, hogy a legtöbbet a teljesítmény és méretezhetőség tekintetében a szolgáltatás és a legalacsonyabb kapcsolatos kell töltött némi idő terhelése költség.

Hogyan fogja tárolni az adatokat? Hogyan fogja lekérni, illetve adatokat kérdezhet le az alkalmazást? Az alkalmazását, olvassa el, vagy nagyrészt írási műveltekből?

Ez a cikk elolvasása után fogja tudni a következő kérdések megválaszolásával:

* Mi az adatmodellezés, és miért érdemes e fontos?
* Miben különbözik az Azure Cosmos DB modellezési adatok egy relációs adatbázis?
* Hogyan express a nem relációs adatbázisban adatkapcsolatok?
* Amikor ágyazhat be adatokat, és ha hivatkozás adatokhoz?

## <a name="embedding-data"></a>Adatok beágyazása

Az Azure Cosmos DB az adatok modellezését indításakor próbál az entitások gyökérkönyvtárral **önálló elemek** JSON-dokumentumok formájában jelöli.

Az összehasonlítást először lássuk, hogyan tudjuk előfordulhat, hogy modellezni az adatokat relációs adatbázis. Az alábbi példa bemutatja, hogyan személy előfordulhat, hogy tárolja a relációs adatbázis.

![Relációs adatbázis-modell](./media/sql-api-modeling-data/relational-data-model.png)

Relációs adatbázisok használata, ha a stratégia az, hogy normalizálása az összes adatot. Az adatok általában normalizálása egy entitás, például egy személy véve, és ossza fel diszkrét összetevőket foglalja magában. A fenti példában egy személy rendelkezhet több ügyfél részletes rekordok, valamint több cím rekord. Kapcsolattartási adatait is le kell bontani oly módon, további közös mezők, például egy típusa. Ugyanez vonatkozik a címet, minden egyes rekord típusúak lehetnek *kezdőlap* vagy *üzleti*.

A megtett helyi, amikor a normalizálás **elkerülése érdekében a redundáns adattárolás** az egyes rögzíti, és inkább az adatokra hivatkoznak. Ebben a példában egy személyt, a kapcsolattartási adatait és a címek, olvassa el a joins ZÁRADÉKOT használják ténylegesen compose vissza (vagy denormalizálja) az adatok futásidőben kell.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Írási műveletek között számos egyedi táblák frissítése egyetlen személy kapcsolattartási adatait és címek van szükség.

Most nézzük tekintse meg, hogyan tudjuk lenne modell ugyanazokat az adatokat az Azure Cosmos DB önálló entitásként.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

A megközelítéssel felett, hogy rendelkezik **denormalizált** a személy szerint rögzítése **beágyazás** be ezt a személyt, például a kapcsolattartási adatait és a címek, kapcsolatos összes információt egy *egyetlen JSON* dokumentumot.
Ezenkívül mivel azt már nem korlátozódik a rögzített sémát kell, hogy a rugalmasságot, például kapcsolattartási adatait a különböző alakzatokra teljes mértékben kellene.

Egy teljes személy rekord lekérése az adatbázis már egy **egyetlen olvasási művelete** egyetlen tároló szemben, és egyetlen cikkre. Frissíti egy személy rekordot, a kapcsolattartási adatait és a címek, egyben egy **írási művelet egyetlen** egyetlen elem ellen.

Denormalizálni az adatokat, amelyet az alkalmazás szükségessé kevesebb lekérdezések és frissítések gyakori műveletek végrehajtásához.

### <a name="when-to-embed"></a>Mikor érdemes beágyazása

Általában beágyazott adatokat használhat a modellek mikor:

* Nincsenek **tartalmazott** entitások közötti kapcsolatok.
* Nincsenek **egy néhány** entitások közötti kapcsolatok.
* Beágyazott adatok, amelyek **csak ritkán változnak**.
* Beágyazott adatok, amelyek nem fog **nélkül kötött**.
* Nincs a beágyazott adatok **gyakran együtt lekérdezett**.

> [!NOTE]
> Általában a konzisztenciamodellből jobban adatok denormalizált **olvasási** teljesítményét.

### <a name="when-not-to-embed"></a>Mikor nem beágyazása

Bár az Azure Cosmos DB a tapasztalatok denormalizálja mindent, és minden adat beágyazása egy elem, ez bizonyos helyzetekben, el kell kerülni vezethet.

A JSON-kódrészletben igénybe vehet.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Ez azért lehet, hogy mi a post entitás beágyazott megjegyzésekkel láthatóhoz hasonló azt modellezési lettek egy tipikus blog vagy tartalomkezelő rendszer, a rendszer. Ebben a példában a probléma merült fel, hogy a megjegyzések tömb **korlátlan streameken működő**, ami azt jelenti, hogy nincs-e bármilyen egyedi közzétételek rendelkezhet hozzászólások számát (gyakorlati) nincs korlátozva. Ez a növekedésével az elem méretének sikerült korlátlanul nagy probléma válhat.

Az elem méretének növekedésével képes továbbítani az adatokat a vezetékes, valamint olvasása és frissítése ipari méretekben, az elem felett érinti.

Lenne ebben az esetben jobb megoldás fontolja meg a következő data model.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Ebben a modellben van ágyazva a post tároló, amely egy tömböt attribútumok készletét a három legutóbbi megjegyzéseket. A többi megjegyzések 100 megjegyzések váró szerint csoportosítva, és a tárolt és különálló elemek. A Köteg mérete lett kiválasztva a 100, mert a fiktív alkalmazás lehetővé teszi, hogy a felhasználó egyszerre 100 megjegyzések betöltése.  

Egy másik esetben, amikor beágyazási adatok nem célszerű akkor, ha a beágyazott adatok elemek között gyakran használják, és gyakran változik.

A JSON-kódrészletben igénybe vehet.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Ez egy személy tőzsdei portfólió utalhatnak. Azt választotta, a tőzsdei információkat egyes portfólióban dokumentu ágyazhat be. Olyan környezetben, ahol kapcsolódó adatok gyakran változnak például egy készletből, kereskedelmi alkalmazás, az adatok gyakran változnak beágyazás fogja azt jelenti, hogy folyamatosan frissítjük az egyes portfólió dokumentumok minden alkalommal, amikor egy készlet forog.

Készlet *zaza* egyetlen alkalommal több száz kerülhetnek nap és a felhasználók ezreit lehet *zaza* a saját portfólió. A fenti portfólió dokumentumok a sok ezer sokszor frissíteni szeretné van például egy adatmodellel minden nap, és a rendszer, amely nem jól méretezhető.

## <a name="referencing-data"></a>Hivatkozó adatok

Így sok esetben szépen adatok beágyazása működik, de egyértelmű, hogy vannak helyzetek, amikor denormalizálni az adatokat, érdemes további problémákat okozhat. Tehát Mi a teendő most?

A relációs adatbázisok nem állnak az egyetlen hely, ahol az entitások közötti kapcsolatokat hozhat létre. A dokumentum-adatbázis, az információt, hogy ténylegesen vonatkozik, adatok, az egyéb dokumentumokat egy dokumentum rendelkezhet. Most szeretnék vagyok nem javasolni akár egy percet, hogy készítünk rendszereket, akkor lehet jobban megfelel az Azure Cosmos DB a relációs vagy egyéb dokumentum-adatbázist, de egyszerű kapcsolatok rendben, és hasznos lehet.

A JSON az alábbi a példában a tőzsdei portfólió a korábban használandó választottuk, de ezúttal nevezzük a portfólió helyett beágyazza a tőzsdei elemet. Ez esetben, amikor a tőzsdei elem gyakran megváltoznak egy nap csak dokumentumot frissíteni kell a készlet egyetlen dokumentum.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

Egy azonnali ennek hátránya, ez a megközelítés azonban, ha az alkalmazás szükség minden egyes készleten egy személy portfólió; megjelenítésekor tárolt adatainak megjelenítése Ebben az esetben kell, hogy több lelassítja betölti az információt az egyes tőzsdei dokumentumok az adatbázishoz. Itt hajtottunk végre az írási műveletek, amelyek gyakran a nap folyamán fordulhat elő, de viszont biztonsága sérült, az olvasási műveletek, amelyek vélhetően kisebb hatással vannak az adott rendszer teljesítményét a hatékonyság növelése döntés.

> [!NOTE]
> Adatmodellek normalized **megkövetelheti további adatváltások** a kiszolgálóhoz.

### <a name="what-about-foreign-keys"></a>Mi a helyzet a külső kulcsokat?

És jelenleg nincs megkötés, mert külső kulcsok vagy egyéb, dokumentumok rendelkező dokumentum közötti kapcsolatok lényegében "gyenge hivatkozások", és maga az adatbázis nem ellenőrzi. Ha azt szeretné, annak érdekében, hogy az adatok egy dokumentum hivatkozik valóban létezik, majd meg kell ezt az alkalmazásban, vagy kiszolgálóoldali eseményindítók és tárolt eljárások az Azure Cosmos DB használatával.

### <a name="when-to-reference"></a>Mikor érdemes hivatkozik

Általában a normalizált adatokat használni a modellek mikor:

* Jelölő **egy-a-többhöz** kapcsolatokat.
* Jelölő **több-a-többhöz** kapcsolatokat.
* A kapcsolódó adatok **gyakran változik**.
* Hivatkozásban szereplő lehet **korlátlan streameken működő**.

> [!NOTE]
> Jobban általában normalizálása biztosít **írási** teljesítményét.

### <a name="where-do-i-put-the-relationship"></a>Hol helyezhetem el a kapcsolatot?

A kapcsolat a növekedési segítségével megállapíthatja, hogy mely dokumentum tárolásához a hivatkozást.

Ha megnézzük az alábbi JSON-t a modellek közzétevők és könyveket.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Ha a közzétevő / könyvek száma korlátozott növekedést kicsi, majd tárolja a könyv hivatkozás a közzétevő dokumentumon belüli akkor lehet hasznos. Azonban ha a közzétevő / könyvek száma korlátlan streameken működő, majd az adatmodell vezetne mutable, egyre növekvő tömbök, mint a fenti példa közzétevő dokumentumot.

Váltás körül egy dolog modell, amely ugyanazokat az adatokat továbbra is jelöli, de most elkerülhetők a nagy mutable gyűjteményekbe eredményez.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

A fenti példában a korlátlan streameken működő gyűjtemény áthúzott azt a közzétevő dokumentumot. Ehelyett csak rá egy hivatkozást a közzétevő minden könyv dokumentum.

### <a name="how-do-i-model-manymany-relationships"></a>Hogyan minta a több: többhöz kapcsolatokat?

A relációs adatbázis *több: több* kapcsolatok gyakran modellezése eltér az illesztési táblákkal, amely csak csatlakozzon együtt más táblákból származó rekordokat.

![Táblák](./media/sql-api-modeling-data/join-table.png)

Előfordulhat, hogy ugyanarra a dologra dokumentumok használatával replikálja, és előállít egy olyan modell, amely a következőhöz hasonló gyermekeiről.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Ez akkor működik. Azonban vagy a szerző, a saját könyvek betöltése, vagy egy könyv betöltése a szerző, a mindig kellene legalább két további lekérdezések az adatbázison. Egy lekérdezést a csatlakozó dokumentumot és a egy másik lekérdezés csatlakoztatott folyamatban van a tényleges dokumentum beolvasása.

Ha az illesztési tábla csak akkor van kapcsolással együtt adatok kétféle információra majd miért nem dobható el, teljesen?
Vegye figyelembe a következőket.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Most ha egy szerző voltam, azonnal tudom, melyik könyvek írásos engedélye szükséges, és ezzel szemben ha egy könyv betöltött dokumentum le kellett volna tudom azonosítóját a szerző(k) az. Ez menti a köztes-lekérdezést az illesztési tábla csökkenti az kiszolgálók száma kerekíteni lelassítja győződjön meg arról, hogy rendelkezik az alkalmazás.

## <a name="hybrid-data-models"></a>Hibrid adatmodellek

Most már áttekintettük beágyazás (vagy denormalizálni) és hivatkozó (vagy normalizálása) adatokat, azok upsides rendelkező, és úgy találtuk, eseménnyel rendelkező.

Nem lehet minden esetben kell vagy Ijedt összekeveri dolgot egy kicsit, nem lehet.

Az alkalmazás által meghatározott használati minták és számítási feladatok, előfordulhat, hogy hol keverése beágyazott alapján és a hivatkozott adatok és a érdeklődő egyszerűbb alkalmazáslogika kevesebb kiszolgálóval való kerekítéséhez lelassítja miközben továbbra is fenntartja az a jó teljesítmény szintű.

Vegye figyelembe a következő JSON-fájllal.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

(Az alkalmazásaimat általában) Itt azt idáig követte a beágyazott modellben, ahol más entitások adatainak be van ágyazva a legfelső szintű dokumentumot, de egyéb adatok hivatkozik.

Ha megtekinti a könyv dokumentumot, láthatjuk néhány érdekes mezők, ha megnézzük a szerzők a tömb. Van egy `id` mezőben, amely a mező használatával vissza tekintse át egy szerző, a szabványos eljárás normalizált modell, a dokumentumot, de majd van `name` és `thumbnailUrl`. Azt a sikerült elakadt `id` , és az alkalmazás számára szükséges további adatokat kérhet a megfelelő szerzői dokumentumot a "link", de mivel az alkalmazás megjeleníti a szerző nevét és minden könyv a miniatűr kép jelenleg is menteni körbeérnek a könyv-lista kiszolgálónként denormalizálni által megjelenített **néhány** a szerző adatait.

Arról Ha a szerző neve megváltozott, vagy frissíteni a fénykép szűrnek lenne van, és minden eddiginél közzé, de az alkalmazáshoz, feltételezve, hogy szerzők nem gyakran változnak nevük alapján minden könyv frissítése, ez egy elfogadható tervezési döntés.  

A példában a **összesítések előre kiszámított** értékek feldolgozási költséges menteni a egy olvasási művelet. A példában a szerző dokumentum beágyazott adatok némelyike a futásidőben számított adatokat. Jön létre minden alkalommal, amikor egy új könyv közzé van téve, egy könyv dokumentum **és** countOfBooks mező egy könyv azt jelzi, hogy létezik egy adott szerző száma alapján számított értékre van állítva. Az optimalizálás jó lenne a olvasási (nagy erőforrásigényű) rendszerek, hogy megengedhet számítások végrehajtandó írások olvasási optimalizálása érdekében.

Modell előre számított mezők vannak lehetséges legyen, mivel az Azure Cosmos DB támogatja **többdokumentumos tranzakciókat**. NoSQL-tárolókat nem tranzakciók tegye a dokumentumok között, és ezért tanácsadójának tervezési döntéseket, például a "mindig beágyazásához mindent" Ez a korlátozás miatt. Az Azure Cosmos DB használhatja a kiszolgálóoldali eseményindítók és tárolt eljárások, amelyek könyvek beszúrási és frissítési szerzők minden ACID tranzakción belül. Most nem **rendelkezik** ágyazhat be mindent az egy dokumentum csak arról, hogy az adatok konzisztensek maradnak.

## <a name="distinguishing-between-different-document-types"></a>Különböző típusok megkülönböztetése

Bizonyos esetekben érdemes lehet értékek nagyságrendjeit különböző dokumentumtípusokhoz ugyanabban a gyűjteményben; Ez általában akkor fordul, ha több, azonos szeretném dokumentumok kapcsolódó [partíció](partitioning-overview.md). Például, sikerült helyezze mindkét könyvek és repülőjáratra felülvizsgálatok ugyanabban a gyűjteményben, és ezt a particionálása `bookId`. Ilyen esetben általában érdemes egy mezővel, amely azonosítja a típusát annak érdekében, hogy megkülönböztessük őket a dokumentumokhoz való hozzáadásához.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>További lépések

Ez a cikk az a legnagyobb takeaways is tisztában lenni azzal, hogy a séma nélküli világában adatmodellezési olyan fontos, mint valaha.

Nincs egyetlen lehetőség, amely jelöli az adatok képernyőn, ahogy nincs modellezheti az adatokat az egyetlen lehetőség. Meg kell megtudhatja, hogy az alkalmazás, és hogyan azt eredményez, felhasználását, és fel az adatokat. Ezután az itt bemutatott irányelveket némelyike alkalmazásával állíthatja be egy modellt, amely az alkalmazás azonnali igényeit létrehozásáról. Ha módosítani kell az alkalmazások, kihasználhatja egy séma nélküli adatbázis kihasználni, amely módosíthatja, és fejlesztheti tovább könnyedén az adatmodellben rugalmasságát.

Azure Cosmos DB kapcsolatos további információkért tekintse meg a szolgáltatás [dokumentáció](https://azure.microsoft.com/documentation/services/cosmos-db/) lapot.

Megismerheti, hogyan szegmensre az adatok több partíción, tekintse meg a [az Azure Cosmos DB particionálási adatok](sql-api-partition-data.md).
