---
title: .NET Core és a Visual Studio használatával több függő szolgáltatás fut.
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: 66a08ad674477da478ec7037833fe4cb836f9bb0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357055"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Az Azure fejlesztési tárolóhelyek több szolgáltatásos fejlesztői

Ebben az oktatóanyagban megismerheti, hogyan szóközzel Azure fejlesztői, a fejlesztői, szóközök biztosító előnyökkel némelyike együtt több szolgáltatást alkalmazások fejlesztéséhez kell.

## <a name="call-another-container"></a>Egy másik tároló hívása
Ebben a szakaszban létrehoz egy második szolgáltatást fogjuk `mywebapi`, és rendelkezik `webfrontend` meghívására. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` projektet egy *különálló Visual Studio-ablakban*.
1. Válassza ki az **Azure Dev Spaces** lehetőséget az indítási beállítások legördülő listájából, ahogy korábban a `webfrontend` projekt esetében is tette. Egy új AKS-fürt létrehozása helyett most válassza ki a már létrehozottat. A korábbiakhoz hasonlóan hagyja a Tér értékét az alapértelmezett `default` értéken, és kattintson az **OK** gombra. A kimeneti ablakban, Észreveheti a Visual Studio elindul, és ez az új szolgáltatás a fejlesztési tárhelyre "ízelítőt" hibakereső indításakor dolgot felgyorsítása érdekében.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A művelet akkor fejeződik be, ha a Visual Studio állapotsora narancssárga színűre vált
1. Jegyezze fel a végpont URL-cím jelenik meg a **az aks-ben az Azure fejlesztési tárolóhelyek** ablaktábláján a **kimeneti** ablak. A következőhöz hasonlóan fog kinézni: `http://localhost:<portnumber>`. Úgy tűnhet, hogy a tároló futtatása helyileg történik, de valójában a Dev Spaces-térben fut az Azure-ban.
2. Ha a `mywebapi` elkészült, a `ValuesController` alapértelmezett GET API-jának meghívásához nyissa meg böngészőjét a localhost címen, és fűzze hozzá az `/api/values` elérési utat az URL-címhez. 
3. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól, amely az alábbihoz hasonlóan néz ki.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé. Váltson arra a Visual Studio-ablakra, amely a `webfrontend` projektet tartalmazza. Az a `HomeController.cs` fájl *cserélje le* névjegy mód a kódot az alábbira:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Látni fogja, hogyan később Ez lehetővé teszi a hatékonyabb fejlesztési környezetet biztosít [forgatókönyvek csapat](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot a `Controllers/ValuesController.cs` fájl `Get(int id)` metódusában, amely kezeli a `api/values/{id}` GET-kéréseket.
1. A `webfrontend` projektben, ahova a fenti kódot illesztette be, állítson be egy töréspontot, mielőtt az GET-kérést küldene a `mywebapi/api/values` számára.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben. A Visual Studio ismét megnyit egy böngészőablakot a localhost megfelelő portjához, és megjelenik a webalkalmazás.
1. Kattintson az oldal tetején lévő **About** (Információ) hivatkozásra a `webfrontend` projekt töréspontjának aktiválásához. 
1. Nyomja le az F10 billentyűt a folytatáshoz. A `mywebapi` projekt töréspontja aktiválódik.
1. Nyomja le az F5 billentyűt a folytatáshoz, és visszakerül a `webfrontend` projekt kódjához.
1. Nyomja le ismételten az F5 billentyűt a kérés teljesítéséhez. Ekkor betöltődik egy oldal a böngészőben. A web app alkalmazásban a névjegy lapra a két szolgáltatás által összefűzött üzenetet jelenít meg: "Hello webfrontend és Hello a mywebapi."

Remek! Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

### <a name="automatic-tracing-for-http-messages"></a>A HTTP-üzenetek automatikus nyomkövetés
Talán észrevette, hogy bár *webfrontend* nem tartalmaz semmilyen speciális kódot nyomtassa ki a HTTP-hívás adatbeolvasási az *mywebapi*, láthatja a HTTP-nyomkövetéseket üzenetek a kimeneti ablakban:
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
Ez az egy "ingyenes" fejlesztői, szóközök rendszerállapot kap. Összetevők, amelyek nyomon követik a HTTP-kéréseket, azok halad át a rendszer, hogy bonyolult, több szolgáltatásos hívások nyomon követheti a fejlesztés során könnyebben beszúrása azt.

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a fejlesztői, szóközök csoportos fejlesztése](team-development-netcore-visualstudio.md)
