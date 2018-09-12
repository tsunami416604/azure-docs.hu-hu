---
title: Csoportos fejlesztés az Azure Dev Spaces, a .NET Core és a VS Code használatával | Microsoft Docs
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
ms.openlocfilehash: 602e2a691dfa150c2e8332cb6dca070dbdd57901
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162087"
---
# <a name="team-development-with-azure-dev-spaces"></a>Csoportos fejlesztés az Azure Dev Spaces használatával

Ezen oktatóanyagból megismerheti, hogyan használhat több Dev Spaces-teret a több különböző fejlesztési környezetben való párhuzamos munkavégzéshez, ahol a különböző munkafolyamatok külön Dev Spaces-terekben tarthatók egyazon fürtön.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![Több tároló](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A folyamat befejezését a VS Code hibakeresési ablakának megjelenése jelzi.
1. A végpont URL-címe valahogy így fog kinézni: http://localhost:\<portnumber\>. **Tipp: A VS Code-állapotsáv egy kattintható URL-címet jelenít meg.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli Dev Spaces-terünkben fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
1. Ha a `mywebapi` elkészült, nyissa meg a böngészőben a localhost címét. Fűzze a `/api/values` sztringet az URL-címhez a `ValuesController`-hez tartozó alapértelmezett GET API meghívásához. 
1. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé.
1. Váltson a `webfrontend` VS Code-ablakára.
1. *Cserélje le* az About metódushoz tartozó kódot:

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

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, hogy ez miként segíti a csapatot az együttműködésen alapuló fejlesztésben.

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot a `Get(int id)` metódussal, amely kezeli a `api/values/{id}` GET kéréseit.
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `mywebapi/api/values` felé.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Hívja meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban.
1. A webalkalmazás About (Információ) oldalán a két szolgáltatás által összefűzött üzenet jelenik meg: „Hello from webfrontend and Hello from mywebapi.”


Remek! Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

[!INCLUDE [](includes/team-development-1.md)]

Lássuk működés közben. Váltson a `mywebapi` VS Code-ablakára, és szerkessze a `string Get(int id)` metódus kódját, például:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```


[!INCLUDE [](includes/team-development-2.md)]

[!INCLUDE [](includes/well-done.md)]

[!INCLUDE [](includes/clean-up.md)]
