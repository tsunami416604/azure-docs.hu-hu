---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905056"
---
## <a name="create-client"></a>Ügyfélkapcsolat létrehozása
Hozzon létre egy ügyfélkapcsolatot egy `WindowsAzure.MobileServiceClient` objektum létrehozásával.  Az `appUrl` helyére írja be mobilalkalmazása URL-címét.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>Táblázatok használata
Az adatok elérése vagy frissítése érdekében hozzon létre a háttértáblára mutató hivatkozást. A `tableName` helyére írja be a tábla nevét.

```javascript
var table = client.getTable(tableName);
```

Ha létrehozta a táblahivatkozást, további műveleteket végezhet a táblával:

* [Tábla lekérdezése](#querying)
  * [Adatok szűrése](#table-filter)
  * [Adatok lapozása](#table-paging)
  * [Adatok rendezése](#sorting-data)
* [Adatok beszúrása](#inserting)
* [Adatok módosítása](#modifying)
* [Adatok törlése](#deleting)

### <a name="querying"></a>kézikönyv: Táblahivatkozás lekérdezése
Ha létrehozta a táblahivatkozást, adatokat kérhet le a segítségével a kiszolgálóról.  A lekérdezések egy, a LINQ-hez hasonló nyelv használatával végezhetőek el.
A tábla összes adatának visszaadásához használja a következő kódot:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Ekkor a rendszer meghívja a success függvényt az eredményekkel együtt.  Ne használja a `for (var i in results)` elemet a success függvényben, mivel az megismétli az eredményekben található információkat más lekérdezési funkciók használatakor (például: `.includeTotalCount()`).

A lekérdezési szintaxissal kapcsolatos további információ: [Lekérdezésobjektum dokumentációja].

#### <a name="table-filter"></a>A kiszolgálón lévő adatok szűrése
A táblahivatkozáson használhatja a `where` záradékot:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Használhat olyan függvényt is, amelyt szűri az objektumot.  Ebben az esetben a `this` változó van az aktuálisan szűrt objektumhoz rendelve.  A következő kód funkcionalitását tekintve megegyezik az előző példában szereplővel:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>Adatok lapozása
Használja a `take()` és a `skip()` metódust.  Például ha 100 soros rekordokra szeretné felosztani a táblát:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

A `.includeTotalCount()` metódus hozzáadja a totalCount mezőt az eredményobjektumhoz.  A totalCount mezőben azoknak a rekordoknak a teljes száma szerepel, amelyeket a rendszer visszaadna, ha nem használna lapozást.

A pages változóval és a felhasználói felület egyes gombjaival oldallistát adhat meg. A `loadPage()` segítségével töltheti be az új rekordokat az egyes oldalakon.  Használjon gyorsítótárazást a már betöltött rekordok eléréséhez.

#### <a name="sorting-data"></a>kézikönyv: Rendezett adatok visszaadása
Használja az `.orderBy()` vagy az `.orderByDescending()` lekérdezési metódust:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

A lekérdezési objektummal kapcsolatos további információ: [Lekérdezésobjektum dokumentációja].

### <a name="inserting"></a>kézikönyv: Adat beszúrása
Hozzon létre egy JavaScript-objektumot a megfelelő dátummal, és hívja meg a `table.insert()` függvényt aszinkrón módon:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

A sikeres beszúrás után a beszúrt elemet a rendszer visszaadja a szinkronizálási műveletekhez szükséges további mezőkkel együtt.  Frissítse ezekkel az információkkal a gyorsítótárat a későbbi frissítésekhez.

Az Azure Mobile Apps Node.js Server SDK támogatja a fejlesztési célra szolgáló dinamikus sémákat.  A dinamikus sémák lehetővé teszik, hogy oszlopokat adjon a táblához úgy, hogy megadja őket egy beszúrási vagy frissítési műveletben.  Javasoljuk a dinamikus sémák kikapcsolását az alkalmazás éles környezetbe helyezése előtt.

### <a name="modifying"></a>kézikönyv: Adatok módosítása
Az `.insert()` metódushoz hasonlóan hozzon létre egy frissítési objektumot majd hívja meg a következőt: `.update()`.  A frissítési objektumnak tartalmaznia kell a frissíteni kívánt rekord azonosítóját – ez a rekord olvasásakor vagy az `.insert()` meghívásakor szerezhető meg.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>kézikönyv: Adat törlése
Egy rekord törléséhez hívja meg a `.del()` metódust.  Adja át az azonosítót egy objektumhivatkozásban:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
