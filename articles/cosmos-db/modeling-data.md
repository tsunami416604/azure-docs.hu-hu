---
title: "NoSQL-adatbázis dokumentum adatok modellezését |} Microsoft Docs"
description: "További tudnivalók a modellezési adatok NoSQL-adatbázisok"
keywords: "adatok modellezését"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig1
documentationcenter: 
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2016
ms.author: arramac
ms.openlocfilehash: 041982f251ddee7570619c0e7e61d07906aaed2f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modellezési dokumentum adatok NoSQL-adatbázisok
Amíg a sémamentes adatbázisok, például Azure Cosmos DB, könnyebben super könnyen vezessék be a módosításokat az adatmodellbe kell továbbra is töltött bizonyos idő számbavétele szolgál az adatokról. 

Hogyan adatokat fog tárolni? Hogyan lesz beolvasásához és kérdezhet le adatokat az alkalmazást? Az a alkalmazás vastag olvasási vagy írási nehéz? 

A cikk elolvasása után lesz a következő kérdések megválaszolásához:

* Hogyan kell egy dokumentumot egy dokumentum-adatbázisban fontolnunk?
* Mi az adatok modellezési, és miért érdemes I fontos? 
* Hogyan különbözik modellezési adatok dokumentum-adatbázisban és a relációs?
* Hogyan express a nem relációs adatbázis adatok kapcsolatokat?
* Ha adatok beágyazása, és ha hivatkozás adatokhoz?

## <a name="embedding-data"></a>Adatok beágyazása
Amikor elkezdi az adatok Azure Cosmos DB, például egy dokumentum áruházban modellezését megpróbálja az entitások tekinti **önálló dokumentumok** JSON jelöli.

Ahhoz, hogy férhet hozzá túl sokkal tovább, ossza meg velünk néhány lépésekkel vissza és tekintse meg a következő hogyan azt előfordulhat, hogy a modell egy relációs adatbázisban, velünk számos már ismeri a tárgy valamit. A következő példa bemutatja, hogyan tárolódhat egy személy egy relációs adatbázisban. 

![Relációs adatbázis-modell](./media/documentdb-modeling-data/relational-data-model.png)

Relációs adatbázisok használata, ha azt korábban lett tanított évig optimalizálására, optimalizálására, optimalizálására.

Az adatokat általában normalizálása magában foglalja a entitás, például egy személy véve, és bontásához, akkor különálló darabokra adatok. A fenti példában egy személy rendelkezhet több kapcsolattartási részletes rekordok, valamint több címadatokat. Azt még egy lépéssel további, és kapcsolattartási adatai szerint lebontva további kibontása közös mezők, például egy típust. Ugyanazt a címet, itt rekordokban típussal rendelkezik például *Home* vagy *üzleti* 

A irányítsa helyi, amikor adatokat normalizálása **ne tárolja a redundáns adatok** minden egyes jegyezze fel, és inkább adatokra hivatkoznak. Ebben a példában olvasni egy személy, a kapcsolattartási adatait és a címek, szükség hatékonyan összesítésére az adatokat a futási időben a joins ZÁRADÉKOT használják.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Az írási műveletek egyetlen frissítése a kapcsolattartási adatait és a címek igényel sok egyes táblák között. 

Most vessen egy pillantást hogyan azt kellene modell ugyanazokat az adatokat egy önálló egységként dokumentum-adatbázisban.

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

Most már tudunk a fenti megközelítéssel **denormalizált** személy where rögzítse azt **beágyazott** a egyetlen JSON-dokumentumában, például a kapcsolattartási adatai és a címek, ennek a személynek kapcsolatos összes információt.
Ezenkívül mivel azt még nem korlátozódik a rögzített sémájába tudunk műveleteket, például a kapcsolattartási adatait különböző alakzatok teljesen rendelkező rugalmasságot. 

A teljes személy rekord lekérése az adatbázis már olvasási művelete egyetlen gyűjtemény ellen, és egyetlen dokumentum egyetlen. Frissítés személy rekord, a kapcsolattartási adatait és a címet, akkor is egyetlen dokumentum szemben egyetlen írási művelet.

Által denormalizing adatok, az alkalmazás esetleg kevesebb lekérdezések és gyakori műveleteinek elvégzéséhez frissítések. 

### <a name="when-to-embed"></a>Mikor érdemes beágyazása
Általában a beágyazott adatok használata esetén a modellek:

