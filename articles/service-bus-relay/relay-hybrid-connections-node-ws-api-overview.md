---
title: Az Azure Relay Node API-k áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt a Azure Relay szolgáltatás Node. js API-jával. Azt is bemutatja, hogyan használható a HYCO-ws Node-csomag.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 2877284c419da4999e23490fc986e5da44e5d92e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514510"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Relay Hibrid kapcsolatok Node API – áttekintés

## <a name="overview"></a>Áttekintés

A Azure Relay Hibrid kapcsolatok [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) -csomópontja a ["ws"](https://www.npmjs.com/package/ws) NPM-csomagra épül, és kibővíti azt. Ez a csomag újraexportálja az adott alapcsomag összes exportálását, és új exportálásokat hoz létre, amelyek lehetővé teszik az integrációt az Azure Relay Service Hibrid kapcsolatok szolgáltatással. 

A meglévő alkalmazások, amelyek `require('ws')` használhatják ezt a csomagot, `require('hyco-ws')` helyette, ami olyan hibrid forgatókönyveket is lehetővé tesz, amelyekben az alkalmazás a WebSocket-kapcsolatokat helyileg, a "tűzfalon belül" és a Hibrid kapcsolatokon keresztül is figyeli.
  
## <a name="documentation"></a>Dokumentáció

Az API [-k dokumentálva vannak a fő "ws" csomagban](https://github.com/websockets/ws/blob/master/doc/ws.md). Ez a cikk azt ismerteti, hogyan különbözik a csomag az alaptervtől. 

Az alapcsomag és a jelen "HYCO-ws" közötti fő különbség az, hogy egy új, `require('hyco-ws').RelayedServer`on és néhány segítő módszeren keresztül exportált kiszolgálói osztályt hoz létre.

### <a name="package-helper-methods"></a>Csomag segítő módszerei

A csomag exportálásának számos segédprogram-módszere elérhető, amelyet a következőképpen hivatkozhat:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Ezzel a csomaggal használhatók a segítő módszerek, de a csomópont-kiszolgáló is használhatja a webes vagy az eszköz ügyfelei számára a figyelők vagy a küldők létrehozásához. A kiszolgáló ezeket a metódusokat használja a rövid élettartamú tokeneket beágyazó URI-k átadásával. Ezek az URI-k olyan közös WebSocket-veremekkel is használhatók, amelyek nem támogatják a HTTP-fejlécek beállítását a WebSocket-kézfogáshoz. Az engedélyezési jogkivonatok URI-ba való beágyazását elsősorban a függvénytár-külső használati forgatókönyvek támogatják. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Egy érvényes Azure Relay hibrid kapcsolatok figyelő URI-t hoz létre a megadott névtérhez és elérési úthoz. Ezt az URI-t ezután a WebSocketServer osztály Relay-verziójával lehet használni.

- `namespaceName` (kötelező) – a használni kívánt Azure Relay névtér tartományhoz tartozó neve.
- `path` (kötelező) – egy meglévő Azure Relay hibrid-kapcsolatok neve a névtérben.
- `token` (nem kötelező) – a figyelő URI-ba beágyazott, korábban kiadott továbbító hozzáférési jogkivonat (lásd a következő példát).
- `id` (nem kötelező) – olyan követési azonosító, amely lehetővé teszi a kérelmek végpontok közötti diagnosztikai nyomon követését.

A `token` érték nem kötelező, és csak akkor használható, ha a WebSocket-kézfogással együtt nem lehet HTTP-fejléceket küldeni, ahogy a W3C WebSocket-verem esetében is.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Létrehoz egy érvényes Azure Relay hibrid kapcsolatok küldési URI-JÁT a megadott névtérhez és elérési úthoz. Ezt az URI-t bármely WebSocket-ügyféllel együtt használhatja.

- `namespaceName` (kötelező) – a használni kívánt Azure Relay névtér tartományhoz tartozó neve.
- `path` (kötelező) – egy meglévő Azure Relay hibrid-kapcsolatok neve a névtérben.
- `token` (nem kötelező) – a küldési URI-ba beágyazott, korábban kiadott továbbító hozzáférési jogkivonat (lásd a következő példát).
- `id` (nem kötelező) – olyan követési azonosító, amely lehetővé teszi a kérelmek végpontok közötti diagnosztikai nyomon követését.

A `token` érték nem kötelező, és csak akkor használható, ha a WebSocket-kézfogással együtt nem lehet HTTP-fejléceket küldeni, ahogy a W3C WebSocket-verem esetében is.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Létrehoz egy Azure Relay megosztott hozzáférési aláírási (SAS-) tokent az adott cél URI, SAS szabály és SAS-szabály kulcsa számára, amely a megadott számú másodpercre érvényes, vagy ha a lejárati argumentum nincs megadva, akkor az aktuális pillanattól egy órára.

- `uri` (kötelező) – az az URI, amely számára ki kell állítani a jogkivonatot. Az URI a HTTP-séma használatára van normalizálva, és a lekérdezési karakterlánc adatai el vannak megfosztotva.
- `ruleName` (kötelező) – az SAS-szabály neve az adott URI által képviselt entitáshoz vagy az URI-gazda rész által képviselt névtérhez.
- `key` (kötelező) – az SAS-szabály érvényes kulcsa. 
- `expirationSeconds` (nem kötelező) – azon másodpercek száma, ameddig a generált tokennek le kell járnia. Ha nincs megadva, az alapértelmezett érték 1 óra (3600).

A kiállított jogkivonat megadja a megadott SAS-szabályhoz társított jogosultságokat a megadott időtartamra.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ez a módszer funkcionálisan egyenértékű a korábban dokumentált `createRelayToken` metódussal, de a bemeneti URI-hoz helyesen hozzáfűzött tokent adja vissza.

### <a name="class-wsrelayedserver"></a>Osztály ws. RelayedServer

A `hycows.RelayedServer` osztály az `ws.Server` osztály alternatívája, amely nem figyeli a helyi hálózatot, de a Azure Relay szolgáltatás figyelését delegálja.

A két osztály többnyire kompatibilis, ami azt jelenti, hogy a `ws.Server` osztályt használó meglévő alkalmazások egyszerűen módosíthatók a közvetített verzió használatára. A fő különbségek a konstruktorban és a rendelkezésre álló beállításokban találhatók.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

A `RelayedServer` konstruktora más argumentumokat támogat, mint a `Server`, mert nem önálló figyelő, vagy egy meglévő HTTP-figyelő keretrendszerbe ágyazható be. Emellett kevesebb lehetőség érhető el, mivel a WebSocket-felügyelet nagyrészt a továbbítási szolgáltatáshoz van delegálva.

Konstruktor argumentumai:

- `server` (kötelező) – a megfigyelni kívánt hibrid kapcsolati név teljesen minősített URI-ja, amely általában a WebSocket. createRelayListenUri () segítő metódussal lett felépítve.
- `token` (kötelező) – Ez az argumentum egy korábban kiadott jogkivonat-karakterláncot vagy egy visszahívási függvényt tartalmaz, amely meghívható egy ilyen jogkivonat-karakterlánc beszerzésére. A visszahívási beállítás előnyben részesített, mivel lehetővé teszi a jogkivonat megújítását.

#### <a name="events"></a>Események

`RelayedServer` példányok három olyan eseményt bocsátanak ki, amelyek lehetővé teszik a bejövő kérések kezelését, a kapcsolatok létrehozását és a hibák észlelését. Az üzenetek kezelésére elő kell fizetnünk a `connect` eseményre. 

##### <a name="headers"></a>fejlécek

```JavaScript 
function(headers)
```

A `headers` esemény közvetlenül a bejövő kapcsolat elfogadása előtt következik be, ami lehetővé teszi az ügyfélnek küldendő fejlécek módosítását. 

##### <a name="connection"></a>kapcsolat

```JavaScript
function(socket)
```

Egy új WebSocket-kapcsolat elfogadásakor lett kibocsátva. Az objektum típusa `ws.WebSocket`, ugyanaz, mint az alapcsomag.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Ha az alapul szolgáló kiszolgáló hibát jelez, itt továbbítjuk.  

#### <a name="helpers"></a>Segítők

A továbbított kiszolgálók indításának leegyszerűsítése és a bejövő kapcsolatokra való azonnali Feliratkozás esetén a csomag egy egyszerű segítő funkciót tesz elérhetővé, amely a példákban is használatos, a következő módon:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Ez a metódus meghívja a konstruktort, hogy létrehozza a RelayedServer egy új példányát, majd Előfizeti a megadott visszahívást a "kapcsolat" eseményre.
 
##### <a name="relayedconnect"></a>relayedConnect

Egyszerűen tükrözheti az `createRelayedServer` helpert a függvényben, `relayedConnect` létrehozza az ügyfélkapcsolatot, és előfizet az eredményül kapott szoftvercsatornán található "nyitott" eseményre.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Elérhető Relay API-k](relay-api-overview.md)
