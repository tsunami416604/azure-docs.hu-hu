---
title: Modell- és partidált adatok az Azure Cosmos DB-n egy valós példával
description: Ismerje meg, hogyan modellezése és particionálása valós példa az Azure Cosmos DB Core API használatával
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445380"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Adatok modellezése és particionálása az Azure Cosmos DB-ben való életből vett példa használatával

Ez a cikk számos Azure Cosmos DB-koncepcióra épül, például [adatmodellezésre,](modeling-data.md) [particionálásra](partitioning-overview.md)és [kiosztott átviteli értékre,](request-units.md) hogy bemutassa, hogyan kezelhető egy valós adattervezési gyakorlat.

Ha általában relációs adatbázisokkal dolgozik, valószínűleg olyan szokásokat és intuíciókat épített ki, amelyek segítségével hogyan tervezhet egy adatmodellt. A speciális korlátok, valamint az Azure Cosmos DB egyedi erősségei miatt a legtöbb ajánlott eljárás nem fordítható le jól, és az optimálistól elmaradó megoldásokba vonhatja. Ez a cikk célja, hogy végigvezeti a teljes folyamat egy valós használati eset az Azure Cosmos DB, az elemmodellezés entitás közös hely és a tároló particionálás a teljes folyamat modellezése egy valós használati eset az Azure Cosmos DB-n, az elemmodellezés entitás közös hely és a tároló particionálás.

## <a name="the-scenario"></a>A forgatókönyv

Ehhez a gyakorlathoz, fogunk, hogy fontolja meg a domain egy blogging platform, ahol a *felhasználók* *létrehozhatnak álláshely*. A felhasználók is *tetszik,* és *megjegyzéseket* fűznek ezekhez a bejegyzésekhez.

> [!TIP]
> Kiemeltünk néhány *dőlt betűs*szót; ezek a szavak azonosítják azokat a "dolgokat", amelyeket a modellünknek manipulálnia kell.

További követelmények hozzáadása a specifikációnkhoz:

- A címlapon a legutóbb létrehozott bejegyzések hírcsatornáját jeleníti meg,
- Tudjuk letölteni az összes álláshely a felhasználó, az összes hozzászólás egy hozzászólást, és minden szereti a post,
- Hozzászólások vissza a felhasználónevét a szerzők és a gróf, hogy hány megjegyzéseket és tetszik van,
- Hozzászólások és tetszik is vissza a felhasználónevét a felhasználók, akik létrehozták őket,
- Ha listákként jelenik meg, a bejegyzéseknek csak a tartalmuk csonka összegzését kell bemutatniuk.

## <a name="identify-the-main-access-patterns"></a>A fő hozzáférési minták azonosítása

Először is, adunk néhány szerkezetet a kezdeti specifikáció azonosításával megoldásunk hozzáférési minták. Az Azure Cosmos DB adatmodelljének tervezésekor fontos megérteni, hogy a modellünknek mely kéréseket kell szolgálnia annak érdekében, hogy a modell hatékonyan szolgálja ki ezeket a kéréseket.

Annak érdekében, hogy a teljes folyamat könnyebben követhető legyen, ezeket a különböző kéréseket parancsokként vagy lekérdezésként kategorizáljuk, és a [CQRS-től](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) kölcsönveszünk néhány szókincset, ahol a parancsok írási kérelmek (azaz a rendszer frissítésének szándékai) és a lekérdezések csak olvasható kérelmek.

Itt van a kérelmek listáját, hogy a platform lesz, hogy ki:

- **[C1]-ig kell leaktni.** Felhasználó létrehozása/szerkesztése
- **-1.** Felhasználó beolvasása
- **[C2]-ig kell leaktni.** Bejegyzés létrehozása/szerkesztése
- **[2. kérdés]** Bejegyzés lekérése
- **-1, hogy ne mondjam el.** A felhasználó bejegyzéseinek listázása rövid formában
- **[C3]-ig** Megjegyzés létrehozása
- **-1, hogy ne mondjam el.** Bejegyzés megjegyzéseinek listázása
- **[C4]-es.** Bejegyzés kedvelése
- **-1, hogy ne mondjam el.** Bejegyzés kedvelésének listázása
- **-1, hogy ne mondjam el.** Sorolja fel az *x* legutóbbi hozzászólások létre rövid formában (feed)

