---
title: Modell és a partíció adatainak az Azure Cosmos DB használatával egy való életből vett példa
description: Ismerje meg, hogyan modellezheti, és a egy való életből vett példa az Azure Cosmos DB Core API használatával a partíció
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 3/27/2019
ms.author: thweiss
ms.openlocfilehash: ac1b94de4b439aab202d53b23b0d0da616a9f851
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58919615"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Modell és a partíció adatainak az Azure Cosmos DB használatával egy való életből vett példa

Ez a cikk épül, amely több Azure Cosmos DB fogalmakat, mint [adatmodellezés](modeling-data.md), [particionálás](partitioning-overview.md), és [kiosztott átviteli sebesség](request-units.md) azzal, hogy egy való életből vett milyen bemutatása adatok tervezése a gyakorlatban.

Ha Ön általában működik együtt a relációs adatbázisok, valószínűleg létrehozott szokásait és a egy olyan modell tervezéséhez intuitions. Egyedi korlátait, de is az Azure Cosmos DB egyedi előnyeit, mert az ajánlott eljárások a legtöbb jól fordítása nem, és előfordulhat, hogy húzza, optimálisnál megoldásokban. Ez a cikk célja, végigvezeti egy való életből vett használati modellezés az Azure Cosmos DB, az entitás a közös elhelyezés és a tároló particionálás modellezési elem teljes folyamatán.

## <a name="the-scenario"></a>A forgatókönyv

Ehhez a gyakorlathoz fogjuk fontolja meg egy blogplatform, amely a tartomány ahol *felhasználók* hozhat létre *bejegyzések*. A felhasználók is *például* , és adja hozzá *megjegyzések* , ezek a bejegyzések.

> [!TIP]
> Az egyes szavak rendelkezik kihirdettük *dőlt*; ezek a szavak azonosíthatja a modell módosítására lesz "dolgokat" típusú.

További követelmények hozzáadásával a specifikációnak:

- Első lap megjeleníti a legutóbb létrehozott bejegyzések hírcsatornában
- Azt is minden bejegyzés egy felhasználó, egy bejegyzés az összes megjegyzést, és a egy bejegyzés az összes kedvelések beolvasása
- A felhasználónév, a szerzők és a egy száma, hogy hány megjegyzések és kedvelések rendelkeznek, visszaadott bejegyzések
- Megjegyzések és kedvelések is küld vissza a rendszer a felhasználónév, aki létrehozta őket, a felhasználók
- Amikor az megjelenik, listák, bejegyzések csak tartalmak csonkolt összesítését kell.

## <a name="identify-the-main-access-patterns"></a>A fő hozzáférési minták azonosítása

Indításához ad szerkezet a kezdeti specifikációnak a azonosításával a megoldás adathozzáférési mintáknak megfelelően. Adatmodell az Azure Cosmos DB tervezésekor fontos tudni, hogy mely kérelmek kiszolgálására, győződjön meg arról, hogy a modell erre a célra ezeket a kérelmeket hatékonyan, a modell lesz.

Ahhoz, hogy a teljes folyamat könnyebb követhetősége érdekében, hogy kategorizálására ezeket a különböző kérelmeket parancsok vagy a lekérdezések, néhány Szójegyzék a hitelnyújtási [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) amelyeknél a lemezírási kérések (azt jelenti, a rendszer frissítéséhez leképezés) parancsok és lekérdezések csak olvasási kérelmek.

Íme, amelyre platformunk elérhetővé kérelmek listája:

- **[C1]**  Egy felhasználó létrehozása/szerkesztése
- **[1.]**  Felhasználó beolvasása
- **[C2]**  Bejegyzés létrehozása/szerkesztése
- **[V2]**  Hozzászólás lekéréséhez
- **[3. NEGYEDÉVI]**  Rövid képernyőn a felhasználó bejegyzések listája
- **[C3]**  Megjegyzés létrehozása
- **[4]**  Egy hozzászólást megjegyzések listázása
- **[C4]**  , Például egy post
- **[5. KÉRDÉS]**  Egy hozzászólást kedvelések listázása
- **6. [KÉRDÉS]**  Lista a *x* legutóbbi bejegyzések létrehozott rövid űrlapot (adatcsatorna)

