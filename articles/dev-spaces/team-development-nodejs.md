---
title: Csoportos fejlesztés az Azure Dev Spaces és a VS Code használatával | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
manager: douge
ms.openlocfilehash: b4c355c864f83bcd76c310fecb0f26dd3372e760
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162750"
---
# <a name="team-development-with-azure-dev-spaces"></a>Csoportos fejlesztés az Azure Dev Spaces használatával

Ezen oktatóanyagból megismerheti, hogyan használhat több Dev Spaces-teret a több különböző fejlesztési környezetben való párhuzamos munkavégzéshez, ahol a különböző munkafolyamatok külön Dev Spaces-terekben tarthatók egyazon fürtön.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Mintakód megnyitása a *mywebapi* szolgáltatáshoz
Elvileg már rendelkeznie kell a `mywebapi` ezen útmutatóban használt mintakódjával a `samples` nevű mappában (ha még nem töltötte le, navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez.) Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A folyamat befejezését a VS Code hibakeresési ablakának megjelenése jelzi.
1. Jegyezze fel a végpont URL-címét, amely valahogy így fog kinézni: http://localhost:\<portnumber\>. **Tipp: A VS Code-állapotsáv egy kattintható URL-címet jelenít meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli fejlesztői környezetében fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
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

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez miként segíti a csapatot az együttműködésen alapuló fejlesztésben.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot az alapértelmezett GET `/` kezelőben.
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `http://mywebapi` felé.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Nyissa meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban. A webalkalmazásban a két szolgáltatás által összefűzött üzenetnek kell megjelennie: „Üdvözöljük a webfrontendben és Üdvözöljük a mywebapiban.”

Remek! Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

[!INCLUDE [](includes/team-development-1.md)]

Most lássuk működés közben:
1. Váltson a `mywebapi` VS Code-ablakára, és szerkessze az alapértelmezett GET `/` kezelő kódját, például:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](includes/team-development-2.md)]

[!INCLUDE [](includes/well-done.md)]

[!INCLUDE [](includes/clean-up.md)]




