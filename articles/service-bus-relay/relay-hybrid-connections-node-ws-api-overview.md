---
title: Az Azure Relay Node API-k áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Relay szolgáltatás Node.js API-j. Azt is bemutatja, hogyan kell használni a hyco-ws Node csomagot.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514510"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Hibrid kapcsolatok csomópontapi-továbbítása – áttekintés

## <a name="overview"></a>Áttekintés

Az [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Azure Relay hibrid kapcsolatok csomópontcsomagja a ["ws"](https://www.npmjs.com/package/ws) NPM-csomagra épül, és kiterjeszti azokat. Ez a csomag újraexportálja az alapcsomag összes exportálását, és új exportálást ad hozzá, amely lehetővé teszi az Azure Relay szolgáltatás hibrid kapcsolatok funkciójával való integrációt. 

Meglévő alkalmazások, amelyek `require('ws')` használhatják ezt a `require('hyco-ws')` csomagot, hanem, amely lehetővé teszi a hibrid forgatókönyvek, amelyben egy alkalmazás figyelheti websocket-kapcsolatok helyileg "a tűzfalon belül" és a hibrid kapcsolatok, mindugyanabban az időben.
  
## <a name="documentation"></a>Dokumentáció

Az API-k at [a fő "ws" csomag dokumentálja.](https://github.com/websockets/ws/blob/master/doc/ws.md) Ez a cikk azt ismerteti, hogy ez a csomag miben különbözik az alapkonfigurációtól. 

A legfontosabb különbség az alapcsomag és a "hyco-ws" között az, `require('hyco-ws').RelayedServer`hogy hozzáad egy új kiszolgálóosztályt, amelyet a , és néhány segítő módszerrel exportál.

### <a name="package-helper-methods"></a>Csomagsegítő módszerek

A csomagexportáláson számos segédprogram érhető el, amelyekre a következőképpen hivatkozhat:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

A segítő módszerek ezzel a csomaggal használhatók, de egy csomópont-kiszolgáló is használhatja, amely lehetővé teszi a webes vagy eszközügyfelek számára figyelők vagy feladók létrehozását. A kiszolgáló ezeket a módszereket használja azáltal, hogy átadja nekik a rövid élettartamú jogkivonatokat beágyazó URI-kat. Ezek az URI-k olyan gyakori WebSocket-halmokkal is használhatók, amelyek nem támogatják a WebSocket-kézfogás HTTP-fejléceinek beállítását. Az engedélyezési jogkivonatok URI-ba való beágyazása elsősorban a külső könyvtár-külső használati forgatókönyvek támogatott. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Létrehoz egy érvényes Azure Relay hibrid kapcsolat figyelő URI a megadott névtér és elérési út. Ez az URI ezután használható a WebSocketServer osztály továbbító verziójával.

- `namespaceName`(kötelező) - az Azure Relay névtér tartományáltal minősített neve.
- `path`(kötelező) - egy meglévő Azure Relay hibrid kapcsolat neve az adott névtérben.
- `token`(nem kötelező) - egy korábban kiadott továbbítási hozzáférési jogkivonat, amely be van ágyazva a figyelő URI-ba (lásd a következő példát).
- `id`(nem kötelező) - egy nyomkövetési azonosító, amely lehetővé teszi a kérelmek végpontok között diagnosztika nyomon követését.

Az `token` érték nem kötelező, és csak akkor használható, ha nem lehet HTTP-fejléceket küldeni a WebSocket-kézfogással együtt, mint a W3C WebSocket verem esetében.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Létrehoz egy érvényes Azure Relay hibrid kapcsolat küldési URI-t a megadott névtérhez és elérési úthoz. Ez az URI bármely WebSocket-ügyféllel használható.

- `namespaceName`(kötelező) - az Azure Relay névtér tartományáltal minősített neve.
- `path`(kötelező) - egy meglévő Azure Relay hibrid kapcsolat neve az adott névtérben.
- `token`(nem kötelező) - egy korábban kiadott továbbítási hozzáférési jogkivonat, amely be van ágyazva a küldési URI-ba (lásd a következő példát).
- `id`(nem kötelező) - egy nyomkövetési azonosító, amely lehetővé teszi a kérelmek végpontok között diagnosztika nyomon követését.

Az `token` érték nem kötelező, és csak akkor használható, ha nem lehet HTTP-fejléceket küldeni a WebSocket-kézfogással együtt, mint a W3C WebSocket verem esetében.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Létrehoz egy Azure Relay megosztott hozzáférésű aláírás (SAS) jogkivonatot a megadott cél URI,SAS-szabály és SAS-szabálykulcshoz, amely a megadott számú másodpercig vagy az aktuális pillanattól számított egy órára érvényes, ha a lejárati argumentum nincs megadva.

- `uri`(kötelező) - az URI, amelyre a jogkivonatot ki kell adni. Az URI normalizálódik a HTTP-séma használatához, és a lekérdezési karakterlánc adatai ki vannak vonva.
- `ruleName`(kötelező) - SAS szabály neve vagy az adott URI által képviselt entitás, vagy az URI-állomás rész által képviselt névtér számára.
- `key`(kötelező) - érvényes kulcs a SAS szabályhoz. 
- `expirationSeconds`(nem kötelező) - a másodpercek száma, amíg a generált jogkivonat lejár. Ha nincs megadva, az alapértelmezett érték 1 óra (3600).

A kiadott jogkivonat a megadott SAS-szabályhoz kapcsolódó jogokat biztosítja az adott időtartamra.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ez a módszer funkcionálisan `createRelayToken` egyenértékű a korábban dokumentált módszerrel, de a bemeneti URI-hoz megfelelően hozzáfűzött jogkivonatot adja vissza.

### <a name="class-wsrelayedserver"></a>Ws osztály. RelayedServer

Az `hycows.RelayedServer` osztály egy alternatíva az `ws.Server` osztály, amely nem figyeli a helyi hálózaton, de delegálja az Azure Relay szolgáltatás figyelése.

A két osztály többnyire szerződés kompatibilis, ami `ws.Server` azt jelenti, hogy egy meglévő alkalmazás az osztály t használja a közvetített változat. A fő különbségek a kivitelező és a rendelkezésre álló lehetőségeket.

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

A `RelayedServer` konstruktor támogatja a `Server`különböző argumentumok, mint a , mert nem önálló figyelő, vagy be ágyazható egy meglévő HTTP-figyelő keretrendszer. Kevesebb lehetőség áll rendelkezésre, mivel a WebSocket-kezelés nagyrészt delegált a továbbító szolgáltatás.

Konstruktor argumentumai:

- `server`(szükséges) - a hibrid kapcsolat nevének teljesen minősített URI-ja, amelyen a figyelés általában a WebSocket.createRelayListenUri() helper metódussal épül fel.
- `token`(kötelező) - ez az argumentum egy korábban kiadott jogkivonat-karakterláncot vagy egy visszahívási függvényt tartalmaz, amely egy ilyen tokenkarakterlánc megszerzéséhez hívható. A visszahívási lehetőség előnyös, mivel lehetővé teszi a jogkivonat-megújítást.

#### <a name="events"></a>Események

`RelayedServer`a példányok három olyan eseményt bocsátanak ki, amelyek lehetővé teszik a bejövő kérések kezelését, a kapcsolatok létesítését és a hibafeltételek észlelését. Az üzenetek kezeléséhez `connect` elő kell fizetnie az eseményre. 

##### <a name="headers"></a>Fejlécek

```JavaScript 
function(headers)
```

Az `headers` esemény közvetlenül a bejövő kapcsolat elfogadása előtt következik be, lehetővé téve a fejlécek módosítását az ügyfélnek történő elküldéshez. 

##### <a name="connection"></a>kapcsolat

```JavaScript
function(socket)
```

Új WebSocket-kapcsolat elfogadásakor ad ki. Az objektum típusa `ws.WebSocket`megegyezik az alapcsomagéval.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Ha az alapul szolgáló kiszolgáló hibát bocsát ki, a rendszer itt továbbítja.  

#### <a name="helpers"></a>Segítők

A továbbított kiszolgáló indításának és a bejövő kapcsolatokra való azonnali előfizetésnek az egyszerűsítése érdekében a csomag egy egyszerű segítő funkciót mutat be, amelyet a példákban is használnak, az alábbiak szerint:

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

Ez a módszer meghívja a konstruktot, hogy hozzon létre egy új példányt a RelayedServer-ből, majd előfizet a megadott visszahívásra a "kapcsolat" eseményre.
 
##### <a name="relayedconnect"></a>relayedConnect

Egyszerűen tükrözve `createRelayedServer` a segítő `relayedConnect` a funkció, létrehoz egy ügyfél kapcsolatot, és előfizet a "nyitott" esemény a létrejövő aljzat.

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

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure Relayről, látogasson el az alábbi hivatkozásokra:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Elérhető relé API-k](relay-api-overview.md)
