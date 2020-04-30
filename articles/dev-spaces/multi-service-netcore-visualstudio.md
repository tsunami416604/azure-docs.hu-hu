---
title: 'Több függő szolgáltatás futtatása: .NET Core & Visual Studio'
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio egy több szolgáltatásból álló .NET Core-alkalmazás hibakereséséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 7f95c21c2cf5b7adcdb34d7bbe2b1f8314c20333
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75438406"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-with-azure-dev-spaces"></a>Több függő szolgáltatás futtatása: a .NET Core és a Visual Studio az Azure dev Spaces használatával

Ebből az oktatóanyagból megtudhatja, hogyan fejleszthet többszolgáltatásos alkalmazásokat az Azure dev Spaces használatával, valamint a dev Spaces által biztosított további előnyökkel.

## <a name="call-another-container"></a>Egy másik tároló hívása
Ebben a szakaszban egy második szolgáltatást fog létrehozni, `mywebapi`és `webfrontend` meghívja azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` projektet egy *különálló Visual Studio-ablakban*.
1. Válassza ki az **Azure Dev Spaces** lehetőséget az indítási beállítások legördülő listájából, ahogy korábban a `webfrontend` projekt esetében is tette. Egy új AKS-fürt létrehozása helyett most válassza ki a már létrehozottat. A korábbiakhoz hasonlóan hagyja a Tér értékét az alapértelmezett `default` értéken, és kattintson az **OK** gombra. A kimeneti ablakban észreveheti, hogy a Visual Studio elkezdi az új szolgáltatás üzembe helyezését a fejlesztői térben, hogy gyorsabban felgyorsítsa a hibakeresést.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A művelet akkor fejeződik be, ha a Visual Studio állapotsora narancssárga színűre vált
1. Jegyezze fel a végponti URL-címet, amely megjelenik a **kimeneti** ablak **Azure dev Spaces for AK** ablaktábláján. A következőhöz hasonlóan fog kinézni: `http://localhost:<portnumber>`. Úgy tűnhet, hogy a tároló futtatása helyileg történik, de valójában a Dev Spaces-térben fut az Azure-ban.
2. Ha a `mywebapi` elkészült, a `ValuesController` alapértelmezett GET API-jának meghívásához nyissa meg böngészőjét a localhost címen, és fűzze hozzá az `/api/values` elérési utat az URL-címhez. 
3. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól, amely az alábbihoz hasonlóan néz ki.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé. Váltson arra a Visual Studio-ablakra, amely a `webfrontend` projektet tartalmazza. A `HomeController.cs` fájlban *cserélje le* a about metódus kódját a következő kódra:

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

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez hogyan könnyíti meg a [csapatmunka](team-development-netcore-visualstudio.md)terén felmerülő fejlesztési élményt.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot a `Controllers/ValuesController.cs` fájl `Get(int id)` metódusában, amely kezeli a `api/values/{id}` GET-kéréseket.
1. A `webfrontend` projektben, ahova a fenti kódot illesztette be, állítson be egy töréspontot, mielőtt az GET-kérést küldene a `mywebapi/api/values` számára.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben. A Visual Studio ismét megnyit egy böngészőablakot a localhost megfelelő portjához, és megjelenik a webalkalmazás.
1. Kattintson az oldal tetején lévő **About** (Információ) hivatkozásra a `webfrontend` projekt töréspontjának aktiválásához. 
1. Nyomja le az F10 billentyűt a folytatáshoz. A `mywebapi` projekt töréspontja aktiválódik.
1. Nyomja le az F5 billentyűt a folytatáshoz, és visszakerül a `webfrontend` projekt kódjához.
1. Nyomja le ismételten az F5 billentyűt a kérés teljesítéséhez. Ekkor betöltődik egy oldal a böngészőben. A webalkalmazás About (Információ) oldalán a két szolgáltatás által összefűzött üzenet jelenik meg: „Hello from webfrontend and Hello from mywebapi.”

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedjen meg a fejlesztői Spaces fejlesztőivel](team-development-netcore-visualstudio.md)