* Nincsenek **tartalmaz** entitások közötti kapcsolatok.
* Nincsenek **egy néhány** entitások közötti kapcsolatok.
* Beágyazott adat, amely **ritkán változó**.
* Nincs beágyazott adatok nem nő **nélkül kötött**.
* Nincs a beágyazott adatok **szerves** a dokumentumban szereplő adatokat.

> [!NOTE]
> Általában a modellek biztosítják jobban adatok denormalizált **olvasási** teljesítményét.
> 
> 

### <a name="when-not-to-embed"></a>Mikor érdemes Nincs beágyazás
Dokumentum-adatbázisban a tapasztalatok denormalize mindent, és egyetlen dokumentum lévő összes adatot beágyazni pedig ez bizonyos helyzetekben, el kell kerülni is járhat.

A JSON-részlet igénybe vehet.

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

Ez akkor lehet, hogy mi a feladás egy vagy több entitás beágyazott megjegyzésekkel néz azt modellezési volt egy tipikus blogba vagy CMS, a rendszer. Ebben a példában a probléma az, hogy a megjegyzések tömb **unbounded**, ami azt jelenti, hogy nincs-e bármilyen egyetlen post rendelkezhet megjegyzések számával (gyakorlati) korlát. Ez válik, hogy a program probléma, mivel a dokumentum méretének sikerült jelentősen megnő.

Ez a dokumentum méretének növekedésével képes továbbítani az adatokat a hálózaton, valamint olvasása és frissítése a dokumentum léptékű keresztül befolyásolja.

Ebben az esetben lenne érdekében fontolja meg a következő modell.

    Post document:
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

    Comment documents:
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

A modellnek az utolsó három megjegyzéseit a feladás egy vagy több saját magát, amely egy tömböt egy rögzített a beágyazott kötött most. A más megjegyzések a 100 megjegyzések kötegekben szerint vannak csoportosítva, és külön dokumentumokban tárolt. A Köteg mérete 100-nak lett választva, mert a fiktív alkalmazás lehetővé teszi, hogy a felhasználó egyszerre csak 100 megjegyzések betöltése.  

Egy másik esetet, amikor beágyazási adatok nem célszerű akkor, ha a beágyazott adatok gyakran használt dokumentumok között, és gyakran változik. 

A JSON-részlet igénybe vehet.

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

Ez lehet egy személy készlet portfóliót. Azt választotta, az készlet minden egyes portfóliót dokumentumhoz információk beágyazható. Olyan környezetben, ahol kapcsolódó adatok gyakran változnak például egy készlet kereskedelmipartner-alkalmazások, adatok gyakran változnak beágyazás érintetlen jelenti azt, hogy folyamatosan frissítjük az egyes portfóliót dokumentum minden alkalommal, amikor egy készlet forog.

Készlet *zaza* egyetlen alkalommal több száz kerülhetnek rendelkezhetnek a nap és a felhasználók ezreit *zaza* a saját portfóliót. Például a fenti azt kell frissíteni a sok ezer portfóliót dokumentumok sokszor adatok modell vezet, hogy a rendszer minden nap, amely nem nagyon jól méretezhető. 

## <a id="Refer"></a>Adatok hivatkozik
Igen sok esetben szépen adatok beágyazása működik, de egyértelmű, hogy nincsenek helyzetek, amikor az adatok denormalizing mint érdemes további problémákat okozhat. Ezért Mi a teendő most? 

Relációs adatbázisok csak az egyetlen hely, ahol létrehozhat entitások közötti kapcsolatok. A dokumentum-adatbázisban egy dokumentumot, hogy ténylegesen vonatkozik, más dokumentumban adatok információkat lehet. Most I vagyok nem javasolni egy percig, még akkor is, hogy azt a rendszerek, akkor lehet jobban megfelel az Azure Cosmos Adatbázisba egy relációs adatbázisban, vagy más dokumentum-adatbázis létrehozása, de egyszerű kapcsolatok rendben, és nagyon hasznos lehet. 

Az alábbi JSON korábbi kívánja használni a példa egy készlet portfóliót a választottuk, de ezúttal hivatkozunk beágyazás helyett portfóliót készlet elemére. Így, ha a készlet elem gyakran megváltoznak egy nap a csak dokumentumot, frissíteni kell a készlet egyetlen dokumentum. 

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


