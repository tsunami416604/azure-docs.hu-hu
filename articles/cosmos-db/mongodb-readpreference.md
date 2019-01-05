---
title: MongoDB olvasási beállítás használata az Azure Cosmos DB API a mongodb-hez
description: Ismerje meg, a MongoDB olvasási beállítás használata az Azure Cosmos DB API a mongodb-hez
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.openlocfilehash: dfb1e0093893fadf22c7a92ef5f351ae8920a977
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036460"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Hogyan terjessze globálisan olvassa be az Azure Cosmos DB API használatával a mongodb-hez

Ez a cikk bemutatja, hogyan terjessze globálisan az olvasási műveletek [MongoDB olvasási beállítás](https://docs.mongodb.com/manual/core/read-preference/) beállításait a mongodb-hez Azure Cosmos DB API segítségével.

## <a name="prerequisites"></a>Előfeltételek 
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Ebben [rövid](tutorial-global-distribution-mongodb.md) cikk útmutatást találhat az Azure portal használatával állítsa be a globális elosztás egy Cosmos-fiók beállítása, és hogyan csatlakozhat ahhoz.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépejen át egy munkakönyvtárba.  

Az alábbi parancsok futtatásával klónozza a mintatárházat. A platform a lényeges alapján, használja a következő minta tárházak egyikét:

1. [.NET mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS-mintaalkalmazás]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [A mongoose-mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot-mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A használt platformtól függően a szükséges csomagok telepítéséhez, és indítsa el az alkalmazást. Függőségek telepítéséhez kövesse a mintatárházban található információs fájlban OLVASHATÓ. Például a NodeJS-mintaalkalmazás használatával a következő parancsokat a szükséges csomagok telepítéséhez és az alkalmazás elindításához.

```bash
cd mean
npm install
node index.js
```
Az alkalmazás megpróbál csatlakozni egy MongoDB-forráshoz, és meghiúsul, mert a kapcsolati karakterlánc érvénytelen. Kövesse a lépéseket a kapcsolati karakterlánc frissítése példasablonjainak `url`. Frissítse a `readFromRegion` olvasási régiót Cosmos-fiókjában található. Az alábbi utasításokat a NodeJS-mintából a következők:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Után a következő lépésekkel a mintaalkalmazás futtatja, és a következő kimenetet:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Olvasási mód olvasási beállítás használata

MongoDB-protokoll biztosítja az ügyfelek számára a következő olvasási beállítás mód:

1. ELSŐDLEGES
2. PRIMARY_PREFERRED
3. MÁSODLAGOS
4. SECONDARY_PREFERRED
5. LEGKÖZELEBBI

Tekintse meg a részletes [MongoDB olvasási beállítás viselkedése](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) ezekről a működését a dokumentációt, olvassa el előnyben módokat. Cosmos dB-ben elsődleges írási régiót és a másodlagos OLVASÁSI régióban leképezések rendeli hozzá.

Gyakori forgatókönyvek alapján, azt javasoljuk, a következő beállításokat:

1. Ha **alacsony késés** is szükséges, használja a **NEAREST** olvasási szabályozó mód. Ez a beállítás utasítja az olvasási műveletek a legközelebbi elérhető régióban. Vegye figyelembe, hogy ha a legközelebbi régió, az írási régiót, majd ezeket a műveleteket a rendszer átirányítja adott régióban.
2. Ha **olvasási magas rendelkezésre állás és a földrajzi eloszlása** szükség (késés nem áll egy korlátozás), majd használja a **másodlagos előnyben részesített** olvasási szabályozó mód. Ez a beállítás utasítja a rendelkezésre álló OLVASÁSI régiók olvasási műveletek. Ha nincs OLVASÁSI régió nem érhető el, majd kérelmek irányítja, az írási régió.

A mintaalkalmazás az alábbi kódrészlet bemutatja, hogyan NodeJS a LEGKÖZELEBBI olvasási beállítás konfigurálása:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Hasonlóképpen az alábbi kódrészlet bemutatja, hogyan NodeJS nyelven a SECONDARY_PREFERRED olvasási beállítás konfigurálása:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Az olvasási beállítás is megadható átadásával `readPreference` paraméterként a kapcsolati karakterlánc URI-beállítások:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Tekintse meg a megfelelő minta alkalmazás adattáraknál a más platformokon, például [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Olvassa el a címkék használatával

Mellett az olvasási beállítás mód a MongoDB protokoll lehetővé teszi a közvetlen olvasási műveletek címkék használatát. A Cosmos DB API a mongodb-hez a `region` címke részeként alapértelmezés szerint tartalmazza a `isMaster` választ:

```json
"tags": {
         "region": "West US"
      }
```

Ezért a MongoClient használhatja a `region` címkét az olvasási műveletek adott régiókba történő közvetlen régiónévhez együtt. Cosmos-fiókok esetében a régiók neveit megtalálható az Azure portal alatt a bal oldali **beállítások -> replika adatait globálisan**. Ez a beállítás akkor hasznos, eléréséhez **elkülönítési olvasási** -melyik ügyfélalkalmazás olyan esetben szeretné irányítani az olvasási műveletek csak egy adott területre. Ez a beállítás ideális megoldást biztosít nem éles/Analytics írja be a forgatókönyveket, amelyek a háttérben futnak, és nem éles kritikus szolgáltatások.

A mintaalkalmazás az alábbi kódrészlet bemutatja, hogyan NodeJS a címkék az olvasási beállítás konfigurálása:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Tekintse meg a megfelelő minta alkalmazás adattáraknál a más platformokon, például [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Ebben a cikkben bemutattuk, hogyan terjessze globálisan az olvasási műveletek olvasási beállítás használata az Azure Cosmos DB API a mongodb-hez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje a Ez a cikk az alábbi lépéseket követve az Azure Portalon létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

* [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
* [A telepítő egy globálisan elosztott adatbázist az Azure Cosmos DB API a mongodb-hez](tutorial-global-distribution-mongodb.md)
* [Helyi fejlesztés az Azure Cosmos DB emulátorral](local-emulator.md)
