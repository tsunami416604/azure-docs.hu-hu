---
title: Adatok modellezése az Azure Cosmos DB-ben
titleSuffix: Azure Cosmos DB
description: Információ a NoSQL-adatbázisok adatmodellezéséről, a relációs adatbázisban és a dokumentum-adatbázisban lévő adatok modellezése közötti különbségekről.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755017"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Adatmodellezés az Azure Cosmos DB-ben

Bár a sémamentes adatbázisok, például az Azure Cosmos DB, rendkívül egyszerűvé teszik a strukturálatlan és félig strukturált adatok tárolását és lekérdezését, érdemes egy kis időt töltenie az adatmodellen, hogy a lehető legtöbbet hozhassa ki a szolgáltatásteljesítmény, méretezhetőség és a legalacsonyabb Költség.

Hogyan fogják tárolni az adatokat? Hogyan fog az alkalmazás adatokat letölteni és lekérdezni? Az alkalmazás olvasási nehéz, vagy írás-nehéz?

A cikk elolvasása után a következő kérdésekre válaszolhat:

* Mi az adatmodellezés, és miért érdekel?
* Miben különböznek az Azure Cosmos DB-ben az adatok modellezése a relációs adatbázistól?
* Hogyan fejezhetem ki az adatkapcsolatokat egy nem relációs adatbázisban?
* Mikor ágyazhatok be adatokat, és mikor tudok adatokra hivatkozni?

## <a name="embedding-data"></a>Adatok beágyazása

Amikor elkezdi az adatok modellezését az Azure Cosmos **DB-ben,** próbálja meg az entitásokat önálló elemekként kezelni, amelyek JSON-dokumentumokként jelennek meg.

Összehasonlításképpen először nézzük meg, hogyan modellezhetjük az adatokat egy relációs adatbázisban. A következő példa bemutatja, hogyan tárolható egy személy relációs adatbázisban.

![Relációs adatbázis modell](./media/sql-api-modeling-data/relational-data-model.png)

A relációs adatbázisok kezelése során a stratégia az összes adat normalizálása. Az adatok normalizálása általában magában foglalja egy entitás, például egy személy, és bontsa le különálló összetevőkre. A fenti példában egy személy nek több kapcsolattartó-adatrekordja, valamint több címbejegyzése is lehet. Az elérhetőségi adatok tovább bonthatók a közös mezők, például egy típus további kinyerésével. Ugyanez vonatkozik a címre, minden bejegyzés lehet *Home* vagy *Business*típusú .

Az adatok normalizálása kor az irányadó feltevés, hogy **ne tároljon redundáns adatokat** az egyes rekordokon, és inkább hivatkozzon az adatokra. Ebben a példában, olvasni egy személy, az összes kapcsolattartási adatait és címét, meg kell használni JOINS hatékonyan össze vissza (vagy denormalize) az adatok at futásidőben.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Egyetlen személy frissítése a kapcsolattartási adataikkal és címükkel írási műveleteket igényel számos egyedi táblában.

Most vessünk egy pillantást, hogyan modellezné ugyanazokat az adatokat, mint egy önálló entitás az Azure Cosmos DB.Now let's take a look at how we would model the same data as a self-self entity in Azure Cosmos DB.

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

A fenti megközelítés sel **normalizáltuk** a személy rekordot, **beágyazva** az összes ezzel a személlyel kapcsolatos információt, például elérhetőségi adataikat és címüket egyetlen *JSON* dokumentumba.
Ezenkívül, mivel nem korlátozódunk egy rögzített sémára, rugalmasan eltudjuk végezni az olyan műveleteket, mint a különböző alakzatok elérhetőségi adatai.

Egy teljes személyrekord beolvasása az adatbázisból **mostantól egyetlen olvasási művelet** egyetlen tároló és egyetlen elem között. Egy személybejegyzés frissítése a kapcsolattartási adataikkal és címükkel egyetlen **írási művelet** egyetlen elemhez képest.

Az adatok denormalizálásával előfordulhat, hogy az alkalmazásnak kevesebb lekérdezést és frissítést kell kiadnia a gyakori műveletek végrehajtásához.

### <a name="when-to-embed"></a>Mikor kell beágyazni?

Általában akkor használjon beágyazott adatmodelleket, ha:

* Entitások között **tartalmazott** kapcsolatok vannak.
* **Egy-a-kevés** kapcsolat van az entitások között.
* Vannak olyan beágyazott adatok, amelyek **ritkán változnak.**
* Vannak olyan beágyazott adatok, amelyek nem növekednek **kötött .**
* Vannak beágyazott adatok, amelyek **gyakran együtt kérdeznek.**

