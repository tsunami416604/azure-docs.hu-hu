---
title: Az adatmodellezés Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Ismerje meg a NoSQL-adatbázisok az adatmodellezés, modellezése a relációs adatbázis és a dokumentum-adatbázis adatait közötti különbségeket.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: da119b2858c6b6c7bbc99b40d340f79964e0fae3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467894"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Adatmodellezés Azure Cosmos DB

A séma nélküli adatbázisok (például a Azure Cosmos DB) rendkívül egyszerűvé teszik a strukturálatlan és részben strukturált adatmennyiségek tárolását és lekérdezését, némi időt kell fordítania az adatmodellre, hogy a lehető legtöbbet használja a szolgáltatás teljesítményének és méretezhetőségének, valamint a legalacsonyabb költség.

Hogyan fogja tárolni az adatokat? Hogyan fogja lekérni, illetve adatokat kérdezhet le az alkalmazást? Az alkalmazás olvasása nagy vagy írható-e?

Ez a cikk elolvasása után fogja tudni a következő kérdések megválaszolásával:

* Mi az adatmodellezés, és miért érdemes e fontos?
* Miben különböznek a modellezési adatAzure Cosmos DBek a kapcsolódó adatbázisokban?
* Hogyan express a nem relációs adatbázisban adatkapcsolatok?
* Amikor ágyazhat be adatokat, és ha hivatkozás adatokhoz?

## <a name="embedding-data"></a>Adatok beágyazása

Amikor megkezdi az adatok modellezését Azure Cosmos DB próbálja meg kezelni az entitásokat JSON-dokumentumként ábrázolt **önálló elemekként** .

Az összehasonlításhoz először lássuk, hogyan lehet egy relációs adatbázisban modellezni az adatmodellt. Az alábbi példa bemutatja, hogyan személy előfordulhat, hogy tárolja a relációs adatbázis.

![Relációs adatbázis-modell](./media/sql-api-modeling-data/relational-data-model.png)

A kapcsolati adatbázisok használatakor a stratégia az összes adathalmaz normalizálása. Az adatnormalizálás általában magában foglalja egy entitás, például egy személy bevonását és a különálló összetevőkbe való lebontását. A fenti példában egy személy több kapcsolattartási adattal és több rekorddal is rendelkezhet. A kapcsolattartási adatok tovább bonthatók a gyakori mezők (például egy típus) további kibontásával. Ugyanez vonatkozik a címekre, az egyes rekordok lehetnek *otthoni* vagy *üzleti*típusúak.

A megtett helyi, amikor a normalizálás **elkerülése érdekében a redundáns adattárolás** az egyes rögzíti, és inkább az adatokra hivatkoznak. Ebben a példában egy személy olvasásához, az összes kapcsolattartási adataival és címével együtt kell használnia az adatokat az adatoknak a futási időben való visszaírásához (vagy denormalizálása).

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Írási műveletek között számos egyedi táblák frissítése egyetlen személy kapcsolattartási adatait és címek van szükség.

Most vessünk egy pillantást arra, hogyan modellezjük ugyanazokat az adategységeket Azure Cosmos DBban.

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

A fenti módszer használatával a személy  rekordját denormalizáljuk a személyhez kapcsolódó összes információ, például a kapcsolattartási adatok és a címek *egyetlen JSON* -dokumentumba való **beágyazásával** .
Ezenkívül mivel azt már nem korlátozódik a rögzített sémát kell, hogy a rugalmasságot, például kapcsolattartási adatait a különböző alakzatokra teljes mértékben kellene.

Egy teljes person rekordnak az adatbázisból való beolvasása már **egyetlen olvasási művelet** egyetlen tárolón és egyetlen elemnél. Egy személy rekordjának frissítése, a kapcsolattartási adatokkal és a címekkel együtt egyetlen, egyetlen elemre vonatkozó **írási művelet** is.

