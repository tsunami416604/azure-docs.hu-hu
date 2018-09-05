---
title: Az Azure Relay csomópont API-k áttekintése |} A Microsoft Docs
description: Továbbítási csomópont API – áttekintés
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
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: bf0173f9c9802be689f7f3a893d381a251a2b16a
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701136"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Továbbítási hibrid kapcsolatok csomópont API – áttekintés

## <a name="overview"></a>Áttekintés

A [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) Azure Relay hibrid-kapcsolatokkal a Node package épül, és kiterjeszti a ['ws'](https://www.npmjs.com/package/ws) NPM-csomag. Ez a csomag újra exportálja az összes exportálásai alapszintű csomagot, és hozzáadja a integrációjának engedélyezése az Azure Relay hibrid kapcsolatok szolgáltatást az új exportálásához. 

Meglévő alkalmazások, amelyek `require('ws')` használhatja ezt a csomagot a `require('hyco-ws')` helyette, amely is lehetővé teszi, amelyben egy alkalmazás képes figyelni a WebSocket kapcsolatokat helyileg "belül a tűzfal" és a hibrid kapcsolatok használatával minden a hibrid forgatókönyvek egy időben.
  
## <a name="documentation"></a>Dokumentáció

Az API-k [részletes ismertetését lásd: a fő "ws" csomag](https://github.com/websockets/ws/blob/master/doc/ws.md). Ez a cikk bemutatja, hogyan Ez a csomag eltér-e alapterv. 

Az alapszintű csomag és a "hyco ws" lényeges különbség az, hogy hozzáad egy új kiszolgáló osztályt, amely keresztül van exportálva `require('hyco-ws').RelayedServer`, és néhány segédmetódusokat.

### <a name="package-helper-methods"></a>Csomag segédmetódusokat

Számos segédprogram-metódusokat érhető el a következő hivatkozási az erőforráscsomag exportálása:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

A segédmetódusokat használhatja a csomaggal, de a figyelők vagy feladók hozhat létre web- vagy eszköz ügyfelek engedélyezése egy csomópont-kiszolgáló is használható. A kiszolgáló ezen módszerek az URI-k, amelyek a beágyazási tokenek rövid ideig tartó átadásával őket. Az URI-k is használható, amelyek a beállítás HTTP-fejlécek nem támogatják a WebSocket-kézfogás a közös WebSocket-implementációt. Hitelesítési jogkivonatok beillesztése az URI-t elsősorban ezeket erőforrástár-külső használati forgatókönyvek esetén támogatott. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Létrehoz egy érvényes Azure Relay hibrid kapcsolati figyelő URI-t az adott névtér és az elérési út. Ez az URI majd WebSocketServer osztály a továbbító verziójával használható.

- `namespaceName` (kötelező) – a tartomány neve az Azure Relay-névtér használatára.
- `path` (kötelező) – egy meglévő Azure Relay hibrid kapcsolat, hogy a névtér nevét.
- `token` (nem kötelező) – egy korábban kiadott Relay hozzáférési jogkivonatát, a figyelő URI beágyazott (lásd az alábbi példát).
- `id` (nem kötelező) – a követési azonosító, amely lehetővé teszi a teljes körű diagnosztikai kérelmek nyomon követheti.

A `token` érték megadása nem kötelező, és csak ha az nem lehet elküldeni a HTTP-fejlécek együtt a WebSocket-kézfogás hasonlóan a W3C WebSocket hibával használható.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Létrehoz egy érvényes Azure Relay hibrid kapcsolat küldési URI-t az adott névtér és az elérési utat. Ez az URI minden WebSocket-ügyféllel használható.

- `namespaceName` (kötelező) – a tartomány neve az Azure Relay-névtér használatára.
- `path` (kötelező) – egy meglévő Azure Relay hibrid kapcsolat, hogy a névtér nevét.
- `token` (nem kötelező) – egy korábban kiadott Relay hozzáférési jogkivonatot, amely a küldési URI beágyazott (lásd az alábbi példát).
- `id` (nem kötelező) – a követési azonosító, amely lehetővé teszi a teljes körű diagnosztikai kérelmek nyomon követheti.

A `token` érték megadása nem kötelező, és csak ha az nem lehet elküldeni a HTTP-fejlécek együtt a WebSocket-kézfogás hasonlóan a W3C WebSocket hibával használható.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Létrehoz egy Azure Relay közös hozzáférésű Jogosultságkód (SAS) jogkivonatot a megadott cél URI-t, a SAS-szabály és a SAS-szabály kulcs, amely érvényes a megadott számú másodperc, vagy a jelenlegi azonnali egy órát, ha a lejárati argumentum esetén.

- `uri` (kötelező) – az URI-t, amelyhez a jogkivonatot kellene kibocsátani van. Az URI-t használja a HTTP sémát van normalizálva, és a lekérdezési karakterlánc adatai van eltávolítani.
- `ruleName` (kötelező) – SAS szabály neve vagy a megadott URI azonosító által jelölt entitás, vagy a névtér URI-t gazdagépre utaló képviseli.
- `key` (kötelező) – az SAS-szabály érvényes kulcs. 
- `expirationSeconds` (nem kötelező) – amíg a generált jogkivonat lejárata másodpercek számát. Ha nincs megadva, az alapértelmezett érték 1 óra (3600).

A kiállított jogkivonat ruház a megadott időtartama alatt a megadott SAS-szabályhoz társított jogosultságok.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Ez a módszer funkcionalitását tekintve megegyezik a `createRelayToken` metódus dokumentált korábban, de a megfelelően hozzáfűzi a bemeneti URI jogkivonatát adja vissza.

### <a name="class-wsrelayedserver"></a>Osztály ws. RelayedServer

A `hycows.RelayedServer` osztály történő alternatívája az `ws.Server` osztály, amely nem figyel a helyi hálózati, de figyeli az Azure Relay szolgáltatás delegáltak.

A két osztály a leginkább kompatibilis, ami azt jelenti, hogy egy meglévő alkalmazást a szerződés a `ws.Server` osztály könnyen módosítható a továbbítón keresztüli verzió használatához. A legfontosabb különbségek a következők: a konstruktorban, és az elérhető lehetőségek közül.

#### <a name="constructor"></a>A konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

A `RelayedServer` a konstruktor támogatja, mint argumentumok külön készletét a `Server`, mert nem egy önálló figyelő vagy egy meglévő HTTP-listener keretrendszer beilleszthető tudni. Is kevesebb lehetőség érhető el, mert a WebSocket felügyeleti nagymértékben delegálás útján megszerezte a továbbítási szolgáltatásnak.

A konstruktor argumentumai:

- `server` (kötelező) – a teljes URI a hibrid kapcsolat neveként figyelésre, általában épített WebSocket.createRelayListenUri() segédmetódus.
- `token` (kötelező) – Ez az argumentum tárolja egy korábban kiadott token karakterlánc vagy egy visszahívási függvény, amely nem hívható meg juthat hozzá az ilyen egy jogkivonat-karakterláncot. A visszahívási beállítást célszerű, mivel lehetővé teszi a token megújításának.

#### <a name="events"></a>Események

`RelayedServer` példányok gridre bocsáthatja ki három eseménynek, amely lehetővé teszi a bejövő kérések kezelésére, kapcsolatokat hozhat létre és hibaállapotok észlel. Elő kell fizetnie a `connect` eseményt üzenetek kezeléséhez. 

##### <a name="headers"></a>fejlécek

```JavaScript 
function(headers)
```

A `headers` egy esemény jelenik meg a bejövő kapcsolatokat elfogadnak, a fejlécek küldése az ügyfélnek módosításának engedélyezése előtt. 

##### <a name="connection"></a>kapcsolat

```JavaScript
function(socket)
```

Amikor egy új WebSocket csatlakozási kísérlet rendelkezésre. Az objektum típusú `ws.WebSocket`, ugyanaz, mint az alapszintű csomaggal.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Ha az alapul szolgáló server bocsát ki a hibát, továbbítja azt itt.  

#### <a name="helpers"></a>Segítők

A továbbítón keresztüli kiszolgáló indítása és a bejövő kapcsolatok azonnal előfizetés leegyszerűsítése a csomag tesz közzé egy egyszerű segítő függvény, amely szintén a példákban a következőképpen használja:

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

Hozzon létre egy új példányt a RelayedServer konstruktor meghívja ezt a módszert, és majd feliratkozik a megadott visszahívás a "kapcsolat" esemény.
 
##### <a name="relayedconnect"></a>relayedConnect

Egyszerűen tükrözést a `createRelayedServer` függvényben segítő `relayedConnect` ügyfél kapcsolatot hoz létre, és feliratkozik a "Megnyitás" esemény az eredményül kapott szoftvercsatorna.

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
Az Azure Relay kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:
* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Rendelkezésre álló Relay API-k](relay-api-overview.md)