Egy azonnali hátránya, hogy ezt a módszert használja, ha szükség minden olyan személy portfóliót; megjelenítésekor tartott készlet információkat jelenít meg az alkalmazás esetén Ebben az esetben kellene több utazgatással legyen az adatbázis betölti az információt a rendszer dokumentumok. Itt hajtottunk írási műveleteket, amelyek gyakran ismétlődnek a nap folyamán fordulhat elő, de az az olvasási műveletekre, amelyek esetleg kevesebb hatással vannak az adott rendszer teljesítményét pedig sérült hatékonyságának növelése érdekében döntést hoznak.

> [!NOTE]
> Normalizált adatmodellekben **megkövetelheti további kiszolgálókkal való adatváltások számát** a kiszolgálóra.
> 
> 

### <a name="what-about-foreign-keys"></a>Mi a helyzet a külső kulcsokat?
Nincs jelenleg egy megkötés, mert külső kulcsok vagy egyéb, rendelkezésre álló dokumentumok közötti dokumentum kapcsolat hatékony "gyenge links" és maga az adatbázis nem ellenőrzi. Ha azt szeretné, annak érdekében, hogy az adatok egy dokumentum hivatkozik valóban létezik, akkor szüksége ehhez az alkalmazás vagy kiszolgálóoldali eseményindítók és tárolt eljárások az Azure Cosmos-adatbázis használatával.

### <a name="when-to-reference"></a>Mikor kell hivatkoznia
Általában a normalizált adatok használata esetén a modellek:

* Képviselő **egy-a-többhöz** kapcsolatokat.
* Képviselő **több-a-többhöz** kapcsolatokat.
* A kapcsolódó adatok **változik gyakran**.
* Hivatkozásban szereplő lehet **unbounded**.

> [!NOTE]
> Általában normalizálása jobban biztosít **írási** teljesítményét.
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Ha helyezze a kapcsolatot?
A kapcsolat a növekedési segítségével megállapíthatja, hogy mely dokumentumban a hivatkozás tárolásához.

Ha úgy tekintünk, a JSON, gyártók és -könyvekkel modellek.

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
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

Ha egy publisher könyvek száma korlátozott növekedési rendelkező kicsi, majd tárolja a könyv hivatkozás a közzétevő dokumentumban lévő lehetnek hasznosak. Azonban ha könyvek publisher másodpercenkénti száma unbounded, majd az adatmodell vezetne változtatható, növekvő tömbök, ahogy a fenti példa publisher dokumentumot. 

Váltás körül bit dolgot modell, amely ugyanazokat az adatokat továbbra is jelenti, de most elkerülhetők a nagy változtatható gyűjtemények eredményezne.

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
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

A fenti példában a unbounded gyűjtemény csökkentek azt a közzétevő dokumentum. Ehelyett csak van egy minden könyv-dokumentum publisher mutató hivatkozás.

### <a name="how-do-i-model-manymany-relationships"></a>Hogyan a több: többhöz kapcsolatok modell?
Egy relációs adatbázisban *több: több* kapcsolatok gyakran van modellezve a táblákat, amelyek csak csatlakozás együtt más táblákból származó rekordokat. 

![Táblák illesztése](./media/documentdb-modeling-data/join-table.png)

Előfordulhat, hogy ugyanazt a dokumentumok használatával replikálja, és előállít egy adatmodell, az alábbihoz hasonló kísértésbe.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Ez akkor működik. Azonban betöltésekor vagy a szerző könyveiben rendelkező, vagy a könyv betölteni a szerző, a mindig igényelnének legalább két további lekérdezéseket. Egy lekérdezést a csatlakozó dokumentumot és a csatlakoztatni kívánt dokumentum beolvasási majd egy másik lekérdezést. 

Ha az illesztés tábla csak akkor van kapcsolása együtt adatok két darab, miért nem előfordulásoknál hagyja el teljesen?
Vegye figyelembe a következőket.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Mostantól Ha egy szerző, azonnal tudható, hogy mely általa könyvek, és ezzel szemben ha betöltött könyv dokumentum le kellett volna tudható, hogy szerzőjét vagy azonosítóit. Ez menti a köztes irányuló lekérdezés az illesztési tábla csökkentése kiszolgáló szám kerekítése való adatváltások számát, hogy rendelkezik az alkalmazás. 

## <a id="WrapUp"></a>Adatok a hibrid modellek
A Microsoft most kikeresi beágyazás (vagy denormalizing) és azok upsides rendelkező hivatkozó (vagy normalizálása) adatokat, és minden a biztonság sérüléseinek rendelkezik, és úgy találtuk. 

