---
title: "Az SQL-lekérdezések Azure Cosmos DB DocumentDB API-hoz |} Microsoft Docs"
description: "A Azure Cosmos DB SQL-szintaxis, adatbázis fogalmait és az SQL-lekérdezések megismerése. SQL Azure Cosmos adatbázis a JSON lekérdezésnyelvet is használja."
keywords: "SQL-szintaxis, sql-lekérdezést, az sql-lekérdezések, json lekérdezési nyelv, adatbázis fogalmait és az sql-lekérdezések, összesítő függvények"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: arramac
ms.openlocfilehash: 862594bcbd6df8a2c62a12340ceb8096fb6bd691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sql-queries-for-azure-cosmos-db-documentdb-api"></a>Az SQL-lekérdezések Azure Cosmos DB DocumentDB API-hoz.
A Microsoft Azure Cosmos DB támogatja a JSON lekérdezésnyelvet SQL (Structured Query Language) használatával dokumentumok lekérdezését. A cosmos DB valóban sémamentes. A JSON-adatmodell, közvetlenül az adatbázis motorján belül az elkötelezettségének, címtár biztosít automatikus indexeléshez JSON-dokumentumok explicit séma vagy a másodlagos indexek létrehozása nélkül. 

A lekérdezési nyelv a Cosmos DB tervezésekor két célok szem előtt tartásával volt:

* Helyett egy új JSON lekérdező nyelv inventing, akartunk támogatja az SQL. Az SQL ismerős és a népszerű lekérdezés nyelveinek. Cosmos DB SQL formális programozási modellt biztosít a részletes lekérdezéseket JSON-dokumentumok keresztül.
* JSON-adatbázisként dokumentum is lehet futtatni az adatbázismotor közvetlenül a JavaScript azt kapcsolniuk a használni kívánt JavaScript programozási modell építkezve a lekérdezési nyelvhez. A DocumentDB API SQL JavaScript típusrendszernek, kifejezés kiértékelése és függvényhívások feltörték. Ez szolgálna természetes programozási modellt biztosít a leképezések relációs, hierarchikus navigációs JSON-dokumentumokat, automatikus illesztések, térbeli lekérdezéseket és teljesen egyéb funkciók között a JavaScript nyelven írt felhasználói függvény (UDF) meghívását. 

Biztosak vagyunk abban, hogy ezek a képességek csökkenti az alkalmazás és az adatbázis közötti súrlódás billentyűt, és fontosságúak a fejlesztést tesz lehetővé.

