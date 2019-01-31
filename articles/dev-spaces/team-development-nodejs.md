---
title: Csoportos fejlesztés az Azure Dev Spaces és a VS Code használatával | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: 8a7200723b39c40fe9596bbab0acaf752acbbaf8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451393"
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

Remek! Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

[!INCLUDE [](../../includes/team-development-1.md)]

Most lássuk működés közben:
1. Váltson a `mywebapi` VS Code-ablakára, és szerkessze az alapértelmezett GET `/` kezelő kódját, például:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.

Most, hogy megismerte az Azure Dev Spacest, [megoszthatja a létrehozott Dev Spaces-teret egy csapattaggal](how-to/share-dev-spaces.md), és megmutathatja neki, milyen egyszerű az együttműködés.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```