Mint ebben a szakaszban Gondoltuk még nem talál, mi minden entitás (felhasználó, post stb.) tartalmazhat. Ezt a lépést általában az első megoldandó relációs tárolókon tervezésekor, mert rendelkezünk, döntse el, hogyan fogja fordítani az ezekről az entitásokról táblákat, oszlopokat, a külső kulcsok stb melyiket között van. Fontos szempont a dokumentum-adatbázis, amelyeket nem semmilyen sémát, írási sokkal rövidebb.

A fő oka, hogy miért fontos az elejétől a hozzáférési minták azonosításához az oka, hogy a kérések listáját a tesztsorozatban topológiában. Minden alkalommal, amikor azt ciklustevékenység az adatmodellbe, azt fogja egyes, a kérelmek, és ellenőrizze azok teljesítményét és méretezhetőségét.

## <a name="v1-a-first-version"></a>V1: Egy első verzió

A két tárolókkal kezdődik: `users` és `posts`.

### <a name="users-container"></a>Felhasználók tároló

Ez a tároló csak tárolja a felhasználói elemeket:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Azt a tároló által particionálása `id`, ami azt jelenti, hogy egyes logikai partíciók azon belül csak egy elemet fogja tartalmazni.

### <a name="posts-container"></a>Bejegyzések tároló

Ez a tároló bejegyzések, hozzászólások, üzemelteti, és a kedvelések:

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

Azt a tároló által particionálása `postId`, ami azt jelenti, hogy egyes logikai partíciók azon belül egy POST-, a bejegyzés véleményezésével a megjegyzések és a bejegyzés véleményezésével összes kedvelések tartalmazza.

Vegye figyelembe, hogy bevezettük a `type` , hogy ez a három típusú entitások közötti különbséget tenni a tárolóban tárolt elemek tulajdonság tárológazdagép.

Ezenkívül választottuk a beágyazás helyett kapcsolódó adatokra hivatkoznak, (tekintse [ebben a szakaszban](modeling-data.md) részletes ezekkel a fogalmakkal) mert:

- a felhasználó létrehozhat, hogy hány bejegyzések nincs felső korlátja
- bejegyzések lehet önkényesen hosszú,
- hány megjegyzések és kedvelések bejegyzés is rendelkezik, nincs felső korlátja
- szeretnénk hozzá megjegyzést, vagy egy hasonló szeretne egy hozzászólásra magát a bejegyzés frissítése nélkül.

## <a name="how-well-does-our-model-perform"></a>Arról, hogy nem lehet végrehajtani a modell?

Azt már időt annak ellenőrzéséhez, teljesítményét és méretezhetőségét, az első verziója. Minden, a korábban azonosított kérelmek, a késése és hány kérés mérjük egységek fogyaszt. Ez a mérték az 5 – 50 bejegyzések felhasználónként, és akár 25 megjegyzések és 100 kedvelések bejegyzésenkénti 100 000 felhasználót tartalmazó helyőrző adatkészlet alapján történik.

### <a name="c1-createedit-a-user"></a>[C1] A felhasználó létrehozása/szerkesztése

A kérelem nagyon egyszerű megvalósításához, azt csak létrehozása vagy frissítése egy elemet a `users` tároló. A kérések szépen köszönhetően minden partíció lesz elosztva a `id` partíciókulcsot.