Azt javasoljuk, kezdeti lépések, amelyet figyeli az alábbi videót, ahol Aravind Ramachandran jeleníti meg, Cosmos DB tartozó lekérdezési képességeket, és látogasson el a [Tesztlekérdezéseket](http://www.documentdb.com/sql/demo), ahol Cosmos DB kipróbálásához és SQL-lekérdezések futtatása az adatkészletet.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/DataExposedQueryingDocumentDB/player]
> 
> 

Ezt követően térjen vissza a cikkhez, ha először egy SQL-lekérdezés oktatóanyag, amely bemutatja, hogyan néhány egyszerű JSON-dokumentumokat és az SQL-parancsokat.

## <a id="GettingStarted"></a>Ismerkedés az SQL-parancsokat az Cosmos-Adatbázisba
Munkahelyi Cosmos DB SQL megtekintéséhez lehetővé néhány egyszerű JSON-dokumentumok kezdődnie, és néhány egyszerű lekérdezéseket bízná. Fontolja meg e két JSON-dokumentumok két családok kapcsolatban. A Cosmos DB azt nem kell minden sémákat, illetve másodlagos indexek explicit módon létrehozásához. Egyszerűen kell beszúrni a JSON-dokumentumok Cosmos DB gyűjteménybe, és ezt követően lekérdezi. Itt egy egyszerű JSON tudunk dokumentum az Andersen családhoz, a szülők, gyermekek (és azok kedvtelésből), a címet, és a regisztrációs adatait. A dokumentum rendelkezik karakterláncok, számok, a logikai, tömbök és beágyazott tulajdonságait. 

**A dokumentum**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Ez az egyetlen különbség – a második dokumentum `givenName` és `familyName` helyett használt `firstName` és `lastName`.

**A dokumentum**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Most próbáljon néhány lekérdezések írásában, ezeket az adatokat a kulcsfontosságú elemeit annak DocumentDB API SQL megismerését. Például a következő lekérdezés visszaad a Ha az azonosítót tartalmazó mezőt megegyezik `AndersenFamily`. Mivel ez egy `SELECT *`, a lekérdezés eredménye a teljes JSON-dokumentum:

**Lekérdezés**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Most pedig nézzük meg az esetben, ha igazolnia kell formáznia egy másik alakzat JSON-kimenetét. Ez a lekérdezés egy új JSON-objektum nevét és a város, két kijelölt mezővel projektek, ha a cím város azonos nevű állapotként. Ebben az esetben a "NY, NY" megegyezik.

**Lekérdezés**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Eredmények**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


A következő lekérdezés gyermekek minden megadott nevét adja vissza a termékcsalád, amelynek azonosítója megegyezik `WakefieldFamily` a város tartózkodási helye alapján rendezve.

**Lekérdezés**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Eredmények**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Szeretnénk felhívja a figyelmet a Cosmos DB lekérdezési nyelv eddig is láttuk példákból néhány fontos aspektusait:  

* Mivel a DocumentDB API SQL JSON értékek működéséről, alakú sorok és oszlopok helyett entitások fa foglalkozik. Ezért a nyelv lehetővé teszi, hogy tekintse meg a fa bármilyen tetszőleges mélységben csomópontok például `Node1.Node2.Node3…..Nodem`, akárcsak a két rész hivatkozását hivatkozó relációs SQL `<table>.<column>`.   
* A strukturált lekérdezésinyelv séma nélküli adatokkal dolgozik. Ezért a típusrendszernek hozzá dinamikusan kell kötni. Ugyanabban a kifejezésben sikerült yield különböző típusaihoz különböző dokumentumokat. A lekérdezés eredménye egy érvényes JSON-érték, de nem biztos, hogy a rögzített sémájába lehet.  
* Cosmos DB csak szigorú JSON-dokumentumokat támogat. Ez azt jelenti, hogy a rendszert és a kifejezések csak JSON típusok kezelésére korlátozódnak. Tekintse meg a [JSON-specifikáció](http://www.json.org/) további részleteket.  
* A Cosmos DB gyűjtemény egy olyan sémamentes tároló JSON-dokumentumot. Tartalmazási, és nem a primary key és idegen kulcs kapcsolatokat a rendszer implicit módon rögzíti a kapcsolatokat, az adatok entitások belül, és egy gyűjtemény dokumentumok között. Ez egy fontos eleme érdemes a jelen cikkben ismertetett intra-dokumentum illesztések alapján mutat.

## <a id="Indexing"></a>A cosmos DB indexelő
Ahhoz, hogy feltölti a DocumentDB API SQL-szintaxis, az indexelési kialakítást a Cosmos DB felfedezése érdemes. 

Adatbázis indexek célja a különböző űrlapok és alakzatok lekérdezések kiszolgálására minimális erőforrás-felhasználás (például CPU és a bemeneti/kimeneti) ugyanakkor biztosítható a jó teljesítmény és kis késleltetése. Adatbázis lekérdezése a megfelelő index a választott gyakran, mennyi tervezést és kísérletezés igényel. Ezt a módszert használja az adatbázisok séma nélküli, ahol az adatok nem felelnek meg a szigorú séma, és gyorsan fejlődésének kihívást jelent. 

Ezért a Cosmos DB indexelési alrendszer tervezésekor be van állítva a következő célok:

* Indexeljük a dokumentumokat anélkül, hogy a séma: az indexelő alrendszer nem kér a sémaadatok és bármely séma feltételezéseket a dokumentumok győződjön. 
* Hatékony és gazdag hierarchikus és relációs lekérdezések támogatása: az index támogatja a Cosmos DB lekérdezési nyelv hatékonyan, beleértve a hierarchikus és relációs leképezések támogatását.
* Egységes lekérdezések in face of írások tartós kötet támogatása: nagy írási átviteli munkaterhelések egységes lekérdezések esetén az index frissítése Növekményesen, hatékony és online állásuk tartós mennyiségű írási műveleteket. A konzisztens index frissítése különösen fontos a lekérdezések végrehajtása az konzisztencia szintjén, amelyben a felhasználó konfigurálta a dokumentum szolgáltatást.
* Több vállalat kiszolgálása támogatása: a foglalásalapú modellben megadott erőforrás irányításhoz bérlők között, index frissítései a keret rendszererőforrást (Processzor, memória és i/o műveletek száma másodpercenként) replika felosztott belül. 
* Tároló-hatékonyságot biztosít: költséghatékonyság, a lemezen terheléssel jár az index nem kötött és előre jelezhető. Ez elengedhetetlen, mert Cosmos DB lehetővé teszi, hogy a fejlesztő költség-alapú mellékhatásokkal index terhelés alapján a lekérdezési teljesítmény közötti győződjön.  

Tekintse meg a [Azure Cosmos DB samples](https://github.com/Azure/azure-documentdb-net) MSDN minták bemutatja, hogyan konfigurálja az indexelési házirendet egy gyűjtemény. Most folytassuk az Azure Cosmos adatbázis SQL-szintaxis részleteinek.

## <a id="Basics"></a>Az Azure Cosmos adatbázis SQL-lekérdezést alapjai
Minden egyes lekérdezés SELECT záradékában és választható FROM áll és a WHERE záradék / ANSI SQL szabványoknak. Általában minden lekérdezéshez a FROM záradékban lévő adatforrás megjelenik a listán. Ezután a WHERE záradékban a szűrő alkalmazása a forrás JSON-dokumentumok részhalmazának beolvasása. Végezetül a SELECT záradékban szolgál a kért JSON értékeit a kiválasztási listán.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>FROM záradékban
A `FROM <from_specification>` záradék használata nem kötelező, kivéve, ha a forrás szűrt vagy projekció a lekérdezésben később. Ehhez a záradékhoz célja, adja meg az adatforrás, amelyre a lekérdezés működnie kell. Az egész gyűjteményre általában a forrás, de ehelyett egy adhat meg a gyűjtemény egy részét. 

A lekérdezés, például `SELECT * FROM Families` azt jelzi, hogy a teljes családok gyűjteményt a forrás, amelyben enumerálása. Egy legfelső szintű speciális azonosítója segítségével határoz meg a gyűjtemény neve helyett a gyűjteményben. Az alábbi lista tartalmazza a szabályokat, amelyek lekérdezésenként lépnek érvénybe:

* A gyűjtemény akkor jelölhető meg aliasként, például a `SELECT f.id FROM Families AS f` vagy egyszerűen `SELECT f.id FROM Families f`. Itt `f` megegyezik a `Families`. `AS`egy nem kötelező kulcsszót alias azonosító érték.
* Egyszer aliasnevet, az eredeti adatforrás nem köthető. Például `SELECT Families.id FROM Families f` szintaktikailag hibás, mert "Családokat" azonosítóját már nem lehet feloldani.
* Lehet, hogy teljesen minősített mutató hivatkozás fog igénylő összes tulajdonság. Szigorú séma való hiányában ez kényszerítése egyetlen nem egyértelmű kötést elkerülése érdekében. Ezért `SELECT id FROM Families f` szintaktikailag óta a tulajdonság nem `id` nincs kötve.

### <a name="subdocuments"></a>Aldokumentumok
A forrás kisebb részhalmazát is lehet korlátozni. Például számbavétele minden a dokumentumban csak egy részfája, hogy a subroot majd válhat a forráskiszolgálón, a következő példában látható módon:

**Lekérdezés**

    SELECT * 
    FROM Families.children

**Eredmények**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

A fenti példa egy tömb forrásaként használható, amíg az objektum is lehet alkalmazni a forrásaként, amely az alábbi példában is látható: a lekérdezés eredménye, hogy minden érvényes JSON-érték (nem nincs definiálva), amelyek megtalálhatók a forrás tekinthető. Ha egyes termékcsaládok nem rendelkezik egy `address.state` érték, a lekérdezés eredményében ki vannak zárva.

**Lekérdezés**

    SELECT * 
    FROM Families.address.state

**Eredmények**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>A WHERE záradék
A WHERE záradékban (**`WHERE <filter_condition>`**) megadása nem kötelező. Azt adja meg a feltételeket, amelyek a forrás által biztosított a JSON-dokumentumok meg kell felelnie ahhoz, hogy a tartalmazzák a eredménye. Bármely JSON-dokumentum ki kell értékelnie, hogy a megadott feltételeknek, a "true", az eredmény figyelembe kell venni. A WHERE záradékban a index réteg használják annak meghatározására, a forrás azt jelzi, hogy az eredmény része lehet abszolút legkisebb részhalmaza. 

A következő lekérdezés kéri a name tulajdonság, amelynek értéke tartalmazó dokumentumok `AndersenFamily`. Bármely más dokumentum, amely nem rendelkezik name tulajdonsággal, vagy ha az érték nem egyezik `AndersenFamily` ki van zárva. 

**Lekérdezés**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


Az előző példából kiderült, egy egyszerű egyenlőség lekérdezést. A DocumentDB API SQL számos skaláris kifejezést. A leggyakrabban használt olyan bináris és egyoperandusú kifejezés. A forrás JSON-objektumból tulajdonsághivatkozást egyaránt érvényes kifejezések. 

A következő bináris operátor jelenleg támogatott, és a következő példákban látható módon a lekérdezésekben használt:  

<table>
<tr>
<td>Aritmetikai</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bitenként</td>    
<td>|}, &, ^, <<>>,, >>> (nulla-Kitöltés jobbra tolást)</td>
</tr>
<tr>
<td>Logikai</td>
<td>ÉS, VAGY SEM</td>
</tr>
<tr>
<td>Összehasonlítása</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Karakterlánc</td>    
<td>|| (összefűzésére)</td>
</tr>
</table>  


Vessen egy pillantást néhány lekérdezést a bináris operátorok használatával.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Az egyoperandusú operátorokat +,-, ~ és nem is támogatottak, és használhatók lekérdezéseken belül a következő példában látható módon:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Bináris és egyoperandusú operátorok mellett tulajdonsághivatkozást is használhatók. Például `SELECT * FROM Families f WHERE f.isRegistered` adja vissza a tulajdonságot tartalmazó JSON-dokumentum `isRegistered` ahol a tulajdonság értéke megegyezik a JSON `true` érték. Egyéb értékek (false, null, nem definiált, `<number>`, `<string>`, `<object>`, `<array>`stb) kivételével az eredményből forrásdokumentum vezet. 

### <a name="equality-and-comparison-operators"></a>Egyenlőség és összehasonlító operátorok
A következő táblázat egyenlőségi összehasonlítás eredménye a DocumentDB SQL-API bármely két JSON-típusok között.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Nincs definiálva</strong>
         </td>
         <td valign="top">
            <strong>NULL értékű</strong>
         </td>
         <td valign="top">
            <strong>Logikai érték</strong>
         </td>
         <td valign="top">
            <strong>Szám</strong>
         </td>
         <td valign="top">
            <strong>Karakterlánc</strong>
         </td>
         <td valign="top">
            <strong>Objektum</strong>
         </td>
         <td valign="top">
            <strong>A tömb</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nincs definiálva<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>NULL értékű<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Logikai érték<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Szám<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Karakterlánc<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objektum<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>A tömb<strong>
         </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
Nincs definiálva </td>
         <td valign="top">
            <strong>OKÉ</strong>
         </td>
      </tr>
   </tbody>
</table>

Más összehasonlító operátorok többek között a >, > =,! =, < és < =, az alábbi szabályok vonatkoznak:   

* Összehasonlítás típusok között meghatározatlan eredményez.
* Két objektum vagy két összehasonlítása tömbállandó meghatározatlan eredményez.   

Ha a szűrő skaláris kifejezés eredménye nincs definiálva, a megfelelő dokumentum nem szerepel az eredmény, mert meghatározatlan logikailag nem egyenlő a "true"értékre.

### <a name="between-keyword"></a>Kulcsszó között
A BETWEEN kulcsszó használatával express tartományok értékek például ANSI SQL-lekérdezéseket is. KÖZÖTTI használható karakterlánc vagy szám ellen.

Például a lekérdezés által visszaadott összes családba tartozó dokumentumok, amelyben az első gyermek osztályú 1-5 (mind a két szélsőértéket beleértve) közé esik. 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Eltérően ANSI-SQL, használhatja a BETWEEN záradék a következő példában például a FROM záradékban.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

A lekérdezés végrehajtása gyorsabb ne felejtse el elleni bármely numerikus tulajdonságok/elérési utakat a BETWEEN záradék a szűrt index Tartománytípus használó indexelési házirend létrehozása. 

A fő különbség a DocumentDB API és ANSI SQL BETWEEN használata között, hogy akkor is express vegyes típusú tulajdonságokhoz lekérdezések – például lehetséges, hogy "osztály" [5] szám lehet bizonyos dokumentumok és mások számára ("grade4") tartalmazó karakterlánc. Ezekben az esetekben például a JavaScript, a "nem definiált" két különböző típusú eredményt, és a dokumentum összehasonlítása a rendszer kihagyja.

### <a name="logical-and-or-and-not-operators"></a>Logikai (AND, OR, és nem) operátorok
Logikai operátorok működhet a logikai értékek. Ezen operátorok logikai igazság táblázatokban az alábbi táblázatban láthatók.

| VAGY | True (Igaz) | False (Hamis) | Nincs definiálva |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |True (Igaz) |True (Igaz) |
| False (Hamis) |True (Igaz) |False (Hamis) |Nincs definiálva |
| Nincs definiálva |True (Igaz) |Nincs definiálva |Nincs definiálva |

| ÉS | True (Igaz) | False (Hamis) | Nincs definiálva |
| --- | --- | --- | --- |
| True (Igaz) |True (Igaz) |False (Hamis) |Nincs definiálva |
| False (Hamis) |False (Hamis) |False (Hamis) |False (Hamis) |
| Nincs definiálva |Nincs definiálva |False (Hamis) |Nincs definiálva |

| NEM |  |
| --- | --- |
| True (Igaz) |False (Hamis) |
| False (Hamis) |True (Igaz) |
| Nincs definiálva |Nincs definiálva |

### <a name="in-keyword"></a>A kulcsszó
Az IN kulcsszó segítségével ellenőrizze, hogy a megadott érték megegyezik-e a lista bármely értéke. Például a lekérdezés által visszaadott összes családba tartozó dokumentumok ahol az azonosító: "WakefieldFamily" vagy "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Ez a példa visszaadja az összes dokumentumot ahol állapota valamely megadott értékét.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternáris (?) és a Coalesce (?) operátorok
A háromkomponensű és Coalesce műveleteivel végrehajtható feltételes kifejezéseket, például a C# és JavaScript népszerű programozási nyelvek hasonló létrehozásához. 

A háromkomponensű (?) operátor akkor lehet hasznos, ha hozhat létre az új JSON-tulajdonságok menet közben. Például most is lekérdezéseket írhat az osztály szintek például kezdő/köztes/speciális alább látható módon emberi olvasható formába besorolását.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Az operátor például az alábbi lekérdezést a hívások is ágyazhatja.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Más lekérdezési operátorok, ha a feltételes kifejezésben hivatkozott tulajdonságok dokumentumtípus hiányzik, vagy ha a összehasonlított típusok eltérőek, majd ezeket a dokumentumokat nem tartoznak a lekérdezés eredményében.

A Coalesce (?) operátor segítségével hatékonyan ellenőrizze a tulajdonság (más néven van meghatározva) dokumentumban. Ez akkor hasznos, ha félig strukturált lekérdezését vagy vegyes típusú adatokat. Például a lekérdezés visszaadja a "Vezetéknév" Ha jelen van, vagy a "Vezetéknév" Ha nem, akkor a jelen.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Idézőjelek közé zárt tulajdonságelérő
Emellett az idézőjelek közé zárt tulajdonság operátorral tulajdonságok `[]`. Például `SELECT c.grade` és `SELECT c["grade"]` egyenértékű. Ez a szintaxis akkor hasznos, ha kell megadnia egy tulajdonság szóközöket, különleges karaktereket tartalmaz, vagy történik a neve megegyezik egy SQL kulcsszó vagy fenntartott szó.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>SELECT záradékban
A SELECT záradékban (**`SELECT <select_list>`**) megadása kötelező, és határozza meg, milyen értékeket a rendszer beolvassa az a lekérdezés fentiekhez hasonló ANSI-SQL-ben. A részhalmazán, amelyben a forrás dokumentumok felett van szűrve a leképezés fázis, amikor a rendszer beolvassa a megadott JSON-értékeket, és egy új JSON-objektum minden egyes azt az alakzatot átadott bemeneti helyezik át lettek adva. 

A következő példa bemutatja egy tipikus SELECT lekérdezés. 

**Lekérdezés**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Beágyazott tulajdonságai
A következő példában két beágyazott tulajdonságok azt kivetítéséről `f.address.state` és `f.address.city`.

**Lekérdezés**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Leképezési JSON kifejezések is támogatja, a következő példában látható módon:

**Lekérdezés**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Nézzük szerepe `$1` itt. A `SELECT` záradék létre kell hoznia egy JSON-objektum, és nem kulcsra azért van, mert implicit argumentum változónevek kezdve használjuk `$1`. Például a lekérdezés által visszaadott implicit argumentum két változót, címkével `$1` és `$2`.

**Lekérdezés**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Aliasképző
Most tegyük kiterjesztése a fenti példában az explicit aliasképző értékek. Ez a kulcsszó használt aliassal való ellátását. Nem kötelező, a második érték kivetítéséről közben látható `NameInfo`. 

Abban az esetben, ha a lekérdezés két tulajdonság azonos névvel rendelkezik, használt aliassal való ellátását, nevezze át a tulajdonságok közül, hogy azok a tervezett eredmény vannak használatát.

**Lekérdezés**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Skaláris kifejezések
Mellett tulajdonsághivatkozást a SELECT záradék is támogatja a skaláris kifejezések állandók, aritmetikai kifejezésekben, logikai kifejezéseket és stb. Például ez egy egyszerű "Hello, World" lekérdezést.

**Lekérdezés**

    SELECT "Hello World"

**Eredmények**

    [{
      "$1": "Hello World"
    }]


Ez egy összetett példa, amely a skaláris kifejezést használ.

**Lekérdezés**

    SELECT ((2 + 11 % 7)-2)/3    

**Eredmények**

    [{
      "$1": 1.33333
    }]


A következő példában a skaláris kifejezés eredménye egy logikai érték.

**Lekérdezés**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Eredmények**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Az objektum és tömb létrehozása
A DocumentDB API SQL egy másik alapfunkciója tömb vagy objektum-létrehozás. Az előző példában vegye figyelembe, hogy létrehoztunk egy új JSON-objektum. Hasonlóképpen egy is végezhet tömbök a következő példákban látható módon:

**Lekérdezés**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Eredmények**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>ÉRTÉK kulcsszó
A **érték** kulcsszó vissza JSON-érték lehetőséget kínál. Például az alábbi lekérdezés visszaadja a skaláris `"Hello World"` helyett `{$1: "Hello World"}`.

**Lekérdezés**

    SELECT VALUE "Hello World"

**Eredmények**

    [
      "Hello World"
    ]


A következő lekérdezés visszaadja a JSON-érték nélkül a `"address"` címke az eredmények között.

**Lekérdezés**

    SELECT VALUE f.address
    FROM Families f    

**Eredmények**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

Az alábbi példa bővíti a bemutatják, hogyan adhat vissza JSON egyszerű értékeket (a levélszintű a JSON-fa). 

**Lekérdezés**

    SELECT VALUE f.address.state
    FROM Families f    

**Eredmények**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* Operátor
A speciális operátort (*) a rendszer támogatja a dokumentumot a projekt-van. Használatakor az egyetlen tervezett mező kell lennie. Például a lekérdezés során `SELECT * FROM Families f` érvényes, `SELECT VALUE * FROM Families f ` és `SELECT *, f.id FROM Families f ` érvénytelen.

**Lekérdezés**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Eredmények**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>TOP operátor
A felső kulcsszó is használható egy lekérdezés által értékek számának korlátozása. FELSŐ együtt az ORDER BY záradék használata esetén az eredménykészlet korlátozódik rendezett értékek; az első N száma Ellenkező esetben azt számát adja vissza az első N eredmények nem definiált sorrendben. Ajánlott eljárásként a SELECT utasítással, mindig használja az ORDER BY záradék a TOP záradék. Ez az az egyetlen lehetőség kiszámítható módon tudja TOP által érintett sorok jelöléséhez. 

**Lekérdezés**

    SELECT TOP 1 * 
    FROM Families f 

**Eredmények**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

FELSŐ egy állandó értékkel (ahogy fent), vagy a paraméteres lekérdezés változó érték használható. További részletekért lásd a paraméteres lekérdezés az alábbi.

### <a id="Aggregates"></a>Aggregátumfüggvények
Az összesítéseket is elvégezheti a `SELECT` záradékban. Az aggregátumfüggvények egy értékhalmazt a számítás elvégzése, és egyetlen érték visszaadása. Például a következő lekérdezés a gyűjteményen belül családba tartozó dokumentumok számát adja meg.

**Lekérdezés**

    SELECT COUNT(1) 
    FROM Families f 

**Eredmények**

    [{
        "$1": 2
    }]

Az összesítés skaláris érték használatával is visszatérhet a `VALUE` kulcsszó. Például a következő lekérdezés egyetlen számként értékek számát adja vissza:

**Lekérdezés**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Eredmények**

    [ 2 ]

A szűrők együtt is elvégezheti összesíti. Például a következő lekérdezés a Washington állam címével a dokumentumok számát küldi vissza.

**Lekérdezés**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Eredmények**

    [ 1 ]

A következő táblázat a DocumentDB API támogatott összesítő függvények listáját tartalmazza. `SUM`és `AVG` numerikus érték, keresztül hajtja végre, mivel `COUNT`, `MIN`, és `MAX` karakterláncok, a logikai és nullák keresztül hajtható végre. 

| Használat | Leírás |
|-------|-------------|
| SZÁMA | A kifejezés a számú elemet ad vissza. |
| SUM   | A kifejezés értékek összegét adja vissza. |
| PERC   | A kifejezés minimumértékét adja vissza. |
| MAXIMÁLIS SZÁMA   | A kifejezés maximumértékét adja vissza. |
| ÁTLAGOS   | Az értékek átlagát adja vissza. a kifejezést. |

Összesíti egy tömb iteráció eredményeit keresztül is elvégezhető. További információkért lásd: [tömb iterációs lekérdezésekben](#Iteration).

> [!NOTE]
> Az Azure-portálon Query Explorer használata esetén vegye figyelembe, hogy összesítési lekérdezések a részlegesen összesített eredmények adhat vissza a lekérdezés lap. Az SDK-k egyetlen értéket összesítő összes oldalán hoz létre. 
> 
> Kód használatával összesítési lekérdezések végrehajtásához szükséges .NET SDK 1.12.0, a .NET Core SDK 1.1.0-ás vagy a Java SDK 1.9.5 vagy újabb.    
>

## <a id="OrderByClause"></a>ORDER BY záradék
Például az ANSI-SQL-ben megadhat egy választható Order By záradék lekérdezése során. A záradékot tartalmazhat választható növekvő/CSÖKKENŐ argumentumaként adja meg a sorrendet, amelyben eredményeket kell beolvasni.

Például ez kártevőcsaládok a rezidens városnév sorrendjét, amely.

**Lekérdezés**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Eredmények**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

És az alábbiakban kártevőcsaládok létrehozásának dátuma, amely egy számot jelölő, kor alapidőpontjának korábban sorrendjét, amely idő, azaz 1970. január 1. a óta eltelt idő másodpercben.

**Lekérdezés**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Eredmények**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Speciális adatbázis fogalmait és az SQL-lekérdezések

### <a id="Iteration"></a>Ismétlés
Egy új szerkezet művelettel lett hozzáadva a **IN** DocumentDB API SQL támogatást nyújt a JSON-tömbök keresztül léptetés kulcsszót. A FROM forrás iterációs támogatja. Kezdjük az alábbi példa:

**Lekérdezés**

    SELECT * 
    FROM Families.children

**Eredmények**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Most már egy másik lekérdezés keresztül a gyűjtemény gyermekek iterációs végző vizsgáljuk meg. Vegye figyelembe a különbség a kimeneti tömbben. Ez a példa felosztja a `children` és az eredmények simítja egyetlen tömbbe.  

**Lekérdezés**

    SELECT * 
    FROM c IN Families.children

**Eredmények**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Ez további használható szűrést végezni a tömb minden egyes bejegyzés, a következő példában látható módon:

**Lekérdezés**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Eredmények**  

    [{
      "givenName": "Lisa"
    }]

Összesítési tömb iterációs eredményét keresztül is elvégezheti. Például a következő lekérdezés gyermekei közötti összes családok megszámlálása.

**Lekérdezés**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Eredmények**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Illesztése
Táblák között csatlakoztatni kell egy relációs adatbázisban, fontos. A logikai corollary az normalizált sémák. Ezzel szemben a DocumentDB API a nem normalizált adatok modell sémamentes dokumentumok foglalkozik. Ez megfelel a logikai a "önillesztés".

A nyelvi támogató szintaxisa < from_source1 > Csatlakozás < from_source2 > ILLESZTÉSI... CSATLAKOZTASSA az < from_sourceN >. A teljes, ezt adja vissza, amely **N**- rekordokat (a rekord **N** értékek). A táblakonstruktor minden rekordjának összes gyűjtemény alias léptetés alatt az megfelelő készletek által visszaadott érték tartozik. Más szóval ez az egy teljes a a illesztésben részt vevő készlet keresztszorzatát.

Az alábbi példák bemutatják, hogyan működik a JOIN záradékban. A következő példa eredménye nem üres, a forrás minden dokumentumát keresztszorzatát óta és üres üres.

**Lekérdezés**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Eredmények**  

    [{
    }]


A következő példában az illesztés a dokumentumgyökér között van, és a `children` subroot. Egy eltérő termék két JSON-objektumok között. Arra, hogy gyermeke tömb nincs hatékony az ILLESZTÉS mivel azt a egyetlen legfelső szintű a gyermekek tömb nem foglalkoznak. Ezért az eredmény tartalmazza csak két eredményt, mivel minden dokumentumot a tömbbel rendelkező keresztszorzatát pontosan csak egy dokumentum adja eredményül.

**Lekérdezés**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Eredmények**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


A következő példa bemutatja a több hagyományos csatlakozzon:

**Lekérdezés**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Eredmények**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



A legfontosabb, ami arról értesít, hogy a `from_source` , a **csatlakozás** záradék egy iterátor. Igen a folyamat ebben az esetben a következőképpen történik:  

* Bontsa ki az egyes gyermekelem **c** a tömbben.
* A dokumentum gyökerébe határokon termék alkalmazása **f** minden gyermekelemmel rendelkező **c** , amely lett egybesimított-e az első lépésben.
* Végezetül projektre a legfelső szintű objektum **f** névtulajdonság önmagában. 

Az első dokumentum (`AndersenFamily`) csak egy gyermekelemet tartalmaz, ezért az eredménykészlet csak ez a dokumentum megfelelő egyetlen objektumot tartalmaz. A második dokumentum (`WakefieldFamily`) két gyermekeket tartalmaz. Igen a határokon termék minden gyermek, ezáltal két objektum, egy minden gyermek, ez a dokumentum megfelelő eredményezve egy külön objektumot hoz létre. A legfelső szintű mezők mindkét ezekben a dokumentumokban ugyanazok, mint egy határokon termékben teheti meg.

A valós segédprogram csatlakozási űrlap rekordokat származik, amely egyébként nehezen projekt alakzat a kereszt-termék. Ezenkívül az alábbi példában látható módon szűrést az, hogy megadható, hogy a felhasználó döntött, hogy a rekordokat a teljes feltételfüggvényt feltétel rekordot kombinációja.

**Lekérdezés**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Eredmények**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Ebben a példában a fenti példában természetes bővítménye, és végrehajtja a dupla való csatlakozást. A határokon termék tehát tekintheti meg a következő látszólagosan kódot:

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`egy gyermek, aki rendelkezik egy háziállat rendelkezik. Igen, a határokon termék eredményez több sorban is (1\*1\*1) a család. WakefieldFamily, azonban a két gyermekelemek tartoznak, de csak egy "Jesse" gyermeket kedvtelésből. Jesse két kedvtelésből, ha rendelkezik. Ezért a határokon termék eredményez 1\*1\*2 = 2 család a sort.

A következő példában nincs egy kiegészítő szűrőt `pet`. Ez nem tartalmazza az összes rekordokat, ahol a háziállatának neve nincs "Árnyékmásolat". Figyelje meg, hogy azt képesek tömbök, az a rekord elemek szűrő származó rekordokat létrehozni, és az elemek kombinációja projektre. 

**Lekérdezés**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Eredmények**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>JavaScript-integráció
Azure Cosmos DB programozási modellt biztosít a feldolgozás alatt álló alapú JavaScript-alkalmazáslogika közvetlenül a gyűjtemények, tárolt eljárások és eseményindítók tekintetében. Ez lehetővé teszi, hogy mindkét:

* Lehetővé teszi nagy teljesítményű tranzakciós CRUD műveletek és a JavaScript futásidejű közvetlenül az adatbázis motorján belül szoros integrációja alapján egy gyűjtemény-dokumentumokon végzett lekérdezések. 
* Természetes modellezési folyamatábrán, változó hatókörének, és a hozzárendelés és az adatbázis-tranzakciókhoz a primitívek kivételkezelő integrálását. A JavaScript-integráció Azure Cosmos DB-támogatással kapcsolatos további információkért tekintse meg a JavaScript kiszolgálóoldali programozhatóság dokumentációját.

### <a id="UserDefinedFunctions"></a>Felhasználói függvény (UDF)
A típusok már definiálva van ebben a cikkben, valamint a DocumentDB API SQL támogatja az a felhasználó definiált függvény (UDF). Skaláris felhasználó által megadott függvények támogatottak, ahol a fejlesztők nulla vagy több argumentumot adjon át és vissza egyetlen argumentuma eredményt vissza. Minden egyes argumentum ellenőrzése alatt álló engedélyezett JSON-érték.  

A DocumentDB API SQL-szintaxis használatával az ezen felhasználó által definiált függvényeket egyéni alkalmazáslogika támogatása az időtartam. Felhasználó által megadott függvények regisztrálhatók a DocumentDB API, és ezután lehet hivatkozni az SQL-lekérdezés részeként. Valójában a felhasználó által megadott függvények exquisitely tervezték, hogy a lekérdezések hívható. Ezt a döntést maradhassanak felhasználó által megadott függvények nincs hozzáférése a context objektumot, a más JavaScript típusok (tárolt eljárások és eseményindítók) rendelkező. Lekérdezések csak olvashatóként hajtható végre, mert futtathatják az elsődleges vagy másodlagos replikákon. Ezért felhasználó által megadott függvények való más JavaScript típusától eltérően a másodlagos replikákon futtatásra tervezték.

Alább példája egy UDF hogyan lehet regisztrálni, a Cosmos DB adatbázist, kifejezetten egy dokumentumgyűjteményt.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

Az előző példa létrehoz egy UDF, amelynek a neve `REGEX_MATCH`. Elfogadja a JSON két karakterlánc-értékek `input` és `pattern` és ellenőrzést, ha az első megfelel a mintának megadott második JavaScript string.match() függvény használatával.

A Microsoft most már használhatja a UDF leképezés lekérdezést. Felhasználó által megadott függvények kell minősíteni, a kis-és nagybetűket előtaggal "udf." Amikor meghívhatók lekérdezések. 

> [!NOTE]
> 3/17/2015, mielőtt Cosmos DB támogatott UDF hívások nélkül az "udf." Válasszon REGEX_MATCH() például előtag. A hívó mintát elavult.  
> 
> 

**Lekérdezés**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Eredmények**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Az UDF is használható egy szűrő belül ahogy az alábbi példában is tartománynévvel együtt az "udf." előtagja:

**Lekérdezés**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Eredmények**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Felhasználó által megadott függvények lényegében érvényes skaláris kifejezések, és leképezések és szűrőket is használhat. 

Bontsa ki a felhasználó által megadott függvények hatványa, vizsgáljuk meg egy másik példa feltételes logikával:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Az alábbiakban látható egy példa, gyakorolja az UDF-ben.

**Lekérdezés**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Eredmények**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


A fenti példákban megjelenítve, mert felhasználó által megadott függvények JavaScript nyelv power a DocumentDB API SQL komplex eljárási, feltételes logikai beépített JavaScript futás közbeni képességek segítségével. Ehhez egy gazdag programozható felületet integrálhatja.

A DocumentDB API SQL biztosít az argumentumok a felhasználó által megadott függvények nyilvántartott egyes dokumentumok a forráshelyen szakaszában az aktuális (a WHERE záradékban vagy a SELECT záradékban) UDF feldolgozása. Az eredmény zökkenőmentesen beépített általános végrehajtás folyamatban. Ha a Tulajdonságok által az UDF paraméterek nem találhatók a JSON-érték, akkor a paraméter nincs definiálva, és ezért a rendszer teljesen kihagyja UDF meghívását. Hasonló módon az UDF eredménye nem definiált, ha az nem szerepel az eredményben. 

Összefoglalva felhasználó által megadott függvények olyan nagy eszközöket tegye a bonyolult üzleti logikát a lekérdezés részeként.

### <a name="operator-evaluation"></a>A kiértékelési operátor
Cosmos DB, egy JSON-adatbázis, amely nem rendelkezik megrajzolja fekvő JavaScript operátorok és az értékelés szemantikáját. Miközben Cosmos DB megpróbálja megőrizheti a JavaScript szemantikáját JSON támogatása szempontjából, a művelet kiértékelése százalékkal, bizonyos esetekben.

A DocumentDB API SQL, ellentétben a hagyományos SQL típusú értékeket gyakran nem ismert mindaddig, amíg a rendszer beolvassa az értékeket az adatbázis. Ahhoz, hogy hatékonyan hajtsa végre a lekérdezéseket, a kezelők többsége a szigorú szemben támasztott követelményeit. 

A DocumentDB API SQL nem hajtható végre implicit konverzió JavaScript eltérően. Például egy lekérdezést, például `SELECT * FROM Person p WHERE p.Age = 21` megegyezik egy kora tulajdonságot, amelynek értéke 21 tartalmazó dokumentumokat. Bármely más, amelynek kora tulajdonsága egyezést mutat a karakterlánc a "21", vagy más valószínűleg végtelen változata dokumentum, például "021", "21,0", "0021", "00021", nem fog egyeztetni stb. Ez a számára a JavaScript-számok implicit módon casted a karakterlánc-értékek esetén ezzel szemben az (pl. operátor szerinti szűrése, alapján: ==). Ez a beállítás nem megfelelő DocumentDB API SQL hatékony index számára elengedhetetlen. 

## <a name="parameterized-sql-queries"></a>A paraméteres SQL-lekérdezések
Cosmos DB lekérdezéseket támogat, a @ notation az ismerős kifejezett paraméterekkel. A paraméteres SQL hatékony kezelése és escape-karaktersorozat felhasználói bevitelt, megakadályozza az SQL-injektálás az adatok véletlen kitettség biztosít. 

Például, hogy a Vezetéknév és címállapot fogad paraméterként, és hajthat végre különböző értékek vezetékneve és a felhasználói bevitel alapján cím állapotát.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

A kérelem majd küldhető Cosmos DB JSON paraméteres például lekérdezésként alább látható.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

ELSŐ argumentumának állíthat be például a paraméteres lekérdezés alább látható.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

A paraméterértékek lehet bármely érvényes JSON (karakterláncok, számok, a logikai, null, akkor is igaz, tömbök, vagy beágyazott JSON). Is mivel Cosmos DB séma nélküli, paraméterek a rendszer nem érvényesíti bármilyen ellen.

## <a id="BuiltinFunctions"></a>Beépített funkciók
Cosmos DB számos beépített funkciót is támogatja a közös műveleteket, például a felhasználói függvény (UDF) lekérdezéseken belül használható.

| Csoport          | Műveletek                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Matematikai funkciók  | ABS, felső határ, EXP, EMELET, napló, LOG10, ENERGIAGAZDÁLKODÁSI, CIKLIKUS, bejelentkezési, SQRT, SZÖGLETES, csonk, ARCCOS, ARCSIN, ATAN, ATN2, COS, tűz, fok, PI, radiánban megadott szög, EG és TAN |
| Írja be az ellenőrzési funkciók | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED és IS_PRIMITIVE                                                           |
| Karakterlánc        | CONCAT, tartalmazza, megadott módon VÉGZŐDŐ, INDEX_OF, balra, hossza, alsó, LTRIM, csere, REPLIKÁLJA, NÉVKERESÉSI, jobbra, RTRIM, megadott módon KEZDŐDŐ, SUBSTRING és felső       |
| A tömb funkciók         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH és ARRAY_SLICE                                                                                         |
| Térbeli funkciók       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID és ST_ISVALIDDETAILED                                                                           | 

Jelenleg használ egy felhasználói függvény (UDF), amelynek beépített függvény mostantól, ha kell használnia a megfelelő beépített funkciót, akkor lesz futtatásához gyorsabb és hatékonyabb. 

### <a name="mathematical-functions"></a>Matematikai funkciók
A matematikai funkciók végezhet a számítást, a bemeneti értékek, amelyek argumentumként szolgálnak, és a visszaadandó numerikus érték alapján. Itt található a támogatott beépített matematikai függvények táblázatát.


| Használat | Leírás |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza. |
| [Felső határ (num_expr)](#bk_ceiling) | A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés. |
| [EMELET (num_expr)](#bk_floor) | A legnagyobb egész számot ad vissza kisebb vagy egyenlő, mint a megadott numerikus kifejezés. |
| [EXP (num_expr)](#bk_exp) | A megadott numerikus kifejezés hatványát adja vissza. |
| [NAPLÓ (num_expr [, Alap])](#bk_log) | A megadott numerikus kifejezés, vagy használja a megadott alapban logaritmusát a természetes alapú logaritmusát adja vissza |
| [LOG10 (num_expr)](#bk_log10) | A 10-es logaritmikus a megadott numerikus kifejezés értékét adja vissza. |
| [KEREK (num_expr)](#bk_round) | Egy numerikus érték, a legközelebbi egész értéket kerekítve adja vissza. |
| [CSONK (num_expr)](#bk_trunc) | Egy numerikus érték, csak az a legközelebbi egész értéket ad vissza. |
| [SQRT (num_expr)](#bk_sqrt) | A megadott numerikus kifejezés négyzetgyökét adja vissza. |
| [NÉGYZETES (num_expr)](#bk_square) | Kiszámítja a megadott numerikus kifejezés. |
| [ENERGIAGAZDÁLKODÁSI (num_expr, num_expr)](#bk_power) | A megadott numerikus kifejezés power visszatér a megadott érték. |
| [BEJELENTKEZÉSI (num_expr)](#bk_sign) | A megadott numerikus kifejezés bejelentkezési értékét (-1, 0, 1) adja vissza. |
| [ARCCOS (num_expr)](#bk_acos) | A szöget adja vissza, az radiánban megadott szög, amelynek koszinusza a megadott numerikus kifejezés; más néven koszinuszát. |
| [ARCSIN (num_expr)](#bk_asin) | A szög radiánban megadott szög, amelynek szinusza a megadott numerikus kifejezést ad vissza. Ez rövidítése szinuszát. |
| [ATAN (num_expr)](#bk_atan) | A szög radiánban megadott szög, amelynek tangense a megadott numerikus kifejezést ad vissza. Ezt arkusz is nevezik. |
| [ATN2 (num_expr)](#bk_atn2) | A szöget adja vissza, az x tengely pozitív és a pont (y, x), a forrásból a ray közötti radiánban ahol x és y az érték a két megadott lebegőpontos kifejezés. |
| [COS (num_expr)](#bk_cos) | Koszinuszát trigonometric a megadott szög radiánban, a megadott kifejezésben. |
| [TŰZ (num_expr)](#bk_cot) | A megadott szög trigonometric kotangensét adja meg a megadott numerikus kifejezés radiánban. |
| [Fokban megadva (num_expr)](#bk_degrees) | A megfelelő szöget adja vissza, az a radiánban megadott szög fokban megadva. |
| [PI)](#bk_pi) | A konstans PI értékét adja vissza. |
| [RADIÁNBAN (num_expr)](#bk_radians) | Vissza a radiánban megadott szög, ha egy numerikus kifejezés fokban, is meg kell adni. |
| [EG (num_expr)](#bk_sin) | Szinuszát trigonometric a megadott szög radiánban, a megadott kifejezésben. |
| [TAN (num_expr)](#bk_tan) | A bemeneti kifejezést tangensét adja vissza a megadott kifejezésben. |

Például most lekérdezéseket is futtathat a következőhöz hasonló:

**Lekérdezés**

    SELECT VALUE ABS(-4)

**Eredmények**

    [4]

A Cosmos DB funkciók ANSI SQL képest közötti fő különbség a, hogy úgy vannak kialakítva, hogy működnek jól séma nélküli és vegyes séma adatokat. Például ha egy dokumentum, ahol a Size tulajdonság hiányzik, vagy rendelkezik-e nem numerikus érték, például "Ismeretlen", majd a dokumentum keresztül, a rendszer kihagyja helyett hibát ad vissza.

### <a name="type-checking-functions"></a>Írja be az ellenőrzési funkciók
A típus ellenőrzési funkciók lehetővé teszik az SQL-lekérdezések lévő kifejezés típusa. Típus ellenőrzési funkciók segítségével határozható meg, hogy a dokumentumokat tulajdonságokat típusú változó vagy ismeretlen. Ez a táblázat beépített típusa támogatott funkciók ellenőrzése.

<table>
<tr>
  <td><strong>Használat</strong></td>
  <td><strong>Leírás</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa tömb logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa olyan logikai érték logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (kifejezés)</a></td>
  <td>Olyan logikai érték, amely azt jelzi, ha az érték típusa null beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa több logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa egy JSON-objektum logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa karakterlánc logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (kifejezés)</a></td>
  <td>Jelzi, ha a tulajdonság van rendelve egy érték logikai érték beolvasása.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (kifejezés)</a></td>
  <td>Azt jelzi, hogy ha az érték típusa karakterlánc, szám, logikai érték vagy null logikai érték beolvasása.</td>
</tr>

</table>

Ezeket a funkciókat használ, most lekérdezéseket is futtathat a következőhöz hasonló:

**Lekérdezés**

    SELECT VALUE IS_NUMBER(-4)

**Eredmények**

    [true]

### <a name="string-functions"></a>Karakterlánc
A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-értékkel, és a karakterlánc, a numerikus és logikai értéket adja vissza. Itt a következő táblázat a beépített karakterlánc:

| Használat | Leírás |
| --- | --- |
| [A hossz (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |A megadott karakterlánc-kifejezés karakterek számát adja vissza |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Karakterlánc, amely legalább két karakterlánc-értékek hozzáfűzésével eredményét adja vissza. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Egy karakterlánc-kifejezés részét adja vissza. |
| [(Str_expr, str_expr) startswith ELEMNEK](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Adja vissza egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés a második végződik |
| [Megadott módon VÉGZŐDŐ (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Adja vissza egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés a második végződik |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Visszaadja egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés tartalmazza a második. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés az első megadott karakterlánc-kifejezés vagy -1, ha a karakterlánc nem található. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza. |
| [JOBB (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |A megadott számú karakterből álló karakterlánc jobb oldali részét adja vissza. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Egy karakterlánc-kifejezés adja vissza, után eltávolítja a kezdő üres. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Egy karakterlánc-kifejezés az összes záró szóközöket csonkítása után adja vissza. |
| [ALSÓ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Egy karakterlánc-kifejezés után nagybetűt adatok kisbetűssé alakításával adja vissza. |
| [FELSŐ (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza. |
| [Cserélje le a (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Megadott karakterlánc-érték összes előfordulását lecseréli egy másik karakterlánc. |
| [REPLIKÁLÁS (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-reference#bk_replicate) |A megadott számú alkalommal megismétel egy karakterlánc-érték. |
| [NÉVKERESÉSI (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |A Fordított sorrend egy karakterlánc értékét adja vissza. |

Ezeket a funkciókat használ, most lekérdezéseket is futtathat a következőhöz hasonló. Például lépjen vissza a családnevet nagybetűs az alábbiak szerint:

**Lekérdezés**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Eredmények**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Vagy ebben a példában például karakterlánc összefűzésére:

**Lekérdezés**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Eredmények**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Karakterlánc is használható a WHERE záradékban szűrése eredményeket, például a következő példa:

**Lekérdezés**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Eredmények**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>A tömb funkciók
A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a numerikus visszatérési, a logikai érték vagy tömb érték. Beépített tömb függvények táblázatát itt található:

| Használat | Leírás |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |A megadott tömb kifejezés elemek számát adja vissza. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Olyan tömb, amely két vagy több tömb értékek hozzáfűzésével eredményét adja vissza. |
| [ARRAY_CONTAINS (arr_expr, kifejezés [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Jelzi, hogy a tömb tartalmaz-e a megadott érték logikai érték beolvasása. Ha az egyezés-e a teljes vagy részleges adhat meg. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Egy tömböt megadó kifejezést részét adja vissza. |

Tömb funkciók segítségével kezelheti a tömbök JSON belül. Például itt található összes dokumentum visszaadó, ahol a szülők egyik "Multiplexelés Wakefield". 

**Lekérdezés**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Eredmények**

    [{
      "id": "WakefieldFamily"
    }]

Megadhatja az egyező elemek a tömbön belüli részleges töredéket. A következő lekérdezés megkeresi az összes szülők a `givenName` a `Robin`.

**Lekérdezés**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Eredmények**

    [{
      "id": "WakefieldFamily"
    }]


Ez például akkor ARRAY_LENGTH használatával gyermekek termékcsalád másodpercenkénti számát.

**Lekérdezés**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Eredmények**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Térbeli funkciók
Cosmos DB földrajzi lekérdezése a következő nyissa meg a földrajzi konzorcium (OGC) beépített funkciókat támogatja. 

<table>
<tr>
  <td><strong>Használat</strong></td>
  <td><strong>Leírás</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Csoport távolságát adja vissza a két GeoJSON-pont, sokszög vagy LineString kifejezések között.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Egy logikai kifejezés jelző az első GeoJSON-objektum (pont, sokszög vagy LineString) objektumban a második GeoJSON (pont, sokszög vagy LineString) adja vissza.</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Egy logikai kifejezés, amely azt jelzi, hogy a két megadott GeoJSON objektumokat (pont, Polygon, vagy LineString) intersect adja vissza.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Azt jelzi, hogy, hogy a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes logikai érték beolvasása.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Egy olyan logikai érték tartalmazó JSON-érték. Ha a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes, és ha érvénytelen értéket adja vissza, továbbá karakterláncként okát.</td>
</tr>
</table>

Térbeli funkciók térbeli adatok közelségi kapcsolat lekérdezések végrehajtásához használható. Például ez visszaadó 30 km-ST_DISTANCE beépített funkcióval a megadott helyen belüli összes termékcsalád dokumentumot. 

**Lekérdezés**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Eredmények**

    [{
      "id": "WakefieldFamily"
    }]

A földrajzi támogatásáról Cosmos DB további részletekért lásd: [földrajzi adatok az Azure Cosmos DB](geospatial.md). Amely foglalja össze a térbeli függvények, és az SQL-szintaxis, a Cosmos DB. Most vessen egy pillantást, hogyan működik, és hogy milyen hatással a használatával lekérdezése LINQ is láttuk eddig.

## <a id="Linq"></a>A DocumentDB API SQL LINQ
LINQ .NET programozási modell, amely szerint az objektumok adatfolyamok lekérdezései számítási kifejezze. Cosmos DB egy ügyféloldali szalagtár LINQ illesztőfelület biztosít a JSON és a .NET-objektumok és a leképezés egy LINQ-lekérdezések részét csak akkor Cosmos DB lekérdezések közötti konverzió megkönnyítésével. 

Az alábbi képen a LINQ-lekérdezések Cosmos DB használatával architektúráját mutatja be.  A Cosmos DB-ügyfélprogram segítségével a fejlesztők hozhat létre egy **IQueryable** objektum, amely közvetlenül a Cosmos DB lekérdezés szolgáltatót, majd a LINQ lekérdezés fordítja le egy Cosmos-adatbázis-lekérdezés. A lekérdezés majd kerülnek a Cosmos DB kiszolgálót egy halmazát, az eredmények JSON formátumban. A keresés eredményeit azokat az ügyféloldali .NET objektumok adatfolyam vannak deszerializálni.

![A LINQ-lekérdezések használata a DocumentDB API - SQL-szintaxis, JSON lekérdező nyelv, adatbázis fogalmait és az SQL-lekérdezések architektúrája][1]

### <a name="net-and-json-mapping"></a>.NET és a JSON-leképezés
A .NET-objektumokat és a JSON-dokumentumok közötti leképezéseket természetes – minden tag adatmező van rendelve egy JSON-objektum, ahol a mező neve az objektum "kulcsot" része van leképezve, és a "érték" része rekurzív módon leképezve az objektum érték részét. Vegye figyelembe az alábbi példa: A család objektum létrehozása a JSON-dokumentumhoz van rendelve, alább látható módon. És ez fordítva is igaz, a JSON-dokumentumhoz van rendelve vissza egy .NET-objektum.

**C#-osztály**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>"LINQ to SQL fordítási"
A Cosmos DB lekérdezésszolgáltató hajt végre, egy Cosmos-adatbázis SQL-lekérdezést az elérhető legjobb leképezéseket a LINQ lekérdezés. A következő leírásában feltételezzük, hogy az olvasó rendelkezik a LINQ alapszintű ismeretét.

Először a típus rendszer esetében támogatott összes JSON egyszerű típusokhoz – numerikus típusok, logikai érték, karakterlánc vagy null. Ezek a JSON típusok támogatottak. A következő skaláris kifejezések használhatók.

* Állandó értékek – ezek közé tartozik az egyszerű adattípusok állandó értékek a lekérdezés kiértékelése időpontjában.
* Tekintse meg a tulajdonság az objektum vagy tömb elem/tulajdonságtömb-index kifejezések – ezek a kifejezések.
  
     termékcsalád. Azonosító;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n egy int változó
* Aritmetikai kifejezésekben - ezek közé tartozik a numerikus és logikai értékek a közös aritmetikai kifejezésekben. A teljes listát lásd az SQL-specifikációnak.
  
     2 * family.children[0].grade;    az x + y;
* Karakterlánc-összehasonlítási kifejezés - ezek közé tartozik egy karakterláncértéket néhány állandó karakterlánc összehasonlítása.  
  
     mother.familyName == "Smith";    child.givenName == s; egy karakterlánc-változóvá-je
* Objektum vagy tömb létrehozása kifejezés - ezek a kifejezések visszatérési összetett érték vagy névtelen típusú objektum vagy egy ilyen objektumokból álló tömb. Ezek az értékek egymásba ágyazható.
  
     új szülő {familyName = a "Smith" givenName = "Joe"}; új {első = 1, a második = 2}; egy két mező rendelkező névtelen típusú              
     új int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Támogatott LINQ operátorok listája
A LINQ szolgáltatónál tartalmazza a DocumentDB .NET SDK-val támogatott LINQ operátorokat listája itt található.

* **Válassza ki**: leképezések lefordítani az SQL, válassza ki például objektumkonstrukciók
* **Ha**: szűrők lefordítani az SQL WHERE, és támogatja a közötti címfordítás & &, || és! az SQL-operátorok
* **A selectmany metódus**: lehetővé teszi a tömbök számára az SQL JOIN záradékban visszagörgetésének. Lánc/nest tömbelemek szűrési kifejezésekben használható
* **OrderBy és OrderByDescending**: az eszköz ORDER BY növekvő/csökkenő
* **Count**, **Sum**, **Min**, **maximális**, és **átlagos** összesítő és a megfelelő aszinkron operátorok **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, és **AverageAsync**.
* **CompareTo**: tartomány módon történő összehasonlítása az eszköz. Gyakran használt karakterláncok óta fontosságúak nem hasonlítható össze az .NET
* **Igénybe**: az eszköz egy lekérdezés eredményeként előálló korlátozó SQL felső
* **Matematikai függvények**: támogatja a fordítás. NET tartozó Abs, ARCCOS, ARCSIN, Atan Cos felső határa, Exp, emelet, napló, Log10, Pow, ciklikus, bejelentkezési, EG, Sqrt, Tan, a megfelelő SQL beépített funkciók Truncate.
* **Karakterlánc**: támogatja a fordítás. NET tartozó Concat, Contains, megadott módon végződő, IndexOf, Count, ToLower, TrimStart, csere, névkeresési, TrimEnd, megadott módon kezdődő, SubString, a megfelelő SQL beépített funkciók ToUpper.
* **A tömb funkciók**: támogatja a fordítás. NET tartozó Concat Contains és számát, hogy a megfelelő SQL beépített funkciók.
* **A földrajzi Kiterjesztésfüggvények**: támogatja a megfelelő SQL beépített funkciók helyettes módszerek távolság IsValid és IsValidDetailed belül a fordítás.
* **Felhasználó által definiált függvény kiterjesztésfüggvény**: támogatja a megfelelő felhasználó által definiált függvény a csonkmetódus UserDefinedFunctionProvider.Invoke a fordítás.
* **Vegyes**: támogatja a coalesce és feltételes operátort fordítását. Lefordíthatja karakterláncot tartalmaz, ARRAY_CONTAINS vagy az SQL-IN attól függően, hogy a környezet tartalmaz.

### <a name="sql-query-operators"></a>SQL-lekérdezési operátorok
Íme néhány példa, amelyek bemutatják, hogyan átszámítani egyes szabványos LINQ lekérdezés operátorok az Cosmos DB lekérdezések le.

#### <a name="select-operator"></a>Válasszon operátort
A szintaxis a következő `input.Select(x => f(x))`, ahol `f` egy skaláris kifejezés.

**LINQ lambda kifejezés**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ lambda kifejezés**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ lambda kifejezés**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>A selectmany metódus operátor
A szintaxis a következő `input.SelectMany(x => f(x))`, ahol `f` egy skaláris kifejezés, amely a gyűjteménytípus adja vissza.

**LINQ lambda kifejezés**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Ha operátor
A szintaxis a következő `input.Where(x => f(x))`, ahol `f` van egy skaláris kifejezés, amely egy logikai értéket ad vissza.

**LINQ lambda kifejezés**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ lambda kifejezés**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Összetett SQL-lekérdezések
A fenti operátorok kell összeállítani, nagyobb teljesítményű lekérdezések kialakításához. Mivel Cosmos DB beágyazott gyűjtemények támogatja, az adott összeállításban kell összefűzendő, vagy beágyazott.

#### <a name="concatenation"></a>Összefűzése
A szintaxis a következő `input(.|.SelectMany())(.Select()|.Where())*`. Olyan összefűzött lekérdezés elindíthatja és egy opcionális `SelectMany` lekérdezés követ több `Select` vagy `Where` operátorok.

**LINQ lambda kifejezés**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ lambda kifejezés**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ lambda kifejezés**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ lambda kifejezés**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>A beágyazási
A szintaxis a következő `input.SelectMany(x=>x.Q())` Q esetén egy `Select`, `SelectMany`, vagy `Where` operátor.

Egy beágyazott lekérdezésen a belső lekérdezés alkalmazzák a külső gyűjtemény minden eleme. Egyik fontos szolgáltatása, hogy a belső lekérdezés jelentheti a mezőket, például a külső gyűjtemény elemeinek Önillesztések.

**LINQ lambda kifejezés**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ lambda kifejezés**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ lambda kifejezés**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>SQL-lekérdezések végrehajtása
A cosmos DB keresztül tesz elérhetővé erőforrásokat egy REST API-t, amely képes a HTTP/HTTPS-kérést bármely olyan nyelvvel hívható. Ezenfelül a Cosmos DB programozási kódtárakat, például a .NET, Node.js, JavaScript és Python számos népszerű nyelvhez biztosít. A REST API-t és a különböző könyvtárak támogatja keresztül SQL lekérdezése. A .NET SDK LINQ lekérdezése SQL mellett támogatja.

A következő példák bemutatják, hogyan hozzon létre egy lekérdezést, és küldje el egy Cosmos-adatbázis adatbázis-fiók.

### <a id="RestAPI"></a>REST API-N
Cosmos DB egy megnyitott RESTful programozási modellt biztosít a HTTP Protokollon keresztül. Adatbázis-fiókok egy Azure-előfizetés használatával telepíthető. A Cosmos DB erőforrás-modellje egy adatbázis-fiók, amelyek egy-címezhető logikai és állandó URI-k használata alatt lévő erőforrások készlete áll. Erőforráscsoport ebben a dokumentumban adatcsatornára nevezzük. Az adatbázisfiók áll az adatbázisok, mindegyike több gyűjteményt, mely szolgálna mindegyikének tartalmazza a dokumentumok, a felhasználó által megadott függvények és a más típusú erőforrások.

Az alapvető interakció modell ezekkel az erőforrásokkal keresztül történik a HTTP-műveletek GET, PUT, POST és DELETE a szabványos tolmácsolási szolgáltatással. A POST műveletet egy új erőforrást, egy tárolt eljárás végrehajtása vagy egy Cosmos-adatbázis-lekérdezés kiadására szolgál. Lekérdezéseket a rendszer mindig csak olvasható műveletekhez, nincs mellékhatásokkal.

Az alábbi példák bemutatják a FELADÁS egy vagy több, amennyiben azt már áttekintette a két minta dokumentumok tartalmazó gyűjtemény ellen DocumentDB API lekérdezéshez. A lekérdezés egy egyszerű szűrési a JSON-name tulajdonsággal rendelkezik. Vegye figyelembe a használatát a `x-ms-documentdb-isquery` és a Content-Type: `application/query+json` fejlécek, hogy-e a művelet egy lekérdezést jelöléséhez.

**Kérés**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Eredmények**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


A második példában egy összetettebb lekérdezés, amely több eredményt ad vissza a való csatlakozást.

**Kérés**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Eredmények**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Ha a lekérdezés eredményei nem férnek el az eredmények egyoldalas belül, akkor a REST API-t adja vissza a folytatási kód keresztül a `x-ms-continuation-token` válaszfejlécet. Az ügyfelek által a további eredmények együtt is megjelenítheti az eredményeket. Laponként eredmények száma is szabályozható a `x-ms-max-item-count` számú fejléc. Ha a megadott lekérdezés tartalmaz egy összesítő függvényt, például `COUNT`, akkor a lekérdezés lap egy részben összesített értéket adhat vissza a lap az eredmények. Az ügyfelek végre kell hajtania a második szintű összesítő ezekkel az eredményekkel, a végső eredményeket, például, a számát, az eredmény abban az egyes lapok a számuk keresztül összeg keresztül.

A lekérdezések adatok konzisztencia házirend kezeléséhez használja a `x-ms-consistency-level` például minden REST API-kérés fejlécének. A munkamenet-konzisztencia esetén szükséges a legutóbbi is echo `x-ms-session-token` Cookie-fejlécének a lekérdezési kérelemben. A lekérdezett gyűjtemény indexelési házirendet is befolyásolhatják a lekérdezési eredmények konzisztencia. Az alapértelmezett házirend-beállítások indexelő, gyűjtemények a indexe mindig naprakész lesz a dokumentum tartalmát és lekérdezési eredmények felel meg a kiválasztott adatok konzisztencia. Ha az indexelési házirendet Lusta van enyhíteni, majd lekérdezések visszaadhatják a elavult eredmények. További információkért lásd: [Azure Cosmos DB Konzisztenciaszintek][consistency-levels].

Ha a konfigurált indexelési házirendet a gyűjtemény nem támogatja a megadott lekérdezés, az Azure Cosmos adatbázis-kiszolgálót 400 "hibás kérelem" adja vissza. A kivonatoló (egyenlő) keresések, és kifejezetten kizárja indexelő elérési út beállítva elérési utak tartomány lekérdezések esetében adja vissza. A `x-ms-documentdb-query-enable-scan` fejléc adható meg a lekérdezést, hogy vizsgálatot végezzen, ha nem érhető el index engedélyezéséhez.

Úgy, hogy a lekérdezés-végrehajtás részletes metrikák is ki `x-ms-documentdb-populatequerymetrics` fejlécének `True`. További információkért lásd: [Azure Cosmos DB DocumentDB API SQL-lekérdezés metrikáját](documentdb-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
A .NET SDK támogatja a LINQ és az SQL lekérdezése. A következő példa bemutatja, hogyan hajthat végre a rendszerben jelent meg a jelen dokumentum korábbi egyszerű szűrő lekérdezés.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Ez a minta összehasonlítja két tulajdonságainak egyenlőség minden a dokumentumban, és névtelen leképezések használja. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


A következő példa bemutatja illesztések, LINQ selectmany metódus használatával.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



A .NET-ügyfél automatikusan a lekérdezés eredményének a fentiek szerint foreach blokkok oldalain telepítéseket. A REST API szakaszában bemutatott lekérdezési lehetőségek is elérhetők a .NET SDK használatával a `FeedOptions` és `FeedResponse` osztályok CreateDocumentQuery metódus. A lapok száma vezérelhető a `MaxItemCount` beállítást. 

Lapozófájl létrehozásával közvetlenül is szabályozhatja `IDocumentQueryable` használatával a `IQueryable` objektumot, majd ehhez beolvassa a` ResponseContinuationToken` gépet értékeket, és átadja őket `RequestContinuationToken` a `FeedOptions`. `EnableScanInQuery`vizsgálatok engedélyezésére, amikor a lekérdezés nem támogatja a konfigurált indexelési házirend állítható be. A particionált gyűjtemények használhatják `PartitionKey` futtatásához a lekérdezés egyetlen partícióazonosító (bár a Cosmos DB is automatikusan kinyerése Ez a lekérdezés szövegének), és `EnableCrossPartitionQuery` esetleg több partíciót kell futtatni a lekérdezések futtatásához. 

Tekintse meg [Azure Cosmos DB .NET minták](https://github.com/Azure/azure-documentdb-net) további mintákat tartalmazó lekérdezések. 

### <a id="JavaScriptServerSideApi"></a>JavaScript kiszolgálóoldali API
A cosmos DB programozási modellt biztosít a feldolgozás alatt álló alapú JavaScript-alkalmazáslogika közvetlenül a gyűjtemények, tárolt eljárások és eseményindítók. A JavaScript-logika regisztrálva, a gyűjtemény szintjén majd adhat ki az adott gyűjteményben lévő dokumentumokon működésének Helyadatbázis-műveletekhez. Ezek a műveletek a környezeti ACID-tranzakciókat van burkolva.

A következő példa bemutatja, hogyan lehet a JavaScript-kiszolgáló API a queryDocuments segítségével ellenőrizze a lekérdezések belső tárolt eljárások és eseményindítók.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Hivatkozások
1. [Bevezetés az Azure Cosmos DB][introduction]
2. [Az Azure Cosmos adatbázis SQL-specifikációja](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Azure Cosmos DB .NET-minták](https://github.com/Azure/azure-documentdb-net)
4. [Az Azure Cosmos DB Konzisztenciaszintek][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. JavaScript-specifikáció [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Lekérdezés kiértékelése technikák nagy adatbázisokhoz [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. A lekérdezés feldolgozás alatt álló párhuzamos relációs adatbázis-rendszerek, IEEE számítógép társadalom nyomja le az 1994.
11. Lu, Ooi, Tan, feldolgozás alatt álló párhuzamos relációs adatbázis-rendszerek, IEEE számítógép társadalom nyomja le az 1994 lekérdezés.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: a Pig Latin: egy nem, külső nyelvi SIGMOD 2008 az adatok feldolgozásához.
13. G. Graefe. Optimalizálás kaszkádokban keretében. IEEE adatok Eng. BULL., 18(3): 1995.

[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md