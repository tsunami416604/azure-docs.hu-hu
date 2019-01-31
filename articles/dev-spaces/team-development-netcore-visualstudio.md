---
title: Csoportos fejlesztés az Azure Dev Spaces, a .NET Core és a Visual Studio használatával | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: ecacb7d3d4576b18eee3faf88c2a598d6acf94a0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465367"
---
# <a name="team-development-with-azure-dev-spaces"></a>Csoportos fejlesztés az Azure Dev Spaces használatával

Ezen oktatóanyagból megismerheti, hogyan használhat több Dev Spaces-teret a több különböző fejlesztési környezetben való párhuzamos munkavégzéshez, ahol a különböző munkafolyamatok külön Dev Spaces-terekben tarthatók egyazon fürtön.

## <a name="call-another-container"></a>Egy másik tároló hívása
Ebben a szakaszban egy második, `mywebapi` nevű szolgáltatást fog létrehozni, és a `webfrontend` használatával fogja hívni azt. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` projektet egy *különálló Visual Studio-ablakban*.
1. Válassza ki az **Azure Dev Spaces** lehetőséget az indítási beállítások legördülő listájából, ahogy korábban a `webfrontend` projekt esetében is tette. Egy új AKS-fürt létrehozása helyett most válassza ki a már létrehozottat. A korábbiakhoz hasonlóan hagyja a Tér értékét az alapértelmezett `default` értéken, és kattintson az **OK** gombra. A kimeneti ablakban észreveheti, hogy a Visual Studio elkezdi „beüzemelni” az új szolgáltatást a Dev Spaces-térben, ezzel elősegítve a gyorsabb végrehajtást a hibakeresés során.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. A művelet akkor fejeződik be, ha a Visual Studio állapotsora narancssárga színűre vált
1. Jegyezze fel a végpont URL-címét, amelyet a **kimeneti** ablak **Azure Dev Spaces for AKS** panelén láthat. A következőhöz hasonlóan fog kinézni: http://localhost:\<portnumber\>. Úgy tűnhet, hogy a tároló futtatása helyileg történik, de valójában a Dev Spaces-térben fut az Azure-ban.
2. Ha a `mywebapi` elkészült, a `ValuesController` alapértelmezett GET API-jának meghívásához nyissa meg böngészőjét a localhost címen, és fűzze hozzá az `/api/values` elérési utat az URL-címhez. 
3. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól, amely az alábbihoz hasonlóan néz ki.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé. Váltson arra a Visual Studio-ablakra, amely a `webfrontend` projektet tartalmazza. A `HomeController.cs` fájlban *cserélje le* az About metódus kódját az alábbira:

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

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Később látni fogja, ez hogyan segíti elő a produktívabb fejlesztési élményt a csapatmunkákban.

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

## <a name="learn-about-team-development"></a>A csapatban végzett fejlesztés bemutatása

Eddig úgy futtatta alkalmazása kódját, mintha Ön dolgozna egyedül fejlesztőként az alkalmazáson. Ebben a szakaszban megismerheti, hogyan teszi zökkenőmentessé az Azure Dev Spaces a csapatban végzett fejlesztést:
* Lehetővé teszi, hogy egy fejlesztőcsapat ugyanabban a környezetben dolgozhasson. Ez lehet egy megosztott Dev Spaces-tér, vagy igény szerint több különálló Dev Spaces-tér.
* Támogatja, hogy minden fejlesztő elszigetelten iterálhassa a saját kódját, miközben nem kell tartani attól, hogy mindeközben mások kódja működésképtelenné válik.
* Végpontok között lehet tesztelni a kódot a kód véglegesítése előtt, és nem szükséges utánzatokat létrehozni vagy függőségeket szimulálni.

### <a name="challenges-with-developing-microservices"></a>A mikroszolgáltatások fejlesztésének kihívásai
A mintaalkalmazása jelenleg nem túl összetett. A valóságban a fejlesztés során folyamatosan merülnek fel kihívások, ahogy egyre több szolgáltatást vesz fel, illetve ahogy nő a fejlesztői csapat.

Képzelje el, hogy egy olyan szolgáltatáson dolgozik, amely több tucat más szolgáltatással van kapcsolatban.

- Hamar életszerűtlenné válhat, hogy mindent helyileg futtasson a fejlesztéshez. Elképzelhető, hogy a fejlesztői gépen nincs elég erőforrás a teljes alkalmazás futtatásához. Az is lehetséges, hogy az alkalmazása olyan végpontokkal rendelkezik, amelyeknek nyilvánosan elérhetőnek kell lenniük (pl. az alkalmazása egy SaaS-alkalmazás webhookjának ad választ).
- Megpróbálhatja csak azokat a szolgáltatásokat futtatni, amelyektől függ az alkalmazás, de ehhez ismernie kell a függőségek teljes körét (például a függőségek függőségeit). Az is problémát jelenthet, hogy nehéznek bizonyul a függőségek felépítése és futtatása, mert korábban nem dolgozott rajtuk.
- Néhány fejlesztő ilyenkor szimulációba kezd, vagy utánzatokat készít a szolgáltatás függőségeiről. Ez néha segíthet, de az utánzatok kezelése hamarosan önmagában is saját fejlesztői erőforrásokat köthet le. Továbbá ez ahhoz vezethet, hogy a fejlesztői környezet eltér az éles üzemtől, és apró hibák jelenhetnek meg.
- Ebből következik, hogy mindenféle végpontok közötti tesztelés nehézkessé válik. Az integráció tesztelése a valóságban csak a véglegesítés után történhet meg, ami azt jelenti, hogy a fejlesztési ciklus későbbi szakaszaiban problémákat tapasztalhat.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Munka egy megosztott Dev Spaces-térben
Az Azure Dev Spaces segítségével beállíthat egy *megosztott* Dev Spaces-teret az Azure-ban. Minden fejlesztőnek csak az alkalmazásból rá eső részre kell koncentrálnia, és iterációs módszerrel fejlesztheti a *véglegesítés előtti kódot* egy olyan Dev Spaces-térben, amely már tartalmaz minden olyan szolgáltatást és felhőerőforrást, amelyektől az egyes forgatókönyvek függhetnek. A függőségek mindig naprakészek, a fejlesztők pedig mindig az éles környezetet tükröző módon dolgozhatnak.

### <a name="work-in-your-own-space"></a>Munkavégzés a saját térben
A szolgáltatás kódjának fejlesztése során, de még leadás előtt a kód sokszor nincs optimális állapotban. Az iteratív módszerrel újra és újra kell formálni, tesztelni kell, megoldásokkal kísérletezni. Az Azure Dev Spaces biztosítja a **tér** fogalmát, amely lehetővé teszi, hogy elszigetelten dolgozhasson, anélkül, hogy a többi csapattag munkájára kihatással lennének a fejlesztési munkálatok.

Az alábbiak elvégzésével meggyőződhet arról, hogy a `webfrontend` és a `mywebapi` szolgáltatás fut **a `default` Dev Spaces-térben** is.
1. Zárjon be minden F5/hibakeresési munkamenetet minden szolgáltatás esetében, de tartsa nyitva a projekteket saját Visual Studio-ablakukban.
2. Váltson arra a Visual Studio-ablakra, amely a `mywebapi` projektet tartalmazza, és nyomja le a Ctrl+F5 billentyűparancsot a szolgáltatás a hibakereső csatolása nélküli futtatásához.
3. Váltson arra a Visual Studio-ablakra, amely a `webfrontend` projektet tartalmazza, és nyomja le a Ctrl+F5 billentyűparancsot a futtatásához.

> [!Note]
> Néha frissíteni kell a böngészőlapot azt követően, hogy a weboldal először megjelenik a Ctrl+F5 lenyomása után.

Bárki, aki megnyitja a nyilvános URL-címet, és felkeresi a webalkalmazást, meghívja az Ön által megírt kódútvonalat, amely mindkét szolgáltatást az alapértelmezett `default` tér használatával futtatja. Tegyük fel, hogy szeretné folytatni a `mywebapi` fejlesztését. Hogyan teheti ezt meg úgy, hogy nem akadályozza a többi, a Dev Spaces-teret használó fejlesztőt? Ehhez be kell állítania a saját terét.

### <a name="create-a-new-dev-space"></a>Új Dev Spaces-tér létrehozása
A Visual Studióból létrehozhat további tereket, amelyek akkor lesznek használatban, ha a szolgáltatásában lenyomja az F5, illetve a Ctrl+F5 billentyűt. Olyan nevet adhat a tereknek, amilyet csak szeretne, és rugalmasan kezelheti a jelentését (pl.: `sprint4` vagy `demo`).

Új teret a következő módon hozhat létre:
1. Váltson arra a Visual Studio-ablakra, amely a `mywebapi` projektet tartalmazza.
2. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, majd válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **Hibakeresés** lehetőséget a bal oldalon az Azure Dev Spaces-beállítások megjelenítéséhez.
4. Itt módosíthatja vagy létrehozhatja azt a fürtöt és/vagy teret, amelyet a program akkor használ, ha lenyomja az F5, illetve a Ctrl+F5 billentyűt. *Győződjön meg róla, hogy a korábban létrehozott Azure Dev Spaces-tér van kiválasztva*.
5. A Tér legördülő listában válassza az **<Új tér létrehozása…>** lehetőséget.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. A **Tér hozzáadása** párbeszédablakban állítsa **alapértelmezett** értékre a szülőteret, majd adjon nevet az új térnek. Használhatja a saját nevét (pl.: „scott”) az új térhez, így munkatársai számára is könnyen beazonosítható a munkájához használt tér. Kattintson az **OK** gombra.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Most már láthatja az AKS-fürtöt és az új, a projekttulajdonságok oldalán kiválasztott teret.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Kód frissítése a *mywebapi*-hoz

1. A `mywebapi` projektben az alábbiak szerint végezheti el a kódmódosítást a `Controllers/ValuesController.cs` fájl `string Get(int id)` metódusában:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Állítson be egy töréspontot a frissített kódrészletben (elképzelhető, hogy korábban már megadott egyet).
3. Nyomja le az F5 billentyűt a `mywebapi` szolgáltatás elindításához. Ez a fürtben lévő szolgáltatást a kiválasztott teret használva indítja el. Ez a jelen esetben a `scott` névre hallgat.

Ez az ábra segít megérteni, hogyan működnek a különféle terek. A lila elérési útvonal egy, a `default` téren keresztül érkezett kérést mutat. Ez az alapértelmezett útvonal, ha nem előzi meg tér az URL-címet. A rózsaszín elérési útvonal egy, a `default/scott` téren keresztül érkezett kérést mutat.

![](media/common/Space-Routing.png)

Az Azure Dev Spaces e beépített funkciója lehetővé teszi, hogy végpontok között tesztelhesse a kódot egy megosztott környezetben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját terük teljes szolgáltatási vermét. Ez az útválasztás megköveteli, hogy a propagálási fejlécek továbbítva legyenek az alkalmazáskódban, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-running-in-the-defaultscott-space"></a>A `default/scott` téren futó kód tesztelése
A `mywebapi` új verzióját a `webfrontend`-del együtt úgy tudja tesztelni, hogy megnyitja böngészőjét a `webfrontend` nyilvános URL-címén (pl.: http://webfrontend.123456abcdef.eastus.aksapp.io)), és felkeresi az About (Információ) oldalt. Az eredeti, „Hello from webfrontend and Hello from mywebapi” üzenetnek kell megjelennie.

Most adja hozzá a „scott.s” kifejezést az URL-címhez. amelynek így kell kinéznie: http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io. Majd frissítse a böngészőoldalt. A `mywebapi` projektben beállított töréspontnak aktiválódnia kell. Nyomja le az F5-öt a folytatáshoz. A böngészőben megjelenik az új üzenet: „Hello from webfrontend and mywebapi now says something new.” Ennek az az oka, hogy a `mywebapi` frissített kódjának elérési útvonala a `default/scott` téren van futtatva.

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
