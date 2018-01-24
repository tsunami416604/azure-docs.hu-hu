---
title: "Az Azure továbbítási csomópont API-k áttekintése |} Microsoft Docs"
description: "Továbbítják a csomópont API – áttekintés"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Továbbítják a hibrid kapcsolatok csomópont API – áttekintés

## <a name="overview"></a>Áttekintés

A [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) csomópont csomag Azure Relay a hibrid kapcsolatok épül, és kiterjeszti a ["ws"](https://www.npmjs.com/package/ws) NPM csomag. Ez a csomag újra exportálja az alapszintű csomag összes exportálását, és hozzáadja az új sablonként exportálja, amelyek lehetővé teszik az Azure-továbbító szolgáltatás hibrid kapcsolatok szolgáltatásával való integráció. 

A meglévő alkalmazásokat, amelyek `require('ws')` használhatja ezt a csomagot a `require('hyco-ws')` helyette, amely is lehetővé teszi hibrid környezetekben, ahol az alkalmazás figyelheti a WebSocket-kapcsolatokat "belül a tűzfal" a helyi és hibrid kapcsolatokon keresztül minden a egy időben.
  
## <a name="documentation"></a>Dokumentáció

Az API-jainak [részletes ismertetését lásd: a fő 'ws"csomag](https://github.com/websockets/ws/blob/master/doc/ws.md). Ez a cikk ismerteti, hogyan eltér az ezt a csomagot, hogy az alaptervhez. 

Az alapszintű csomag és a "hyco ws" közötti legfőbb különbségek az új kiszolgáló osztályt, keresztül exportált ad `require('hyco-ws').RelayedServer`, és néhány segédmódszereket.

### <a name="package-helper-methods"></a>Csomag segédmódszereket

Többféleképpen segédprogram érhető el a csomag export, melyeket referenciaként használhat az alábbiak szerint:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

A segédmódszereket használható ezzel a csomaggal, de a webszolgáltatás vagy az eszköz ügyfelek figyelői vagy feladók hozzon létre egy csomópont kiszolgáló is használható. A kiszolgáló úgy, hogy azok rövid élettartamú jogkivonatok beágyazása URI-azonosítók. ezeket a módszereket használja. Az URI-k is, hogy a beállítás HTTP-fejlécek nem támogatják a WebSocket-kézfogással közös WebSocket-verem használható. Engedélyezési jogkivonatok beillesztése az URI elsősorban e dokumentumtár-külső használati forgatókönyvek támogatott. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

A megadott névtér és elérési útja egy érvényes Azure Relay a hibrid kapcsolat figyelő URI hoz létre. Ezt az URI használható a WebSocketServer osztály a továbbító verziójával.

- `namespaceName`(szükséges) – a tartomány minősített név a Azure továbbítási névtér használatára.
- `path`(kötelező) – a neve egy létező Azure Relay a hibrid kapcsolat az adott névtérben.
- `token`(nem kötelező) – egy korábban kiadott továbbítási access token, amely a figyelő URI beágyazott (lásd a következő példát).
- `id`(nem kötelező) – olyan nyomkövetési azonosító, amely lehetővé teszi, hogy a végpont diagnosztikai nyomkövetési kérelmek.

A `token` értéket nem kötelező, és csak amikor nincs lehetőség küldeni HTTP-fejlécekkel együtt a WebSocket-kézfogás, mint a W3C WebSocket veremmel rendelkező esetében használható.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

A megadott névtér és elérési útja egy érvényes Azure Relay a hibrid kapcsolat küldési URI hoz létre. A WebSocket ügyfél ezt az URI használható.

- `namespaceName`(szükséges) – a tartomány minősített név a Azure továbbítási névtér használatára.
- `path`(kötelező) – a neve egy létező Azure Relay a hibrid kapcsolat az adott névtérben.
- `token`(nem kötelező) – egy korábban kiadott továbbítási access token, amely a küldési URI beágyazott (lásd a következő példát).
- `id`(nem kötelező) – olyan nyomkövetési azonosító, amely lehetővé teszi, hogy a végpont diagnosztikai nyomkövetési kérelmek.

A `token` értéket nem kötelező, és csak amikor nincs lehetőség küldeni HTTP-fejlécekkel együtt a WebSocket-kézfogás, mint a W3C WebSocket veremmel rendelkező esetében használható.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Létrehoz egy Azure továbbítási közös hozzáférésű Jogosultságkód (SAS) jogkivonatot adott cél URI Azonosítóját, a SAS-szabály és a SAS-szabály kulcs, amely érvényes a megadott számú másodpercig vagy az aktuális azonnali órán keresztül, ha a lejárati argumentum.

- `uri`(szükséges) – az URI Azonosítót, amelynek a token kell végezni. Az URI normalizálva van a HTTP protokollt használja, és a lekérdezési karakterlánc adatok van csíkozott.
- `ruleName`(szükséges) – SAS szabály neve vagy URI-Azonosítóhoz által képviselt entitás vagy a névtér URI gazdagépre utaló képviseli.
- `key`(szükséges) – a biztonsági Társítások szabály érvényes kulcs. 
- `expirationSeconds`(választható) - másodpercben, amíg a generált jogkivonat lejárati. Ha nincs megadva, az alapértelmezett érték 1 óra (3600).

A kibocsátott jogkivonat ruház megadott időtartama alatt a megadott SAS-szabály kapcsolódó jogosultságok.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ez a módszer akkor funkcionális szempontból egyenértékű az `createRelayToken` metódus dokumentált korábban, de a megfelelően hozzáfűzi a bemeneti URI jogkivonatot ad vissza.

### <a name="class-wsrelayedserver"></a>Osztály ws. RelayedServer

A `hycows.RelayedServer` osztály helyett a `ws.Server` osztály, amely a helyi hálózaton, de az Azure-továbbítási szolgáltatás figyel delegáltak nem figyel.

A két kompatibilis, ami azt jelenti, hogy egy meglévő alkalmazást, amely főleg szerződés a `ws.Server` osztály könnyen módosíthatja a továbbítón keresztüli verzióját használja. A fő különbség a konstruktorban, és a rendelkezésre álló lehetőségeket vannak.

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

A `RelayedServer` konstruktor támogatja a argumentumok, mint egy másik készletét a `Server`, mivel az nem egy önálló figyelő vagy egy meglévő HTTP-figyelő keretrendszer beilleszthető tudni. Nincsenek is kevesebb lehetőséget a WebSocket felügyeleti nagymértékben delegált a továbbítási szolgáltatás óta.

A konstruktor argumentumai:

- `server`(szükséges) – a teljes URI a hibrid kapcsolat neve a figyelésre, általában kialakítani WebSocket.createRelayListenUri() segédmetódus.
- `token`(szükséges) – Ez az argumentum rendelkezik egy korábban kiállított jogkivonat karakterlánc vagy egy visszahívási függvényt, amely a token karakterlánc beszerzése hívható. A visszahívási beállítás részesíti előnyben, mivel lehetővé teszi a jogkivonat megújítási.

#### <a name="events"></a>Események

`RelayedServer`példányok kibocsátás három események, amelyek lehetővé teszik, hogy a bejövő kérelmeket kezelnek, kapcsolatot és a hibát észleli. Elő kell fizetnie a `connect` esemény üzenetek kezeléséhez. 

##### <a name="headers"></a>fejlécek

```JavaScript 
function(headers)
```

A `headers` egy esemény jelenik meg a bejövő kapcsolatokat elfogadható, elküld az ügyfélnek a fejlécek módosítása engedélyezése előtt. 

##### <a name="connection"></a>kapcsolat

```JavaScript
function(socket)
```

Ha egy új WebSocket-kapcsolat elfogadható kibocsátott. Az objektum típusa nem `ws.WebSocket`, ugyanaz, mint az alapszintű csomag.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Ha az alapul szolgáló server bocsát ki a hibát, továbbítja azt itt.  

#### <a name="helpers"></a>Segítő

Egyszerűbbé teheti a továbbítón keresztüli kiszolgáló indításához, és azonnal fizessen elő a bejövő kapcsolatok, a csomag közzététele egy egyszerű segítő függvénynek, amely is szolgál a példákban az alábbiak szerint:

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

Ez a metódus meghívja a konstruktor a RelayedServer új példányának létrehozása, és majd előfizet a "kapcsolat" eseményhez megadott visszahívás.
 
##### <a name="relayedconnect"></a>relayedConnect

Egyszerűen tükrözést a `createRelayedServer` függvényben szereplő segítő `relayedConnect` kapcsolatot hoz létre ügyfél és a "Megnyitás" esemény az eredményül kapott szoftvercsatorna számítógépcsoportra fizetett elő.

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
Azure-továbbítási kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Rendelkezésre álló továbbítási API-k](relay-api-overview.md)