Denormalizálni az adatokat, amelyet az alkalmazás szükségessé kevesebb lekérdezések és frissítések gyakori műveletek végrehajtásához.

### <a name="when-to-embed"></a>Mikor érdemes beágyazása

Általában beágyazott adatokat használhat a modellek mikor:

* **Az** entitások között találhatók kapcsolatok.
* Nincsenek **egy néhány** entitások közötti kapcsolatok.
* Beágyazott adatok, amelyek **csak ritkán változnak**.
* Olyan beágyazott adat is van, amely nem növekszik **kötés nélkül**.
* Vannak olyan beágyazott adathalmazok, amelyek **gyakran**vannak lekérdezve.

> [!NOTE]
> Általában a konzisztenciamodellből jobban adatok denormalizált **olvasási** teljesítményét.

### <a name="when-not-to-embed"></a>Mikor nem beágyazása

Habár a Azure Cosmos DBi szabálya az, hogy denormalizálja a mindent, és egyetlen elembe ágyazza be az összes adathalmazt, ez bizonyos helyzetek elkerülését eredményezheti.

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

Ez azért lehet, hogy mi a post entitás beágyazott megjegyzésekkel láthatóhoz hasonló azt modellezési lettek egy tipikus blog vagy tartalomkezelő rendszer, a rendszer. Ebben a példában a probléma merült fel, hogy a megjegyzések tömb **korlátlan streameken működő**, ami azt jelenti, hogy nincs-e bármilyen egyedi közzétételek rendelkezhet hozzászólások számát (gyakorlati) nincs korlátozva. Ez problémát jelenthet, mivel az elemek mérete végtelenül nagy növekedést eredményezhet.

Ahogy az elem mérete növekszik, az adatokat a vezetékes hálózaton keresztül továbbíthatja, valamint az elem olvasását és frissítését is befolyásolja.

Ebben az esetben jobb lenne a következő adatmodellt figyelembe venni.

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

Ez a modell a post tárolóban beágyazott három legújabb megjegyzést tartalmaz, amely az attribútumok rögzített készletét tartalmazó tömb. A többi Megjegyzés a 100-es hozzászólások kötegei között van csoportosítva, és külön elemként van tárolva. A Köteg mérete lett kiválasztva a 100, mert a fiktív alkalmazás lehetővé teszi, hogy a felhasználó egyszerre 100 megjegyzések betöltése.  

Egy másik eset, amikor az adatok beágyazása nem jó ötlet, ha a beágyazott adatokat gyakran használják az elemek között, és gyakran változnak.

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

Ez egy személy tőzsdei portfólió utalhatnak. Úgy döntöttünk, hogy az összes portfólió-dokumentumba ágyazza be a tőzsdei adatokat. Olyan környezetben, ahol kapcsolódó adatok gyakran változnak például egy készletből, kereskedelmi alkalmazás, az adatok gyakran változnak beágyazás fogja azt jelenti, hogy folyamatosan frissítjük az egyes portfólió dokumentumok minden alkalommal, amikor egy készlet forog.

Készlet *zaza* egyetlen alkalommal több száz kerülhetnek nap és a felhasználók ezreit lehet *zaza* a saját portfólió. A fenti portfólió dokumentumok a sok ezer sokszor frissíteni szeretné van például egy adatmodellel minden nap, és a rendszer, amely nem jól méretezhető.

## <a name="referencing-data"></a>Hivatkozó adatértékek

Az adatok beágyazásával szépen működik sok esetben, de az adatok denormalizálása során több problémát is okozhat, mint amennyit érdemes. Tehát Mi a teendő most?

A relációs adatbázisok nem állnak az egyetlen hely, ahol az entitások közötti kapcsolatokat hozhat létre. A dokumentum-adatbázisokban egy olyan dokumentumban található információ, amely más dokumentumokban lévő adatokra vonatkozik. Nem javasoljuk olyan rendszerek kialakítását, amelyek jobban illeszkednek a Azure Cosmos DB vagy bármely más dokumentum-adatbázis relációs adatbázisához, de az egyszerű kapcsolatok is hasznosak lehetnek.

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

