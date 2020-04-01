---
title: Olvasási preferencia használata az Azure Cosmos DB MongoDB API-jával
description: Ismerje meg, hogyan használhatja a MongoDB olvasási preferenciáját az Azure Cosmos DB MongoDB-hoz való API-jával
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 579767a0d535605a2316c35bd413a75474b5a3de
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410005"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Olvasások globális terjesztése az Azure Cosmos DB MongoDB-hoz való API-jával

Ez a cikk bemutatja, hogyan globálisan terjesztheti olvasási műveletek [MongoDB olvasási preferencia](https://docs.mongodb.com/manual/core/read-preference/) beállítások segítségével az Azure Cosmos DB API-t a MongoDB használatával.

## <a name="prerequisites"></a>Előfeltételek 
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Tekintse meg ezt a [rövid útmutató](tutorial-global-distribution-mongodb.md) cikket az Azure Portal használatával globális disztribúcióval létrehozott Cosmos-fiók beállításához, majd csatlakozzon hozzá.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

Az alábbi parancsok futtatásával klónozza a mintatárházat. Az ön érdeklődési platformja alapján használja az alábbi mintatárolók egyikét:

1. [.NET mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS mintaalkalmazás]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongúz mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A használt platformtól függően telepítse a szükséges csomagokat, és indítsa el az alkalmazást. Függőségek telepítéséhez kövesse a mintaalkalmazás-tárházban található README-t. Például a NodeJS mintaalkalmazás, használja a következő parancsokat a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
cd mean
npm install
node index.js
```
Az alkalmazás megpróbál csatlakozni egy MongoDB forráshoz, és nem sikerül, mert a kapcsolati karakterlánc érvénytelen. A kapcsolati karakterlánc frissítéséhez kövesse `url`a README lépéseit. Emellett frissítse `readFromRegion` a Cosmos-fiók olvasási régiójára. A következő utasítások a NodeJS-mintából származnak:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Az alábbi lépések végrehajtásával a mintaalkalmazás a következő kimenetet futtatja és állítja elő:

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

## <a name="read-using-read-preference-mode"></a>Olvasás olvasási beállítással

A MongoDB protokoll a következő olvasási beállításokat biztosítja az ügyfelek számára:

1. Elsődleges
2. PRIMARY_PREFERRED
3. Másodlagos
4. SECONDARY_PREFERRED
5. Legközelebbi

Az egyes olvasási beállításmódok viselkedésével kapcsolatos részletes dokumentációban a [MongoDB olvasási preferencia viselkedési](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) dokumentációjában olvashat. A Cosmos DB elsődleges térképek írása régió és másodlagos térképek READ régióban.

A gyakori forgatókönyvek alapján a következő beállításokhasználatát javasoljuk:

1. Ha **alacsony késleltetésű olvasásra** van szükség, használja a **LEGKÖZELEBBI** olvasási beállítási módot. Ez a beállítás irányítja az olvasási műveleteket a legközelebbi elérhető régióba. Vegye figyelembe, hogy ha a legközelebbi régió a WRITE régió, majd ezek a műveletek az adott régióba lesznek irányítva.
2. Ha **az olvasások magas rendelkezésre állása és földrajzi eloszlása** szükséges (a késés nem korlátozás), akkor használja az **ELSŐDLEGES PREFERÁLT** vagy MÁSODLAGOS **PREFERÁLT** olvasási beállítási módot. Ez a beállítás az olvasási műveleteket egy elérhető WRITE vagy READ régióba irányítja. Ha a régió nem érhető el, majd a kérelmek a következő rendelkezésre álló régióba lesznek irányítva az olvasási beállítás viselkedésének megfelelően.

A mintaalkalmazás következő kódrészlete bemutatja, hogyan konfigurálható a LEGKÖZELEBBI olvasási preferencia a NodeJS-ben:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Hasonlóképpen az alábbi kódrészlet bemutatja, hogyan konfigurálható a SECONDARY_PREFERRED olvasási preferencia a NodeJS-ben:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Az Olvasási beállítás úgy `readPreference` is beállítható, hogy paraméterként adja át a kapcsolati karakterlánc URI-beállításait:

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

Tekintse meg a megfelelő mintaalkalmazás-repofájlokat más platformokon, például a [.NET-en](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és a [Java-n.](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)

## <a name="read-using-tags"></a>Olvasás címkék használatával

Az Olvasási beállítás mód mellett a MongoDB protokoll lehetővé teszi a címkék használatát az olvasási műveletek közvetlen irányításához. A Cosmos DB MongoDB API-jában a `region` címke alapértelmezés szerint `isMaster` a válasz részeként szerepel:

```json
"tags": {
         "region": "West US"
      }
```

Ezért a MongoClient `region` használhatja a címkét a régió nevével együtt, hogy az olvasási műveleteket adott régiókra irányítsa. A Cosmos-fiókok esetében a régiónevek az Azure Portalon találhatók a **Beállítások >Replika-adatok globális an- és környezetben.** Ez a beállítás **olvasási elkülönítés** eléréséhez hasznos – olyan esetekben, amikor az ügyfélalkalmazás csak egy adott régióra szeretné irányítani az olvasási műveleteket. Ez a beállítás ideális nem éles/elemzési típusú forgatókönyvek, amelyek a háttérben futnak, és nem éles kritikus szolgáltatások.

A mintaalkalmazás következő kódrészlete bemutatja, hogyan konfigurálható az Olvasási beállítás a NodeJS-ben lévő címkékkel:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Tekintse meg a megfelelő mintaalkalmazás-repofájlokat más platformokon, például a [.NET-en](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és a [Java-n.](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)

Ebben a cikkben megtanulta, hogyan terjesztheti globálisan az olvasási műveleteket az Azure Cosmos DB MongoDB API-jával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a cikk által létrehozott összes erőforrást az Azure Portalon az alábbi lépésekkel:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

* [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
* [Globálisan elosztott adatbázis beállítása az Azure Cosmos DB MongoDB-hoz való API-jával](tutorial-global-distribution-mongodb.md)
* [Helyi fejlesztés az Azure Cosmos DB emulátorral](local-emulator.md)