Mindig kell lennie, vagy nem rendelkezik vagy nem lehet összekeveri dolgot még a Ijedt. 

Az alkalmazás a konkrét használati mintákat és előfordulhat, hogy hol keverése beágyazott esetekben munkaterhelések alapján és hivatkozott adatok szabálykészletében, és sikerült kevesebb kiszolgálóval egyszerűbb alkalmazáslogikát vezethet kerekíteni való adatváltások számát továbbra is a megfelelő szintű teljesítmény megőrzése.

Vegye figyelembe a következő JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Itt azt (főleg) követte a beágyazott modellt, amikor más entitás adatait a legfelső szintű dokumentumban található beágyazva, de más adatok hivatkozik. 

A címjegyzék dokumentum tekinti meg, ha néhány láthatja érdekes mezők, ha úgy tekintünk, a szerzők tömbje. Van egy *azonosító* mezőben, amely körkörösen a szerző dokumentumhoz, általános gyakorlat egy normalizált modell használatával mező, de akkor azt is meg kell *neve* és *thumbnailUrl*. Azt sikerült már csak állapottal *azonosító* az alkalmazások azokat az adatokat, és azt használja a "link" megfelelő Szerző-dokumentumból szükséges, de mivel az alkalmazás megjelenik a szerző nevét és a miniatűr kép jelenik meg minden könyv azt menteni tudja oda-vissza listaként könyv kiszolgálónként denormalizing által **néhány** a szerző adatait.

Biztos Ha a szerző neve megváltozott, vagy frissítse a fénykép azt volna meg kell nyitnia egy frissítés végrehajthat minden könyv legalább egyszer közzététel, de az alkalmazás azt feltételezi, hogy szerzők nem módosul a nevek túl gyakran, ez pedig egy elfogadható tervezési döntés a.  

A példa nincsenek **összesítések előre számított** értékek költséges feldolgozási menti az olvasási művelet. A példában a szerző dokumentumban a beágyazott adatok egy részét az adatai, számított futásidőben. Egy könyv-dokumentum létrehozása minden alkalommal, amikor egy új könyv közzé van téve, **és** countOfBooks mező egy adott szerző tartozó címjegyzék dokumentumok száma alapján számított értékre van beállítva. Az optimalizálás lenne a helyes írásvédett nehéz rendszerekben ahol azt megfizethető számítások végrehajtandó írások olvasási műveletek optimalizálása érdekében.

A lehetővé teszi, hogy előre számított mezők modell lehetséges legyen, mivel az Azure Cosmos DB támogatja **többdokumentumos tranzakció**. Sok NoSQL-tárolókon nem tranzakciók tegye a dokumentumok között, és ezért a tervezési döntéseit, például a "mindig beágyazásához mindent" Ez a korlátozás miatt támogatják. Az Azure Cosmos DB kiszolgálóoldali eseményindítókat vagy tárolt eljárásokat, könyvek beszúrása és szerzők ACID tranzakción belül az összes frissítése is használhatja. Most nem **rendelkezik** beágyazása a tartalmát egy dokumentum csak annak ellenőrzése, hogy az adatok konzisztensek maradnak.

## <a name="NextSteps"></a>Következő lépések
Ez a cikk a legnagyobb takeaways tisztában lenni azzal, hogy a sémamentes világ modellezési adatok éppen olyan fontos, mint valaha is. 

Nincs nincs egyetlen konkrét módszert meghatározni a képernyőn megjelenő adatok egy részét képviseli, mint nincs nincs egyetlen konkrét módszert meghatározni a adatok. Meg kell az alkalmazást, és hogyan azt eredményeznek, felhasználását, és feldolgozni az adatokat. Az itt bemutatott útmutatást némelyike alkalmazásával, beállíthat egy modell, amely a azonnali igényeket elégíti ki az alkalmazás létrehozása. Az alkalmazásokat módosítani kell, amikor kihasználhatják a sémamentes adatbázis bevezető, módosítása és könnyen fejleszteni az adatmodell a rugalmasságot. 

Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg a szolgáltatás [dokumentáció](https://azure.microsoft.com/documentation/services/cosmos-db/) lap. 

Megértése, hogyan shard az adatok így vannak elrendezve több partíciót lásd [particionálás adatokat az Adatbázisba az Azure Cosmos](documentdb-partition-data.md). 