Mivel ebben a szakaszban, még nem gondoltam a részleteket, amit az egyes entitások (felhasználó, posta stb) tartalmazni fogja. Ez a lépés általában az elsők között kell kezelni, amikor egy relációs üzlettel szemben terveznek, mert ki kell találnunk, hogy ezek az entitások hogyan fognak lefordítani táblázatok, oszlopok, idegen kulcsok stb. Ez sokkal kevésbé aggodalomra ad okot a dokumentum-adatbázis, amely nem kényszeríti ki a séma íráskor.

A fő ok, amiért fontos, hogy azonosítsa a hozzáférési minták a kezdetektől fogva, mert ez a kérelmek listáját lesz a mi teszt suite. Minden alkalommal, amikor az adatmodellünkön haladunk, végigmegyünk az egyes kéréseken, és ellenőrizzük azok teljesítményét és méretezhetőségét.

## <a name="v1-a-first-version"></a>V1: Az első verzió

Kezdjük két tartályban: `users` és `posts`.

### <a name="users-container"></a>Felhasználók tárolója

Ez a tároló csak felhasználói elemeket tárol:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Ezt a tárolót a , azaz `id`azt jelenti, hogy a tárolón belüli minden logikai partíció csak egy elemet tartalmaz.

### <a name="posts-container"></a>Bejegyzések tárolója

Ez a tároló hozzászólásokat, megjegyzéseket és kedveléseket tárol:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Mi partíció ezt `postId`a tárolót , ami azt jelenti, hogy minden logikai partíció takar, hogy a tároló tartalmaz egy hozzászólást, az összes megjegyzést, hogy a post, és az összes szereti az adott post.

Vegye figyelembe, hogy `type` a tárolóban tárolt elemekben bevezettünk egy tulajdonságot, hogy különbséget tegyünk a tároló által üzemeltetett három entitástípus között.

Továbbá úgy döntöttünk, hogy a kapcsolódó adatokra hivatkozunk a beágyazás helyett [(ellenőrizze ezt a szakaszt](modeling-data.md) a fogalmak részleteiért), mert:

- nincs felső határa, hogy hány hozzászólás a felhasználó hozhat létre,
- a bejegyzések önkényesen hosszúak lehetnek,
- nincs felső határa, hogy hány hozzászólás, és szereti a post lehet,
- azt akarjuk, hogy képes legyen hozzá egy megjegyzést, vagy hasonló egy post anélkül, hogy frissítse a post is.

## <a name="how-well-does-our-model-perform"></a>Milyen jól teljesít a modellünk?

Itt az ideje, hogy felmérjük az első verzió teljesítményét és méretezhetőségét. A korábban azonosított kérelmek mindegyikében mérjük a késést, és hogy hány kérelemegységet használ fel. Ez a mérés egy 100 000 felhasználót tartalmazó hamis adathalmazellen történik, felhasználónként 5–50 bejegyzéssel, és legfeljebb 25 hozzászólással és bejegyzésenként 100 kedveléssel.

### <a name="c1-createedit-a-user"></a>[C1]-ig kell leaktni. Felhasználó létrehozása/szerkesztése

Ez a kérés egyszerű végrehajtani, mint mi csak `users` hozzon létre vagy frissítsen egy elemet a tárolóban. A kérelmek szépen elosztva az `id` összes partíciót köszönhetően a partíció gombot.

