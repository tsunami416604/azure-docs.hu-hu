---
title: 'Több függő szolgáltatás futtatása: Node.js & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy több szolgáltatásból álló Node.js alkalmazások hibakereséséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 2c87dedda1db97a033526c809de735fe036120ef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006982"
---
# <a name="running-multiple-dependent-services-nodejs-and-visual-studio-code-with-azure-dev-spaces"></a>Több függő szolgáltatás futtatása: a Node.js és a Visual Studio Code az Azure dev Spaces-szel

Ebből az oktatóanyagból megtudhatja, hogyan fejleszthet többszolgáltatásos alkalmazásokat az Azure dev Spaces használatával, valamint a dev Spaces által biztosított további előnyökkel.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![Az ábrán a webfrontend szolgáltatás hívása látható (amelyet a nyíl jelez) a mywebapi szolgáltatás.](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Mintakód megnyitása a *mywebapi* szolgáltatáshoz
`mywebapi`Ehhez az útmutatóhoz már egy nevű mappában `samples` (ha nem, https://github.com/Azure/dev-spaces válassza a **klónozás vagy a letöltés** lehetőséget a GitHub-tárház letöltéséhez). Ennek a szakasznak a kódja a következő: `samples/nodejs/getting-started/mywebapi` .

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. Tudni fogja, hogy készen áll, amikor a 80-as *porton megfigyelt* üzenet megjelenik a hibakeresési konzolon.
1. Jegyezze fel a végpont URL-címét, amely valahogy így fog kinézni: `http://localhost:<portnumber>`. **Tipp: a VS Code állapotsora narancssárga színűre vált, és egy kattintható URL-címet jelenít meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli fejlesztői környezetében fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
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
   1. *Távolítsa el* a `server.close()` sort a `server.js` végén

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez miként segíti a csapatot az együttműködésen alapuló fejlesztésben.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot az alapértelmezett GET `/` Kezelőn belül a [ `server.js` 8. sorban ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `http://mywebapi` felé.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Nyissa meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban. A webalkalmazásban a két szolgáltatás által összefűzött üzenetnek kell megjelennie: „Üdvözöljük a webfrontendben és Üdvözöljük a mywebapiban.”

### <a name="well-done"></a>Szép munka!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedjen meg a fejlesztői Spaces fejlesztőivel](team-development-nodejs.md)