> [!NOTE]
> Általában denormalizált adatmodellek jobb **olvasási** teljesítményt biztosítanak.

### <a name="when-not-to-embed"></a>Mikor nem ágyaz

Míg az Azure Cosmos DB ökölszabálya az, hogy denormalizálja a mindent, és az összes adatot egyetlen elembe ágyazza be, ez bizonyos helyzetekhez vezethet, amelyeket el kell kerülni.

Fogd ezt a JSON részletet.

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

Ez lehet, amit egy post entitás beágyazott megjegyzéseket nézne ki, ha mi volt modellezés egy tipikus blog, vagy CMS, rendszer. A probléma ezzel a példával az, hogy a hozzászólások tömb **határtalan**, ami azt jelenti, hogy nincs (praktikus) korlátozza a hozzászólások száma minden egyes post lehet. Ez problémát okozhat, mivel az elem mérete végtelenül megnőhet.

Mivel a méret a tétel nő a képességét, hogy továbbítja az adatokat a vezetéken keresztül, valamint az olvasás és frissítése az elem, a skála, hatással lesz.

Ebben az esetben jobb lenne, ha figyelembe veszi a következő adatmodell.

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

Ez a modell rendelkezik a bejegyzéstárolóba ágyazott három legutóbbi megjegyzéssel, amely egy rögzített attribútumkészlettel rendelkező tömb. A többi megjegyzés 100 megjegyzésből álló kötegekbe van csoportosítva, és külön cikkként tárolódik. A tétel méretét 100-nak választották, mert fiktív alkalmazásunk lehetővé teszi a felhasználó számára, hogy egyszerre 100 megjegyzést töltsön be.  

Egy másik eset, amikor az adatok beágyazása nem jó ötlet, amikor a beágyazott adatokat gyakran használják az elemek között, és gyakran változnak.

Fogd ezt a JSON részletet.

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

Ez képviselheti egy személy részvényportfólióját. Úgy döntöttünk, hogy a készletadatokat minden portfóliódokumentumba beágyazza. Egy olyan környezetben, ahol a kapcsolódó adatok gyakran változnak, mint például egy tőzsdei kereskedési alkalmazás, a gyakran változó adatok beágyazása azt jelenti, hogy minden egyes portfóliódokumentumot folyamatosan frissít minden alkalommal, amikor egy részvényt kereskednek.

Stock *zaza* lehet kereskedni több száz alkalommal egy nap, és több ezer felhasználó volna *zaza* a portfolió. Egy olyan adatmodellel, mint a fenti, naponta többször több ezer portfóliódokumentumot kell frissítenünk, ami egy olyan rendszerhez vezet, amely nem méretezhető jól.

## <a name="referencing-data"></a>Hivatkozás adatokra

Az adatok beágyazása sok esetben jól működik, de vannak olyan esetek, amikor az adatok denormalizálása több problémát okoz, mint amennyit ér. Akkor most mit csináljunk?

A relációs adatbázisok nem az egyetlen helyek, ahol entitások közötti kapcsolatokat hozhat létre. A dokumentum-adatbázisokban az egyik dokumentumban más dokumentumok adataira vonatkozó információk is szerepelhetnek. Nem javasoljuk, hogy olyan rendszereket építsen, amelyek jobban megfelelnek az Azure Cosmos DB vagy bármely más dokumentum-adatbázis relációs adatbázisának, de az egyszerű kapcsolatok rendben vannak, és hasznosak lehetnek.

Az alábbi JSON-ban úgy döntöttünk, hogy egy korábbi részvényportfólió példáját használjuk, de ezúttal a portfólió részvénytételére hivatkozunk, ahelyett, hogy beágyaznánk. Így, ha a készletcikk gyakran változik a nap folyamán, az egyetlen frissítendő bizonylat az egyetlen készletbizonylat.

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

Egy azonnali hátránya, hogy ez a megközelítés azonban az, ha az alkalmazás szükséges, hogy információkat jelenítsen meg az egyes állomány, amely birtokában van, amikor megjeleníti a személy portfólió; ebben az esetben több utat kell tennie az adatbázisba az egyes készletbizonylatok adatainak betöltéséhez. Itt úgy döntöttünk, hogy javítjuk az írási műveletek hatékonyságát, amelyek gyakran történnek a nap folyamán, de viszont veszélyeztették az olvasási műveleteket, amelyek potenciálisan kisebb hatást gyakorolnak az adott rendszer teljesítményére.

> [!NOTE]
> A normalizált adatmodellek **több adatváltást igényelhetnek** a kiszolgálóra.