![Egyetlen elem írása a felhasználók tárolójára](./media/how-to-model-partition-example/V1-C1.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 ezres | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>-1. Felhasználó beolvasása

A felhasználó beolvasása a megfelelő elem `users` tárolóból történő olvasásával történik.

![Egyetlen elem beolvasása a felhasználók tárolójából](./media/how-to-model-partition-example/V1-Q1.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 ezres | 1 KT | ✅ |

### <a name="c2-createedit-a-post"></a>[C2]-ig kell leaktni. Bejegyzés létrehozása/szerkesztése

Hasonlóan **a [C1]**, mi csak `posts` meg kell írni, hogy a tartályba.

![Egyetlen elem írása a bejegyzések tárolóba](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ezres | 8,76 Ru | ✅ |

### <a name="q2-retrieve-a-post"></a>[2. kérdés] Bejegyzés lekérése

Kezdjük azzal, hogy lekéri `posts` a megfelelő dokumentumot a tartályból. De ez nem elég, mint egy a specifikáció is van, hogy összesítse a felhasználónevét a post szerzője és a számít, hogy hány megjegyzést, és hány szereti ezt a hozzászólást, amely előírja, 3 további SQL lekérdezéseket kell kiadni.

![Bejegyzés beolvasása és további adatok összesítése](./media/how-to-model-partition-example/V1-Q2.png)

A további lekérdezések mindegyike szűri a partíciós kulcsot a megfelelő tároló, amely pontosan mit akarunk maximalizálni a teljesítményt és a méretezhetőséget. De végül négy műveletet kell végrehajtanunk, hogy egyetlen bejegyzést visszaadhassunk, így ezt a következő ismétlésben javítjuk.

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ezres | 19.54 Ru | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>-1, hogy ne mondjam el. A felhasználó bejegyzéseinek listázása rövid formában

Először is, meg kell letölteni a kívánt álláshely egy SQL lekérdezés, amely lekéri a hozzászólások megfelelő adott felhasználó. De további lekérdezéseket is ki kell adnunk a szerző felhasználónevének és a megjegyzések és kedvelések számának összesítéséhez.

![A felhasználó összes bejegyzésének beolvasása és a további adatok összesítése](./media/how-to-model-partition-example/V1-Q3.png)

Ez a megvalósítás számos hátrányt jelent:

- a hozzászólások és kedvelések számát összesítő lekérdezéseket minden, az első lekérdezés által visszaküldött bejegyzésre ki kell adni,
- a fő lekérdezés nem szűri a `posts` tároló partíciókulcsát, ami egy ventilátor-out és egy partíció scan keresztül a tárolót.

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 130 ms | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3]-ig Megjegyzés létrehozása

A megjegyzés a megfelelő elem `posts` tárolóba írásával jön létre.

![Egyetlen elem írása a bejegyzések tárolóba](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 ezres | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>-1, hogy ne mondjam el. Bejegyzés megjegyzéseinek listázása

Kezdjük a lekérdezést, hogy lekéri az összes megjegyzést, hogy a post, és még egyszer, azt is meg kell összesíteni felhasználónevek külön-külön minden megjegyzést.

![A bejegyzéshez fűzött összes megjegyzés beolvasása és a további adatok összesítése](./media/how-to-model-partition-example/V1-Q4.png)

Bár a fő lekérdezés szűri a tároló partíciókulcsát, a felhasználónevek külön-külön összesítése bünteti az általános teljesítményt. Majd később javítunk rajta.

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 23 ezres | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4]-es. Bejegyzés kedvelése

Csakúgy, mint **a [C3]**, `posts` hozzuk létre a megfelelő elemet a tartályban.

![Egyetlen elem írása a bejegyzések tárolóba](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 6 ezres | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>-1, hogy ne mondjam el. Bejegyzés kedvelésének listázása

Csakúgy, mint **a [Q4],** lekérdezzük a kedvelőket az adott bejegyzéshez, majd összesítjük a felhasználónevüket.

![Az összes kedvelés beolvasása egy bejegyzéshez és a további adatok összesítése](./media/how-to-model-partition-example/V1-Q5.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 59 ms | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>-1, hogy ne mondjam el. Sorolja fel az x legutóbbi hozzászólások létre rövid formában (feed)

A legutóbbi bejegyzéseket úgy használjuk le, hogy lekérdezzük a tárolót csökkenő `posts` létrehozási dátum szerint rendezve, majd összesítjük a felhasználóneveket és a megjegyzések és kedvelések számát az egyes bejegyzésekhez.

![A legutóbbi bejegyzések beolvasása és további adataik összesítése](./media/how-to-model-partition-example/V1-Q6.png)

Még egyszer, a kezdeti lekérdezés nem szűri `posts` a partíciós kulcsot a tároló, amely elindítja a költséges fan-out. Ez még rosszabb, mivel sokkal nagyobb eredményhalmazt célozunk meg, és az eredményeket egy `ORDER BY` záradékkal rendezzük, ami drágábbá teszi a kérési egységek tekintetében.

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>A V1 teljesítményének tükrözése

Az előző szakaszban tapasztalt teljesítményproblémákat tekintve két fő problémaosztályt tudunk azonosítani:

- egyes kérések több lekérdezés kiadását igénylik annak érdekében, hogy összegyűjtsük a visszaküldéshez szükséges összes adatot,
- Egyes lekérdezések nem szűrik a megcélzott tárolók partíciókulcsát, ami egy olyan ventilátorhoz vezet, amely akadályozza a méretezhetőséget.

Oldjuk meg ezeket a problémákat, kezdve az elsővel.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: A denormalizáció bevezetése az olvasási lekérdezések optimalizálásához

Az ok, amiért bizonyos esetekben további kéréseket kell kiadnunk, az az, hogy az eredeti kérelem eredményei nem tartalmazzák az összes visszaküldéshez szükséges adatot. Ha nem relációs adattár, például az Azure Cosmos DB használatával dolgozik, ezt a problémát általában az adatkészletük ben lévő adatok denormalizálásával oldják meg.

A példánkban módosítjuk a bejegyzéselemeit, hogy hozzáadjuk a bejegyzés szerzőjének felhasználónevét, a hozzászólások számát és a kedvelések számát:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Azt is módosíthatja megjegyzést, és mint az elemeket, hogy adjunk a felhasználó nevét a felhasználó, aki létrehozta őket:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing comment és hasonló számít

Mit akarunk elérni, hogy minden alkalommal, amikor hozzá egy megjegyzést, `commentCount` vagy `likeCount` hasonló, mi is növekszik a vagy a megfelelő post. Mivel `posts` a tároló particionált `postId`, az új elem (megjegyzés vagy hasonló) és a megfelelő post ülni ugyanabban a logikai partíción. Ennek eredményeképpen a művelet végrehajtásához [használhatunk egy tárolt eljárást.](stored-procedures-triggers-udfs.md)

Most, amikor létrehoz egy megjegyzést (**[C3]**), `posts` ahelyett, hogy csak egy új elemet a tartályba, a következő tárolt eljárást hívjuk a tárolón:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Ez a tárolt eljárás a bejegyzés azonosítóját és az új megjegyzés törzsét paraméterként veszi át, majd:

- letölti a post
- növekmények a`commentCount`
- helyettesíti a post
- hozzáadja az új megjegyzést

Mivel a tárolt eljárások atlétatranzakciókként kerülnek végrehajtásra, garantált, hogy a megjegyzések értéke `commentCount` és tényleges száma mindig szinkronban marad.

Nyilvánvalóan hasonló tárolt eljárást hívunk, amikor új kedvelőket adhozzá a `likeCount`.

### <a name="denormalizing-usernames"></a>Felhasználónevek denormalizálása

A felhasználónevek más megközelítést igényelnek, mivel a felhasználók nem csak különböző partíciókban, hanem egy másik tárolóban is ülnek. Amikor denormalizálnunk kell az adatokat a partíciók és tárolók között, használhatjuk a forrástároló [módosítási hírcsatornáját.](change-feed.md)

A példánkban a tároló módosítási hírcsatornáját használjuk, hogy reagáljunk, amikor a `users` felhasználók frissítik a felhasználónevüket. Ha ez megtörténik, a módosítást egy másik tárolt `posts` eljárás hívásával terjesztjük a tárolón:

![Denormalizing felhasználónevek a hozzászólások konténer](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Ez a tárolt eljárás a felhasználó azonosítóját és a felhasználó új felhasználónevét veszi paraméterekként, majd:

- letölti az `userId` összes elem megfelelő (ami lehet hozzászólás, hozzászólás, vagy szereti)
- minden egyes ilyen tételhez
  - felváltja a`userUsername`
  - felülírja az elemet

> [!IMPORTANT]
> Ez a művelet költséges, mert megköveteli, hogy ezt a `posts` tárolt eljárást kell végrehajtani a tároló minden partícióján. Feltételezzük, hogy a legtöbb felhasználó a regisztráció során kiválaszt egy megfelelő felhasználónevet, és soha nem fogja megváltoztatni, így ez a frissítés nagyon ritkán fog futni.

## <a name="what-are-the-performance-gains-of-v2"></a>Mik a teljesítmény nyereség a V2?

### <a name="q2-retrieve-a-post"></a>[2. kérdés] Bejegyzés lekérése

Most, hogy a denormalizáció a helyén van, csak egy elemet kell lehívnunk, hogy kezeljük a kérést.

![Egyetlen elem beolvasása a bejegyzések tárolójából](./media/how-to-model-partition-example/V2-Q2.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 ezres | 1 KT | ✅ |

### <a name="q4-list-a-posts-comments"></a>-1, hogy ne mondjam el. Bejegyzés megjegyzéseinek listázása

Itt is megkímélhetjük a felhasználóneveket lekérő további kéréseket, és egyetlen lekérdezést kaphatunk, amely szűri a partíciókulcsot.

![A bejegyzéshez fűzött összes megjegyzés beolvasása](./media/how-to-model-partition-example/V2-Q4.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ezres | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>-1, hogy ne mondjam el. Bejegyzés kedvelésének listázása

Pontosan ugyanaz a helyzet, amikor felsorolja a szeret.

![Az összes kedvelés beolvasása egy bejegyzéshez](./media/how-to-model-partition-example/V2-Q5.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ezres | 8,92 Ru | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Annak biztosítása, hogy minden kérés méretezhető

Az általános teljesítménybeli javulásunkat tekintve még mindig van két kérés, amelyeket még nem optimalizáltunk teljesen: **[Q3]** és **[Q6]**. Ezek a lekérdezéseket tartalmazó lekérdezések, amelyek nem szűrik a partíciós kulcsot a megcélzott tárolók.

### <a name="q3-list-a-users-posts-in-short-form"></a>-1, hogy ne mondjam el. A felhasználó bejegyzéseinek listázása rövid formában

Ez a kérés már a V2-ben bevezetett fejlesztések előnyeit élvezi, amely megkíméli a további lekérdezéseket.

![A felhasználó összes bejegyzésének beolvasása](./media/how-to-model-partition-example/V2-Q3.png)

De a fennmaradó lekérdezés továbbra sem szűri `posts` a tároló partíciókulcsát.

A mód, hogy gondolkodni ezt a helyzetet valójában egyszerű:

1. Ez *a* kérés kell `userId` szűrni a, mert azt akarjuk, hogy letölteni az összes álláshely egy adott felhasználó
1. Nem működik jól, mert a `posts` tárolóval szemben kerül végrehajtásra, amelyet nem particionál`userId`
1. A nyilvánvalót kiadva, a teljesítményproblémát úgy oldanánk meg, hogy ezt a kérést egy olyan tárolóval hajtjuk végre, amelyet *a*`userId`
1. Kiderül, hogy már van egy ilyen `users` tartályunk: a tartály!

Ezért bevezetjük a denormalizáció második szintjét azáltal, `users` hogy megkettőzzük a teljes oszlopokat a tartályba. Ezzel, hogy mi hatékonyan kap egy másolatot a mi álláshely, csak particionált `userId`mentén egy másik dimenziók, így így hatékonyabb letölteni a .

A `users` tároló most már kétféle elemet tartalmaz:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Vegye figyelembe:

- bevezettünk egy `type` mezőt a felhasználói elemben, hogy megkülönböztessük a felhasználókat a bejegyzésektől,
- van is hozzá `userId` egy mezőt a felhasználói elem, `id` amely redundáns a területen, de szükség van, mint a `users` tároló most particionált `userId` (és nem, `id` mint korábban)

Ennek a denormalizációnak az eléréséhez ismét használjuk a változás-hírcsatornát. Ezúttal reagálunk a `posts` tároló módosítási hírcsatornájára, hogy minden `users` új vagy frissített bejegyzést küldjünk a tárolóba. És mivel a bejegyzések listázása nem igényli a teljes tartalmuk visszaküldését, csonkolhatjuk őket a folyamatban.

![Denormalizing álláshely a felhasználók konténer](./media/how-to-model-partition-example/denormalization-2.png)

Most már átirányíthatjuk `users` a lekérdezést a tárolóba, a tároló partíciókulcsának szűrésére.

![A felhasználó összes bejegyzésének beolvasása](./media/how-to-model-partition-example/V3-Q3.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ezres | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>-1, hogy ne mondjam el. Sorolja fel az x legutóbbi hozzászólások létre rövid formában (feed)

Hasonló helyzettel kell foglalkoznunk: még a V2-ben bevezetett denormalizáció által szükségtelennek hagyott további lekérdezések megkímélése után sem szűri a fennmaradó lekérdezést a tároló partíciókulcsára:

![A legutóbbi bejegyzések beolvasása](./media/how-to-model-partition-example/V2-Q6.png)

Ugyanezt a megközelítést követve a kérelem teljesítményének és méretezhetőségének maximalizálása megköveteli, hogy csak egy partíciót. Ez azért elképzelhető, mert csak korlátozott számú tételt kell visszaküldenünk; annak érdekében, hogy feltöltsük a blogolás platform honlapján, mi csak meg kell, hogy a 100 legújabb álláshely, anélkül, hogy paginate a teljes adatkészletet.

Tehát, hogy optimalizálja ezt az utolsó kérést, bevezetünk egy harmadik tartályt a tervünkbe, amely teljes mértékben a kérés kiszolgálására szolgál. Mi denormalize állásaink, hogy az új `feed` konténer:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Ezt a tárolót `type`a , `post` a , amely mindig a mi elemeket. Ennek során biztosítja, hogy a tárolóban lévő összes elem ugyanabban a partícióban fog ülni.

A denormalizáció elérése érdekében csak be kell akasznunk a korábban bevezetett változás-hírcsatorna-folyamatra, hogy az új tárolóba küldjük az álláshelyeket. Az egyik fontos dolog, hogy tartsa szem előtt, hogy meg kell győződnie arról, hogy csak tárolja a 100 legutóbbi álláshely; ellenkező esetben a tároló tartalma a partíció maximális méretét meghaladóra nőhet. Ez úgy történik, hogy minden alkalommal meghívja az [eseményindítót,](stored-procedures-triggers-udfs.md#triggers) amikor egy dokumentumot hozzáadanak a tárolóba:

![Denormalizáló oszlopok a takarmánytárolóba](./media/how-to-model-partition-example/denormalization-3.png)

Itt van a test a post-trigger, hogy csonkolja a gyűjtemény:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Az utolsó lépés az, hogy átirányítsa a lekérdezést az új `feed` tároló:

![A legutóbbi bejegyzések beolvasása](./media/how-to-model-partition-example/V3-Q6.png)

| **Késés** | **VT díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ezres | 16.97 Ru | ✅ |

## <a name="conclusion"></a>Összegzés

Vessünk egy pillantást az általános teljesítmény és méretezhetőség fejlesztések általunk bevezetett különböző változatai a design.

| | 1. verzió | 2. verzió | V3 |
| --- | --- | --- | --- |
| **[C1]-ig kell leaktni.** | 7 ms / 5,71 RU | 7 ms / 5,71 RU | 7 ms / 5,71 RU |
| **-1.** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]-ig kell leaktni.** | 9 ms / 8,76 RU | 9 ms / 8,76 RU | 9 ms / 8,76 RU |
| **[2. kérdés]** | 9 ms / 19,54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **-1, hogy ne mondjam el.** | 130 ms / 619,41 RU | 28 ms / 201,54 RU | 4 ms / 6,46 RU |
| **[C3]-ig** | 7 ms / 8,57 RU | 7 ms / 15,27 RU | 7 ms / 15,27 RU |
| **-1, hogy ne mondjam el.** | 23 ms / 27,72 RU | 4 ms / 7,72 RU | 4 ms / 7,72 RU |
| **[C4]-es.** | 6 ms / 7,05 RU | 7 ms / 14,67 RU | 7 ms / 14,67 RU |
| **-1, hogy ne mondjam el.** | 59 ms / 58,92 RU | 4 ms / 8,92 RU | 4 ms / 8,92 RU |
| **-1, hogy ne mondjam el.** | 306 ms / 2063,54 RU | 83 ms / 532,33 RU | 9 ms / 16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Optimalizáltunk egy olvasási nehéz forgatókönyvet

Lehet, hogy észrevette, hogy mi koncentrált erőfeszítéseinket teljesítményének javítása érdekében az olvasási kérelmek (lekérdezések) rovására írási kérelmek (parancsok). Sok esetben az írási műveletek mostantól a módosítási hírcsatornákon keresztül imitálni fogják a későbbi denormalizációt, ami számításilag drágábbá és hosszabb rakoncátaskodóvá teszi őket.

Ezt az indokolja, hogy a blogolási platform (mint a legtöbb közösségi alkalmazás) olvasható, ami azt jelenti, hogy az olvasási kérelmek mennyisége általában nagyságrendekkel magasabb, mint az írási kérelmek mennyisége. Így van értelme, hogy írási kérelmek drágább végrehajtani annak érdekében, hogy hagyja olvasási kérelmek olcsóbb és jobban teljesítő.

Ha megnézzük a legszélsőségesebb optimalizálástettünk, **[Q6]** ment 2000 + RUs-ból csak 17 RUS; elértük, hogy a denormalizing álláshelyek költsége mintegy 10 RT-k tételenként. Mivel sokkal több takarmánykérést szolgálnánk ki, mint az álláshelyek létrehozása vagy frissítése, a denormalizáció költsége elhanyagolható, figyelembe véve a teljes megtakarítást.

### <a name="denormalization-can-be-applied-incrementally"></a>A denormalizáció fokozatosan alkalmazható

A cikkben megvizsgált méretezhetőségi fejlesztések az adatkészlet en keresztüli denormalizációt és adatmásolást foglalnak magukban. Meg kell jegyezni, hogy ezeket az optimalizációkat nem kell bevezetni az első napon. A partíciókulcsokra szűrő lekérdezések nagyobb léptékben jobban teljesítenek, de a partíciók közötti lekérdezések teljesen elfogadhatók lehetnek, ha ritkán vagy korlátozott adatkészlettel vannak megnevezve. Ha csak egy prototípust épít, vagy egy kis és ellenőrzött felhasználói bázissal rendelkező terméket indít el, akkor valószínűleg megkímélheti ezeket a fejlesztéseket későbbre; ami fontos, akkor az, hogy [figyelemmel kíséri](use-metrics.md) a modell teljesítményét, így eldöntheti, ha és amikor itt az ideje, hogy őket.

A módosítási hírcsatorna, amelyet a frissítések más tárolókba való terjesztésére használunk, folyamatosan tárolja ezeket a frissítéseket. Ez lehetővé teszi, hogy kérje az összes frissítést létrehozása óta a tároló és bootstrap denormalized nézetek, mint egy egyszeri felzárkózási művelet akkor is, ha a rendszer már sok adatot.

## <a name="next-steps"></a>További lépések

A gyakorlati adatmodellezés és -particionálás bevezetése után érdemes lehet a következő cikkeket ellenőrizni az általunk tárgyalt fogalmak áttekintéséhez:

- [Adatbázisok, tárolók és elemek használata](databases-containers-items.md)
- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Csatorna módosítása az Azure Cosmos DB-ben](change-feed.md)