Ha már megismertem a szerzőt, azonnal tudni szeretném, hogy mely könyveket írtak, és fordítva, ha betöltöttem a könyv dokumentumát, a szerző (k) azonosítóit szeretném megismerni. Ez menti a köztes-lekérdezést az illesztési tábla csökkenti az kiszolgálók száma kerekíteni lelassítja győződjön meg arról, hogy rendelkezik az alkalmazás.

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

Ha megtekinti a könyv dokumentumot, láthatjuk néhány érdekes mezők, ha megnézzük a szerzők a tömb. Létezik egy olyan `id` mező, amely az a mező, amellyel visszahivatkozhatunk egy szerzői dokumentumra, egy normalizált modell standard gyakorlatára, de a és `name` `thumbnailUrl`a is. Megakadt `id` az alkalmazásba, és otthagyta az alkalmazást, hogy a megfelelő szerzői dokumentumhoz szükséges további információkat kapjon a "link" használatával, de mivel az alkalmazás a szerző nevét és egy miniatűr képét jeleníti meg minden könyvvel megjelenítjük, hogy a szerzőtől **származó adatok** denormalizálása révén egy oda-vissza is menthetők a-kiszolgálóra.

Győződjön meg arról, hogy ha a szerző neve megváltozott, vagy frissíteni szeretné a fényképét, akkor azt a feltételezés alapján, hogy a szerzők gyakran nem változtatják meg a nevüket, ez egy elfogadható tervezési döntés.  

A példában a **összesítések előre kiszámított** értékek feldolgozási költséges menteni a egy olvasási művelet. A példában a szerző dokumentum beágyazott adatok némelyike a futásidőben számított adatokat. Jön létre minden alkalommal, amikor egy új könyv közzé van téve, egy könyv dokumentum **és** countOfBooks mező egy könyv azt jelzi, hogy létezik egy adott szerző száma alapján számított értékre van állítva. Az optimalizálás jó lenne a olvasási (nagy erőforrásigényű) rendszerek, hogy megengedhet számítások végrehajtandó írások olvasási optimalizálása érdekében.

Modell előre számított mezők vannak lehetséges legyen, mivel az Azure Cosmos DB támogatja **többdokumentumos tranzakciókat**. NoSQL-tárolókat nem tranzakciók tegye a dokumentumok között, és ezért tanácsadójának tervezési döntéseket, például a "mindig beágyazásához mindent" Ez a korlátozás miatt. Az Azure Cosmos DB használhatja a kiszolgálóoldali eseményindítók és tárolt eljárások, amelyek könyvek beszúrási és frissítési szerzők minden ACID tranzakción belül. Most nem **kell** semmit beágyaznia egyetlen dokumentumba, hogy az adatai konzisztensek maradnak.

## <a name="distinguishing-between-different-document-types"></a>Különböző dokumentumtípusok megkülönböztetése

Bizonyos esetekben előfordulhat, hogy különböző dokumentumtípust szeretne összekeverni ugyanabban a gyűjteményben; Ez általában akkor történik, ha több, kapcsolódó dokumentumot is szeretne ugyanazon a partíción ülni [](partitioning-overview.md). Tegyük fel például, hogy a könyvek és a könyv-felülvizsgálatok ugyanabban a gyűjteményben vannak `bookId`, és particionálja a következővel:. Ilyen esetben általában fel kell vennie a dokumentumaiba egy olyan mezővel, amely azonosítja a típusát, hogy megkülönböztesse őket.

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

Ha szeretné megtudni, hogyan modellezheti és particionálhatja Azure Cosmos DB a valós példa használatával, tekintse meg az adatmodellezést [és particionálást – egy valós példát](how-to-model-partition-example.md).