![A felhasználók tárolót egyetlen elem írása](./media/how-to-model-partition-example/V1-C1.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[1.] Felhasználó beolvasása

Felhasználó beolvasása végzi el a megfelelő elemet az olvasó a `users` tároló.

![A felhasználók tárolóból egy elem beolvasása](./media/how-to-model-partition-example/V1-Q1.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Bejegyzés létrehozása/szerkesztése

Hasonlóan **[C1]**, csak rá kell írni a `posts` tároló.

![A bejegyzések tárolót egyetlen elem írása](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[V2] Egy hozzászólás lekéréséhez

A megfelelő dokumentumból lekérésével kezdődik a `posts` tároló. Azonban ez nem elegendő, a specifikációnak megfelelően is kell, hogy a bejegyzés szerzője felhasználóneve összesített, és hány hozzászólások számát és a kedvelések számát ebben a bejegyzésben rendelkezik, amelyhez szükséges 3 további SQL-lekérdezések katalóguskeresést kell végezni.

![Bejegyzés beolvasása és a további adatok összevonása](./media/how-to-model-partition-example/V1-Q2.png)

Minden további lekérdezések a partíciókulcs a megfelelő tárolót, amely pontosan mit szeretnénk a teljesítmény és méretezhetőség, a szűrők. De idővel rendelkezünk, négy műveleteket való visszatéréshez az egyedi közzétételek, így azt fogjuk javítása, amely a következő verzió továbbfejlesztésében.

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[3. NEGYEDÉVI] Rövid képernyőn a felhasználó bejegyzések listája

Először azt kell beolvasni a kívánt SQL-lekérdezés, amely beolvassa a megfelelő bejegyzések az adott felhasználóra fűzött hozzászólásokat. De mi is a szerző felhasználónév és a hozzászólások számát összesítenie további lekérdezések kiadására rendelkezik, és kedvelések.

![Egy felhasználó minden bejegyzés beolvasása és a további adatok összevonása](./media/how-to-model-partition-example/V1-Q3.png)

Ez a megvalósítás sok hátrányai mutat be:

- a lekérdezések, megjegyzések és kedvelések számát összesítésével kötelező, hogy az egyes bejegyzések az első lekérdezés által visszaadott
- a fő lekérdezés szerinti szűrés partíciókulcsa a `posts` tárolót, a tároló között egy logikájával és a egy partíció vizsgálat vezető.

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Megjegyzés létrehozása

Hozzászólás írása a megfelelő elemet a hozza létre a `posts` tároló.

![A bejegyzések tárolót egyetlen elem írása](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[4] A post megjegyzések listázása

Kezdődik meg, amely beolvassa az összes megjegyzést a bejegyzés véleményezésével lekérdezéssel, és ismét is meg kell összesített felhasználónevek külön-külön az egyes megjegyzéseket.

![A hozzászólás összes megjegyzések lekérdezésekor és a további adatok összevonása](./media/how-to-model-partition-example/V1-Q4.png)

Bár a fő lekérdezés szűrése a partíciókulcs a tárolót, külön-külön összevonása a felhasználónevei penalizes általános teljesítménye. Lesz azzal, hogy később.

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Például egy post

Akárcsak a **[C3]**, hozunk létre a megfelelő elemet a `posts` tároló.

![A bejegyzések tárolót egyetlen elem írása](./media/how-to-model-partition-example/V1-C2.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[5. KÉRDÉS] A post kedvelések listázása

Akárcsak a **[4]**, azt, hogy a post esetében a kedvelések lekérdezése, majd összesíti a felhasználónevek egyeztetéséhez.

![Lekéri az összes kedveli a hozzászólás, és a további adatok összevonása](./media/how-to-model-partition-example/V1-Q5.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>6. [KÉRDÉS] A rövid űrlapot (adatcsatorna) létrehozott x legutóbbi bejegyzések listája

Azt a legutóbbi bejegyzések fetch lekérdezésével a `posts` rendezve csökkenő létrehozás dátuma, majd a összesített felhasználónevek és a megjegyzések és kedvelések számát az egyes bejegyzéseket a hozzászólások tároló.

![Legutóbbi bejegyzések beolvasása és a további adatok összevonása](./media/how-to-model-partition-example/V1-Q6.png)

Még egyszer a kezdeti lekérdezés partíciókulcsa a nem szűr a `posts` tároló, amely elindítja a költséges erre. Látható, hogy sokkal nagyobb az eredményhalmaz célként, és szűrje az eredményeket a még rosszabb egy `ORDER BY` záradék, ami lehetővé teszi az drágább kérelemegység tekintetében.

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>V1 teljesítményét tükröző

A teljesítménnyel kapcsolatos problémák, azt az előző szakaszban megmérkőzött megnézzük, hogy is ezzel problémákat két fő osztályát:

- egyes kérésekhez ellenőriznünk kell a visszaadása, több lekérdezés katalóguskeresést kell végezni ahhoz, hogy minden adat összegyűjtése
- Bizonyos lekérdezések a partíciókulcs a tárolók céloznak, és a egy erre, amely a méretezhetőség hátráltatja nem szűrhet.

Nézzük feloldani egyes ezekre a problémákra, kezdve az elsőt.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introducing denormalizáció olvasási lekérdezések optimalizálása

Miért kell, hogy egyes esetekben további kéréseket azért, mert a kezdeti kérés eredményét adja vissza kell minden adat nem tartalmaz. Ha például az Azure Cosmos DB nem relációs adattár dolgozik, a probléma van gyakran kiküszöbölni denormalizálni az adatokat az adatkészlet között.

Ebben a példában módosítjuk utáni elemeket adja hozzá a felhasználónevet, a bejegyzés szerzője, a Megjegyzések száma és száma, kedvelések:

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

Azt is módosíthatja a megjegyzést, és elemek hozzáadása az őket létrehozó felhasználó felhasználóneve, például:

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

### <a name="denormalizing-comment-and-like-counts"></a>Megjegyzés denormalizálni és számát, például

Mit szeretnénk eléréséhez, hogy minden alkalommal, amikor hozzáadjuk a megjegyzést, vagy egy hasonló, mi is történik a `commentCount` vagy a `likeCount` a megfelelő bejegyzésben található. Mint a `posts` particionált tároló által `postId`, az új elem (tegye megjegyzésbe, vagy mint) és a megfelelő post ugyanazon logikai partíció található. Ennek eredményeképpen használhatjuk egy [tárolt eljárás](stored-procedures-triggers-udfs.md) végrehajtani a műveletet.

Most már Megjegyzés létrehozásakor (**[C3]**), helyett csak az egy új elem hozzáadása a `posts` tároló nevezzük a következő tárolt eljárást a tárolóban:

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

Ez a tárolt eljárás vesz igénybe a post és a szervezet az új megjegyzés azonosítója meg paraméterként, majd:

- a bejegyzés beolvasása
- lépésekben a `commentCount`
- a hozzászólás váltja fel
- az új megjegyzés hozzáadása

Tárolt eljárások végrehajtásakor atomi tranzakciókként, azt garantált, hogy értékét `commentCount` és hozzászólások tényleges száma mindig szinkronban maradjon.

Amikor új kedvelések növekszik nyilvánvalóan nevezzük hasonló tárolt eljárás a `likeCount`.

### <a name="denormalizing-usernames"></a>Felhasználónevek denormalizálni

Felhasználónevek különböző megközelítést igényelnek, mivel a felhasználók csak nem található a különböző partíciók, de egy másik tárolóba. Amikor denormalizálja az adatokat a partíciók és tárolók között kell, hogy a forrás-tároló használatával [módosításcsatornáját](change-feed.md).

Ebben a példában a módosítási hírcsatorna a használjuk a `users` , amikor azok felhasználónevek frissítsenek react-tárolót. Ha ez történik, azt propagálása a módosítás a egy másik tárolt eljárás meghívásával a `posts` tároló:

![A bejegyzések tárolóba denormalizálni felhasználónevek](./media/how-to-model-partition-example/denormalization-1.png)

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

Ez a tárolt eljárás vesz igénybe a felhasználó és a felhasználó új felhasználónevét azonosítója meg paraméterként, majd:

- minden elemet lekéri a `userId` (amely lehet bejegyzések, hozzászólások, vagy a kedvelések)
- az egyes elemek
  - lecseréli a `userUsername`
  - az elem váltja fel

> [!IMPORTANT]
> Tato operace je költséges, mert ez a tárolt eljárás minden partíciójának végrehajtását igényel a `posts` tároló. Feltételezzük, hogy a felhasználók többsége a megfelelő felhasználónevet válasszon a regisztrációhoz, és minden eddiginél változásaitól, így a frissítés nagyon ritkán fog futni.

## <a name="what-are-the-performance-gains-of-v2"></a>Mik azok a teljesítményre képes a V2?

### <a name="q2-retrieve-a-post"></a>[V2] Egy hozzászólás lekéréséhez

Most, hogy a denormalizáció van beállítva, hogy csak meg kell kezelni a kérést, csak egyetlen elemet beolvasni.

![A bejegyzések tárolóból egy elem beolvasása](./media/how-to-model-partition-example/V2-Q2.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[4] A post megjegyzések listázása

Itt újra, hogy is tartalék az extra beolvasni a felhasználónevek és a záró egyetlen lekérdezéssel, amely szűri a partíciókulcs a kérelmeket.

![Egy bejegyzés az összes megjegyzés lekérése](./media/how-to-model-partition-example/V2-Q4.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ms | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[5. KÉRDÉS] A post kedvelések listázása

Pontos ugyanez a helyzet a kedvelések listázása során.

![A hozzászólás lekéri az összes kedvelések](./media/how-to-model-partition-example/V2-Q5.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Gondoskodik róla, hogy minden kérelmet is méretezhető

Az általános, teljesítménnyel kapcsolatos fejlesztések megnézzük, továbbra is vannak két kérelmet, hogy még nem teljes mértékben optimalizált: **[3. negyedévi]** és **[6. kérdés]**. Azok a kérelmeket, a tárolók céloznak partíciókulcsa a szűrő nem lekérdezéseket is.

### <a name="q3-list-a-users-posts-in-short-form"></a>[3. NEGYEDÉVI] Rövid képernyőn a felhasználó bejegyzések listája

Ezt a kérelmet már előnyös a V2, amely megkíméli további lekérdezések rendszerben bevezetett fejlesztései.

![Egy felhasználó minden bejegyzés beolvasása](./media/how-to-model-partition-example/V2-Q3.png)

De a többi lekérdezés még nem szűrést a partíciókulcsa a `posts` tároló.

Az ebben a helyzetben gondolja módja ténylegesen egyszerű:

1. A kérelem *rendelkezik* szűrés a `userId` mivel azt egy adott felhasználó minden bejegyzés beolvasása
1. Ez nem hajtható végre, jól, mert elleni hajtja végre a `posts` tároló, amely nincs particionálva által `userId`
1. Figyelmezteti a nyilvánvaló, azt megoldhatja a teljesítménnyel kapcsolatos problémákat úgy, hogy végrehajtja a kérelem egy tároló, amely *van* particionált szerint `userId`
1. Azt tapasztaltuk, hogy már van ilyen egy tároló: a `users` tároló!

A második szintű denormalizáció teljes hozzászólások megkettőzésével is bevezettünk, a `users` tároló. Amely elvégzésével hatékony kapunk egy példányát a bejegyzések csak egy másik dimenzió mentén particionált így sokkal hatékonyabb a letöltendő azok `userId`.

A `users` tároló most már tartalmaz 2 típusú elem:

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

- bevezettük a `type` hozzászólások megkülönböztetni a felhasználók a felhasználói konfigurációelem mező
- is hozzáadtunk egy `userId` mezőbe a felhasználói cikk, amely a redundáns a `id` mező azonban szükség, mint a `users` tároló most már dokumentumtárolási `userId` (és nem `id` korábban)

Adott denormalizáció eléréséhez a változáscsatorna ismét használjuk. Ennek során azt reagálni, a módosítási hírcsatorna a a `posts` tárolót minden olyan új vagy frissített közzététel a szállítást a `users` tároló. És ajánlati bejegyzések nem igényel vissza a teljes tartalom, mert azt is truncate őket a folyamat során.

![A felhasználók tárolóba denormalizálni bejegyzések](./media/how-to-model-partition-example/denormalization-2.png)

Most már tudjuk irányítani a lekérdezést a `users` tárolót, a szűrést a partíciókulcs a tárolót.

![Egy felhasználó minden bejegyzés beolvasása](./media/how-to-model-partition-example/V3-Q3.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>6. [KÉRDÉS] A rövid űrlapot (adatcsatorna) létrehozott x legutóbbi bejegyzések listája

A fentiekhez hasonlóan itt kezelésére van: után is a további lekérdezések sparing szükségtelen által hátrahagyott a V2-ben bevezetett denormalizáció, a fennmaradó lekérdezés nem szűrhet a tároló partíciókulcs:

![Legutóbbi bejegyzések beolvasása](./media/how-to-model-partition-example/V2-Q6.png)

Ugyanezzel a módszerrel, a kérelem teljesítmény és skálázhatóság maximalizálása a következő szükséges, hogy csak elér egy partíciót. Ez az elképzelhető, mert csak korlátozott számú elemet; vissza annak érdekében, hogy feltölti a blogplatform kezdőlapja, csak le kell a 100 legutóbbi bejegyzések révén a teljes adatkészlet oldalakra bontása nélkül.

A legutóbbi kérelem optimalizálása érdekében bevezettünk egy harmadik tárolót, a tervezési, így teljes mértékben dedikált szolgálja ki ezt a kérelmet. Mi az, hogy az újonnan bejegyzések denormalizálja `feed` tároló:

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

Ezen tároló által particionálása `type`, amely mindig, `post` az elemek. Ennek során, amely biztosítja, hogy minden ebben a tárolóban található elemek ugyanazon a partíción lesz található.

A denormalizáció eléréséhez csak rá a változáscsatorna folyamat korábban már bevezettük az új tároló hozzászólások tart elküldeni a környezet igénybe vételét. Szem operációs egyik lényeges tudnivaló, hogy azt kell győződjön meg arról, hogy csak tárolása a legutóbbi 100 bejegyzések; Ellenkező esetben a tároló tartalmának előfordulhat, hogy növelje meghaladja a maximális partíció. Ez történik, meghívásával egy [utáni eseményindító](stored-procedures-triggers-udfs.md#triggers) minden alkalommal, amikor egy dokumentum hozzáadása a tárolóban:

![A hírcsatorna tárolóba denormalizálni bejegyzések](./media/how-to-model-partition-example/denormalization-3.png)

A gyűjtemény csonkolja utáni trigger törzse a következő:

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

![Legutóbbi bejegyzések beolvasása](./media/how-to-model-partition-example/V3-Q6.png)

| **Késés** | **RU-díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Összegzés

Nézzük meg, az általános teljesítmény és méretezhetőségi fejlesztései. bevezettük a tervezési különböző verzióihoz képest.

| | 1. verzió | 2. verzió | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / 8.57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | az ms 23 / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Hogy ez a forgatókönyv olvasási optimalizálása

Talán már észrevette, hogy mi közölte erőfeszítéseinket javítására vonatkozó olvasási kérelmek (lekérdezések) a teljesítmény rovására írási kérések (parancsok). Sok esetben az írási műveletek aktiválja ezt követő denormalizáció keresztül módosítás adatcsatorna, ami lehetővé teszi őket, akár több költséges és tényleges táblává alakíthatóak hosszabb.

Ez az a tény, hogy egy blogplatform, amely (mint például a legtöbb közösségi)-e olvasási-(nagy erőforrásigényű), ami azt jelenti, hogy kiszolgálására rendelkezik olvasási kérések mennyisége általában magasabb, mint az írási kérések mennyisége nagyságrenddel indokolt. Ezért logikus, hogy az írási kérelmek drágább hajtható végre annak érdekében, hogy lehetővé teszik az olvasási kérések lehet olcsóbb és nagyobb végrehajtása.

Ha eddig még, a leginkább szélsőséges optimalizálási **[6. kérdés]** a()z) 2000 + RUs csak 17 csökkenti; el, amely körülbelül 10 kérelemegységet elemenként áron bejegyzések denormalizálni által. Azt fogják kiszolgálni, mint a létrehozás vagy hozzászólás frissítések sokkal hírcsatorna kéréseket, mert ez denormalizáció költsége elhanyagolható a mérlegeli, az általános megtakarítási.

### <a name="denormalization-can-be-applied-incrementally"></a>Növekményes denormalizáció is alkalmazható

A méretezhetőséggel kapcsolatos fejlesztések, hogy korábban bemutattuk az ebben a cikkben denormalizáció és adatredundancia magában foglalja az adatkészlet között. Megjegyzendő, hogy ezek az optimalizációk nem kell kell bevezetni 1 nap. A partíciókulcsok szűrő lekérdezéseket jobb teljesítményt nagy mennyiségű, de lehet, hogy a partícióra kiterjedő lekérdezések teljes mértékben elfogadható, ha ritkán vagy korlátozott adat szoftverkonfigurációját nevezzük. Ha éppen fejleszt, a prototípus, vagy egy kis- és ellenőrzött felhasználói bázis termék indításakor, akkor valószínűleg tartalék későbbi használatra; ezek a fejlesztések Ezután a fontos, hogy [figyelő](use-metrics.md) a modell teljesítményét, így eldöntheti, ha ideje betöltheti őket.

A módosítási hírcsatorna, hogy használjuk-e frissítéseket más tárolók Store egyáltalán tartósan frissíti a. Ez lehetővé teszi lekéri az összes módosítást a tároló és a rendszer-indításkori denormalizált nézetek létrehozása óta utólagos műveletet egyszer kell, akkor is, ha a rendszer már rendelkezik-e nagy mennyiségű adatot.

## <a name="next-steps"></a>További lépések

Után ez a bevezető gyakorlati adatok modellezése és particionálás érdemes áttekinteni azokat a fogalmakat, hogy a következő cikkekben ellenőrzéséhez:

- [Adatbázisok, tárolók és elemek használata](databases-containers-items.md)
- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Az Azure Cosmos DB csatorna módosítása](change-feed.md)