### <a name="what-about-foreign-keys"></a>Mi van a külföldi kulcsokkal?

Mivel jelenleg nincs fogalom a megkötés, az idegen kulcs vagy más, a dokumentumok közötti kapcsolatok, hogy van a dokumentumok ban hatékonyan "gyenge láncszemek", és nem ellenőrzi az adatbázis maga. Ha azt szeretné, hogy a dokumentum által átadott adatok valóban léteznek, akkor ezt az alkalmazásban, vagy az Azure Cosmos DB kiszolgálóoldali eseményindítók vagy tárolt eljárások használatával kell megtennie.

### <a name="when-to-reference"></a>Mikor rakják fel a hivatkozást?

Általában akkor használjon normalizált adatmodelleket, ha:

* **Egy-a-többhöz** kapcsolatokat képvisel.
* **Több-a-többhöz** kapcsolatokat képvisel.
* A kapcsolódó adatok **gyakran változnak**.
* A hivatkozott adatok **nem lehetnek korlátozás nélkül.**

> [!NOTE]
> A normalizálás általában jobb **írási** teljesítményt biztosít.

### <a name="where-do-i-put-the-relationship"></a>Hova tegyem a kapcsolatot?

A kapcsolat növekedése segít meghatározni, hogy melyik dokumentumban tárolja a hivatkozást.

Ha megnézzük a JSON alatt, hogy a modellek kiadók és könyvek.

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

Ha a kiadónkénti könyvek száma kicsi, korlátozott növekedéssel, akkor hasznos lehet a könyvhivatkozás nak a közzétevői dokumentumban való tárolása. Ha azonban a kiadónkénti könyvek száma nincs határtalan, akkor ez az adatmodell képzhetővé tévő, növekvő tömbökhöz vezetne, mint a fenti példamegjelenítői dokumentumban.

A dolgok egy kicsit történő váltása azt eredményezné, hogy egy modell továbbra is ugyanazokat az adatokat képviseli, de most elkerüli ezeket a nagy módosítható gyűjteményeket.

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

A fenti példában elejtettük a nem kötött gyűjteményt a közzétevői dokumentumon. Ehelyett már csak egy hivatkozást a kiadó minden könyv dokumentum.

### <a name="how-do-i-model-manymany-relationships"></a>Hogyan modellezhetek sok:sok kapcsolatot?

A relációs adatbázisokban *sok:sok* kapcsolatot gyakran illesztési táblákkal modelleznek, amelyek csak más táblák rekordjait egyesítik.

![Táblák illesztése](./media/sql-api-modeling-data/join-table.png)

Előfordulhat, hogy a kísértés, hogy replikálja ugyanazt a dokumentumot, és készítsen egy adatmodell, amely úgy néz ki, mint a következő.

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

Ez működne. Azonban a szerző betöltése a könyveikkel, vagy egy könyv betöltése a szerzővel, mindig legalább két további lekérdezést igényelne az adatbázissal kapcsolatban. Egy lekérdezés az illesztési dokumentumhoz, majd egy másik lekérdezés az egyesített dokumentum beolvasásához.

Ha mindez összeilleszteni táblázat csinál ás van ragasztás együtt két darab adat, akkor miért nem csepp ez teljesen?
Vegye figyelembe a következőket.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Most, ha volt egy szerző, azonnal tudom, hogy mely könyveket írtak, és fordítva, ha volt egy könyv dokumentum betöltve tudnám az azonosítók a szerző (ek). Ezzel menti a köztes lekérdezést az illesztőtáblához, csökkentve az alkalmazás által eltelegbe tett kiszolgálói adatváltások számát.

## <a name="hybrid-data-models"></a>Hibrid adatmodellek

Most már nézett beágyazása (vagy denormalizing) és hivatkozva (vagy normalizálása) adatok, mindegyiknek megvan a maga előnye, és mindegyik kompromisszumok, mint láttuk.

Nem kell mindig, vagy, ne félj, hogy keverjük össze a dolgokat egy kicsit.

Az alkalmazás konkrét használati minták és számítási feladatok alapján előfordulhatnak olyan esetek, amikor a beágyazott és a hivatkozott adatok keverése van értelme, és egyszerűbb alkalmazáslogikához vezethet kevesebb kiszolgálói körkörös utazással, miközben továbbra is jó teljesítményt biztosít.

Tekintsük a következő JSON.

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

Itt (többnyire) követtük a beágyazott modellt, ahol más entitásokból származó adatok vannak beágyazva a legfelső szintű dokumentumba, de más adatokra hivatkozunk.

