---
title: 'Több függő szolgáltatás futtatása: .NET Core & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Ez az oktatóanyag bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy több szolgáltatásból álló .NET Core-alkalmazás hibakereséséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
ms.openlocfilehash: 0bbb1aefe517c45207160b83b89f7207e8909666
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75438318"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Több függő szolgáltatás futtatása: a .NET Core és a Visual Studio Code az Azure dev Spaces használatával

Ebből az oktatóanyagból megtudhatja, hogyan fejleszthet többszolgáltatásos alkalmazásokat az Azure dev Spaces használatával, valamint a dev Spaces által biztosított további előnyökkel.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második szolgáltatást fog létrehozni, `mywebapi`és `webfrontend` meghívja azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![Több tároló](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. Tudni fogja, hogy készen áll az *alkalmazás elindításakor. A leállításhoz nyomja le a CTRL + C* billentyűkombinációt. üzenet jelenik meg a hibakeresési konzolon.
1. A végpont URL-címe valahogy így fog kinézni: `http://localhost:<portnumber>`. **Tipp: a VS Code állapotsora narancssárga színűre vált, és egy kattintható URL-címet jelenít meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli Dev Spaces-terünkben fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
1. Ha a `mywebapi` elkészült, nyissa meg a böngészőben a localhost címét. Fűzze a `/api/values` sztringet az URL-címhez a `ValuesController`-hez tartozó alapértelmezett GET API meghívásához.
1. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé.
1. Váltson a `webfrontend` VS Code-ablakára.
1. *Cserélje le* a about metódus kódját a következőben `HomeController.cs`:

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

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később láthatja, hogyan segít ez a csapatok [együttműködésen alapuló fejlesztésben](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. A `Get(int id)` Get kéréseket kezelő `api/values/{id}` metóduson belül állítson be egy töréspontot. Ez [a *Controllers/ValuesController. cs* fájl 23. sorában](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23)található.
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `mywebapi/api/values` felé. Ez az előző szakaszban módosított [ *Controllers/HomeController. cs* fájl](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs) 32. sora.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Hívja meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban.
1. A webalkalmazás About (Információ) oldalán a két szolgáltatás által összefűzött üzenet jelenik meg: „Hello from webfrontend and Hello from mywebapi.”

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedjen meg a fejlesztői Spaces fejlesztőivel](team-development-netcore.md)
