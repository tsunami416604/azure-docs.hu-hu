---
title: Node.js és a VS Code használatával több függő szolgáltatásokat futtató |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 11/21/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: f3dbe8c62cb1fcc0585b5abccc51a620ea713543
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666384"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Az Azure fejlesztési tárolóhelyek több szolgáltatásos fejlesztői

Ebben az oktatóanyagban megismerheti, hogyan szóközzel Azure fejlesztői, a fejlesztői, szóközök biztosító előnyökkel némelyike együtt több szolgáltatást alkalmazások fejlesztéséhez kell.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Mintakód megnyitása a *mywebapi* szolgáltatáshoz
Elvileg már rendelkeznie kell a `mywebapi` ezen útmutatóban használt mintakódjával a `samples` nevű mappában (ha még nem töltötte le, navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez.) Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A folyamat befejezését a VS Code hibakeresési ablakának megjelenése jelzi.
1. Jegyezze fel a végpont URL-címét, amely valahogy így fog kinézni: http://localhost:\<portnumber\>. **Tipp: A VS Code állapotsor kattintható URL-cím jelenik meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli fejlesztői környezetében fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
1. Ha a `mywebapi` elkészült, nyissa meg a böngészőben a localhost címét. Választ kell kapnia a `mywebapi` szolgáltatástól („Üdvözöljük a mywebapiban”).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé.
1. Váltson a `webfrontend` VS Code-ablakára.
1. Adja hozzá ezeket a kódsorokat a `server.js` fájl elejéhez:
    ```javascript
    var request = require('request');
    ```

3. *Cserélje le* a `/api` GET-kezelőhöz tartozó kódot. Kérés kezelésekor hívást indít a `mywebapi` felé, majd visszaadja az eredményeket mindkét szolgáltatásból.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
 4. *Távolítsa el* a `server.close()` sort a `server.js` végén

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez miként segíti a csapatot az együttműködésen alapuló fejlesztésben.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot az alapértelmezett GET `/` kezelőben.
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `http://mywebapi` felé.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Nyissa meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban. A webes alkalmazás megjelenít egy üzenetet, a két szolgáltatás által összefűzött: "Hello webfrontend és Hello a mywebapi."

### <a name="automatic-tracing-for-http-messages"></a>A HTTP-üzenetek automatikus nyomkövetés
Talán észrevette, hogy bár *webfrontend* nem tartalmaz semmilyen speciális kódot nyomtassa ki a HTTP-hívás adatbeolvasási az *mywebapi*, láthatja a HTTP-nyomkövetéseket üzenetek a kimeneti ablakban:
```
// The request from your browser
webfrontend.<id>.<region>.aksapp.io --hyh-> webfrontend:
   GET /api?_=1544485357627 HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend --1b1-> mywebapi:
   GET / HTTP/1.1

// Response from *mywebapi*
webfrontend <-1b1-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
webfrontend.<id>.<region>.aksapp.io <-hyh-- webfrontend:
   HTTP/1.1 200 OK
   Hello from webfrontend and Hello from mywebapi
```
Ez az egy "ingyenes" fejlesztői, szóközök rendszerállapot kap. Összetevők, amelyek nyomon követik a HTTP-kéréseket, azok halad át a rendszer, hogy bonyolult, több szolgáltatásos hívások nyomon követheti a fejlesztés során könnyebben beszúrása azt.

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a fejlesztői, szóközök csoportos fejlesztése](team-development-nodejs.md)