Ha megnézzük a könyv dokumentum, láthatjuk néhány érdekes területen, ha megnézzük a tömb a szerzők. Van egy `id` mező, amely az általunk használt utalvissza a szerző dokumentum, a szokásos gyakorlat `name` `thumbnailUrl`egy normalizált modell, de akkor is van, és . Mi lehetett volna `id` megragadt, és elhagyta az alkalmazást, hogy minden további szükséges információt a megfelelő szerző dokumentum segítségével a "link", hanem azért, mert a mi alkalmazás megjeleníti a szerző nevét és egy miniatűr képet minden könyv jelenik meg tudjuk menteni egy oda-vissza a szerver egy könyvet egy listát denormalizing **néhány** adatot a szerző.

Persze, ha a szerző neve megváltozott, vagy azt akarták, hogy frissítse a fotó mi volna menni, és frissítse az összes könyvet, amit valaha is megjelent, de a mi alkalmazás, azon a feltételezésen alapul, hogy a szerzők nem változnak a nevüket gyakran, ez egy elfogadható tervezési döntés.  

A példában vannak **előre kiszámított összesítések** értékek et menteni költséges feldolgozás egy olvasási művelet. A példában a szerzői dokumentumba ágyazott adatok egy része futásidőben számított adat. Minden alkalommal, amikor egy új könyv jelenik meg, egy könyv dokumentum jön létre, **és** a countOfBooks mező van beállítva, hogy a számított érték alapján a könyv dokumentumok száma, hogy létezik egy adott szerző. Ez az optimalizálás jó lenne olvasni nehéz rendszerek, ahol megengedhetjük magunknak, hogy nem számítások at írja annak érdekében, hogy optimalizálja olvasás.

Az előre kiszámított mezőkkel rendelkező modell használata lehetővé vált, mivel az Azure Cosmos DB támogatja a **többdokumentumos tranzakciókat.** Sok NoSQL áruház nem tud tranzakciókat végrehajtani a dokumentumok között, és ezért támogatja a tervezési döntéseket, mint például a "mindig mindent beágyazza", e korlátozás miatt. Az Azure Cosmos DB segítségével kiszolgálóoldali eseményindítók, vagy tárolt eljárások, amelyek beszúrják a könyveket, és frissíti a szerzők minden egy ACID-tranzakció. Most már nem **kell** mindent beágyaznia egy dokumentumba, csak azért, hogy megbizonyosodjon arról, hogy az adatok konzisztensek maradnak.

## <a name="distinguishing-between-different-document-types"></a>Megkülönböztetés a különböző dokumentumtípusok között

Bizonyos esetekben előfordulhat, hogy különböző dokumentumtípusokat szeretne keverni ugyanabban a gyűjteményben; ez általában akkor áll fenn, ha több, kapcsolódó dokumentumot szeretne ugyanabban a [partícióban](partitioning-overview.md)ülni. Például, akkor tegye mind a könyvek és a könyv `bookId`véleménye ugyanabban a gyűjteményben, és particionálja azt . Ilyen helyzetben általában olyan mezővel szeretnénk hozzáadni a dokumentumokat, amely azonosítja a típusukat, hogy megkülönböztethesse őket.

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

A legnagyobb elvihető ebből a cikkből annak megértése, hogy az adatmodellezés egy sémamentes világban ugyanolyan fontos, mint valaha.

Ahogy egyetlen módja sincs egy adat ábrázolásának a képernyőn, úgy nincs egyetlen módja az adatok modellezésének. Meg kell értenie az alkalmazást, és hogyan fogja előállítani, felhasználni és feldolgozni az adatokat. Ezután az itt bemutatott irányelvek némelyikének alkalmazásával beállíthatja, hogy hozzon létre egy modellt, amely az alkalmazás azonnali igényeit elégíti ki. Ha az alkalmazásoknak módosítaniuk kell, kihasználhatja a sémamentes adatbázis rugalmasságát, hogy könnyen átélhesse ezt a változást, és könnyen fejleszthesse az adatmodellt.

Ha többet szeretne megtudni az Azure Cosmos DB-ről, tekintse meg a szolgáltatás [dokumentációs](https://azure.microsoft.com/documentation/services/cosmos-db/) oldalát.

Ha meg szeretné tudni, hogyan szilánkos az adatok több partíción, tekintse meg [particionálási adatok az Azure Cosmos DB.](sql-api-partition-data.md)

Ha meg szeretné tudni, hogyan modellezzése és particionálása az Azure Cosmos DB-n egy valós példa használatával, olvassa el [az adatmodellezésésés particionálás – egy valós példa.](how-to-model-partition-example.md)
