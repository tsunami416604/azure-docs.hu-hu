---
title: "MongoDB olvasható beállítás használata az Azure Cosmos DB MongoDB API |} Microsoft Docs"
description: "Útmutató: Azure Cosmos DB MongoDB API-val MongoDB olvasási preferenciát kell használnia"
services: cosmos-db
documentationcenter: 
author: vidhoonv
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: b28285695f52d6dbcc33d9fb0efe06b43cdf1cab
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Globálisan terjesztése beolvassa olvasható beállítás használata az Azure Cosmos DB MongoDB API 

Ez a cikk bemutatja, hogyan globálisan terjeszteni az olvasási műveletek használatával [MongoDB olvasható beállítás](https://docs.mongodb.com/manual/core/read-preference/) Azure Cosmos DB MongoDB API-beállításokat. 

## <a name="prerequisites"></a>Előfeltételek 
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Tekintse meg a [gyors üzembe helyezés](tutorial-global-distribution-mongodb.md) cikk az Azure-portál használatával globális terjesztési Azure Cosmos DB fiók beállítását, és csatlakoztassa a MongoDB API használatával.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépejen át egy munkakönyvtárba.  

Az alábbi parancsok futtatásával klónozza a mintatárházat. Fontos a platformtól függően, használja a következő minta adattárak egyikét:

1. [.NET mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS mintaalkalmazás]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot mintaalkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Attól függően, hogy a használt platformra a szükséges csomagok telepítése, és indítsa el az alkalmazást. Függőségek telepítéséhez hajtsa végre az információs fájl, a minta alkalmazás tárház szerepel. Például a NodeJS mintaalkalmazás segítségével az alábbi parancsokat a szükséges csomagok telepítése, és indítsa el az alkalmazást.

```bash
cd mean
npm install
node index.js
```
Az alkalmazás megpróbál csatlakozni a MongoDB-forrás, és sikertelen lesz, mivel a kapcsolati karakterlánc érvénytelen. Kövesse az információs fájl frissítése a kapcsolati karakterláncot a `url`. Frissítse a `readFromRegion` Azure Cosmos DB fiókjában olvasási régióban. Az alábbi utasításokat a NodeJS mintából a következők:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

Után ezeket a lépéseket követve, a mintaalkalmazást, és a következő kimenetet:

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

## <a name="read-using-read-preference-mode"></a>Olvasott olvasási Preferencia mód

MongoDB biztosít az ügyfelek számára a következő olvasható beállítás módjainak:

1. ELSŐDLEGES
2. PRIMARY_PREFERRED
3. MÁSODLAGOS
4. SECONDARY_PREFERRED
5. LEGKÖZELEBBI

Tekintse meg a részletes [MongoDB olvasható beállítás viselkedése](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) valamelyikében dokumentációjában találhatók a működését, olvassa el előnyben módok. Azure Cosmos DB elsődleges régió írási és OLVASÁSI régió másodlagos leképezve rendeli hozzá.

Gyakori forgatókönyvek alapján, azt javasoljuk, a következő beállításokat:

1. Ha **kis késleltetésű beolvassa** van szükség esetén használja a **NEAREST** Preferencia mód olvasni. Ez a beállítás utasítja az olvasási műveletek a legközelebbi rendelkezésre álló terület. Vegye figyelembe, hogy ha a legközelebbi régiót az írási régiót, majd ezeket a műveleteket a rendszer átirányítja adott régióban.
2. Ha **olvasási magas rendelkezésre állás és a földrajzi eloszlásáról** szükségesek (késés nincs korlátozás), majd használja a **másodlagos előnyben részesített** olvasási Preferencia mód. Ez a beállítás utasítja az olvasási műveletek egy elérhető OLVASÁSI régióban. Nem OLVASHATÓ terület áll rendelkezésre, ha kérelmek az írási régió van irányítva.

Az alábbi kódrészletben a mintaalkalmazást a LEGKÖZELEBBI olvasási beállítások konfigurálása a NodeJS láthatja:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Hasonlóképpen az alábbi kódrészletben láthatja, a SECONDARY_PREFERRED olvasási beállítások konfigurálása a NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Tekintse meg a megfelelő minta alkalmazás repók a más platformokon, például a [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Olvassa el a címkék használatával

Az olvasási Preferencia mód mellett MongoDB engedélyezi, hogy át tudja irányítani az olvasási műveletek címkék. Az Azure Cosmos Adatbázisba MongoDB az API-hoz a `region` címke veszi fel alapértelmezés szerint egy részét a `isMaster` választ:

```json
"tags": {
         "region": "West US"
      }
```

Emiatt MongoClient használhatja a `region` címkét az adott területre az olvasási műveletek közvetlen régió nevét. Azure-portálon a bal oldalon található terület név Azure Cosmos DB fiókok **beállítások -> replikaadatok globálisan**. Ez a beállítás akkor hasznos, ha elérése **elkülönítési olvasási** -mely ügyfélalkalmazás olyan esetben szeretné irányítani az olvasási műveletek csak egy adott területre. Ez a beállítás akkor hasznos, ha nem éles/analytics írja be a forgatókönyveket, amelyek a háttérben futnak, és nincsenek éles kritikus szolgáltatások.

A következő kódrészletet a mintaalkalmazást a bemutatja, hogyan NodeJS címkék a olvasási beállítások konfigurálása:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Tekintse meg a megfelelő minta alkalmazás repók a más platformokon, például a [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) és [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Ebben a cikkben olvasható beállítás használata Azure Cosmos DB MongoDB API olvasási műveletek globálisan terjesztése hogy megismerte.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Nem fog továbbra is használhatják az alkalmazást, ha törli a cikkben a következő lépések az Azure-portálon létrehozott összes erőforrás:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

* [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
* [Globálisan replikált Azure Cosmos DB fiók beállítása és használatához a MongoDB API](tutorial-global-distribution-mongodb.md)
* [Helyileg emulátorral fejlesztése](local-emulator.md)
