---
title: Oktatóanyag – Többszolgáltatású webalkalmazás létrehozása, hibaelhárítása, üzembe helyezése és figyelése a Service Fabric Meshben | Microsoft Docs
description: Ebben az oktatóanyagban létrehozhat egy ASP.NET Core-webhelyet tartalmazó, háttér-webszolgáltatással kommunikáló, többszolgáltatású Azure Service Fabric mesh-szolgáltatást, valamint azon helyi hibakeresést végezhet, majd közzéteheti az Azure-on.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2053706aac2e6136e35e8574dcd19150fe3d3b6a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805426"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Oktatóanyag: Létrehozása, hibakeresése, üzembe helyezése és egy Service Fabric-háló több szolgáltatásos alkalmazás frissítése

Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan állíthat össze egy ASP.NET webes előtérrendszerrel és egy ASP.NET Core Web API háttérszolgáltatással rendelkező Service Fabric Mesh-alkalmazást a Visual Studióban. Ezt követően futtathatja az alkalmazás helyi hibakeresését a helyi fejlesztési fürtön. Az alkalmazást közzéteheti az Azure-on, majd konfigurációs és kódmódosításokat végezhet, és frissítheti az alkalmazást. Végül törölheti a nem használt Azure-erőforrásokat, így csak azért kell fizetnie, amit használ.

Miután elkészült, megismerkedett az alkalmazások életciklus-kezelésének legtöbb szakaszával, és létrehozott egy olyan alkalmazást, amely bemutatja, hogyan intézhet szolgáltatások közti hívást egy Service Fabric Mesh-alkalmazásban.

Ha nem szeretné manuálisan létrehozni a feladatlista-alkalmazást, akkor [letöltheti a forráskódot](https://github.com/azure-samples/service-fabric-mesh) a kész alkalmazáshoz, és folytathatja az [alkalmazás helyi hibakeresésével](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Egy ASP.NET webes előtérrendszerrel rendelkező Service Fabric mesh-alkalmazás létrehozása a Visual Studióval.
> * Egy feladatlista-elemeket megjelenítő modell létrehozása.
> * Egy háttérszolgáltatás létrehozása, majd adatok lekérése.
> * Vezérlő és DataContext környezet hozzáadása a háttérszolgáltatás Model-View-Controller elrendezésének részeként.
> * Weblap létrehozása a feladatlista elemeinek megjelenítéséhez.
> * A háttérszolgáltatást azonosító környezeti változók létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Service Fabric Mesh-alkalmazás létrehozása Visual Studióban
> * [Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Először [alakítsa ki a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) a Service Fabric futtatókörnyezet, az SDK, a Docker, és a Visual Studio 2017 telepítésével.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Service Fabric Mesh-projekt létrehozása Visual Studióban

Nyissa meg a Visual Studio alkalmazást, majd válassza a **File** (Fájl)  > **New** (Új)  > **Project** (Projekt) elemet

A **New Project** (Új projekt) párbeszédpanel fenti **Search** (Keresés) mezőjébe írja be a következőt: `mesh`. Válassza a **Service Fabric Mesh Application** (Service Fabric mesh-alkalmazás) sablont. Ha nem látja a sablont, győződjön meg róla, hogy telepítette a Mesh SDK-t és a VS-eszközök előzetes verzióját a [fejlesztési környezet beállításáról szóló szakaszban](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ismertetett módon.  

A **Name** (Név) mezőbe írja be a(z) `todolistapp` kifejezést, a **Location** (Hely) mezőben pedig adja meg a mappa elérési útját a projekt tárolandó fájljaihoz.

Ügyeljen rá, hogy bejelölje a **Create directory for solution** (Címtár létrehozása a megoldáshoz) jelölőnégyzetet, majd kattintson az **OK** gombra a Service Fabric mesh-projekt létrehozásához.

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Ezután megjelenik a **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanel.

### <a name="create-the-web-front-end-service"></a>A webes előtér-szolgáltatás létrehozása

A **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanelen válassza ki az **ASP.NET Core** projekttípust, és ügyeljen rá, hogy a **Container OS** (Tároló operációs rendszere) beállítás **Windows** értékre van állítva.

Állítsa a **Service Name** (Szolgáltatás neve) beállítást **WebFrontEnd** értékre. Az ASP.NET Core szolgáltatás létrehozásához kattintson az **OK** gombra.

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Ezután megjelenik az ASP.NET Core-webalkalmazás párbeszédpanel. Válassza a **Web Application** (Webalkalmazás) lehetőséget, majd kattintson az **OK** gombra.

![Visual Studio új ASP.NET Core-alkalmazás](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Most már rendelkezik egy Service Fabric mesh-alkalmazással. Következő lépésként hozza létre a modellt a feladatlista-adatokhoz.

## <a name="create-the-to-do-items-model"></a>A feladatlista-modell létrehozása

Az egyszerűség kedvéért a feladatlista elemei egy memórialistában találhatók. Hozzon létre egy osztálytárat a feladatlista elemeihez, valamint egy listát, amelyben tárolhatja őket. A Visual Studióban, amely jelenleg rendelkezik a **todolistapp** betöltése megoldás kiválasztása **fájl** > **Hozzáadás** > **új projekt** .

Az a **új projekt hozzáadása** párbeszédpanel **keresési** tetején típus `C# .net core class`. Válassza aki a **Class Library (.NET Core)** (Osztálytár [.Net Core]) sablont.

A **Name** (Név) mezőbe írja a következőt: `Model`. Az osztálytár létrehozásához kattintson az **OK** gombra.

A Megoldáskezelő **Model** (Modell) területén kattintson a jobb gombbal a **Class1.cs** elemre, és válassza a **Rename** (Átnevezés) lehetőséget. Nevezze át az osztályt **ToDoItem.cs** névre. Amikor megjelenik egy jelenített meg nevezze át az összes hivatkozást, kattintson a **Igen**.

Az üres `class ToDoItem` tartalmát cserélje a következőre:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Ez az osztály a Tennivalólista elemein jelenti.

A feladatelemek listájának létrehozásához a Visual Studióban kattintson a jobb gombbal a **Model** (Modell) osztálytárra, majd az **Add** > **Class...** (Hozzáadás > Osztály...) lehetőségre. Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel. A **Name** (Név) tulajdonságot állítsa `ToDoList.cs` értékre, és kattintson az **Add** (Hozzáadás) lehetőségre.

A **ToDoList.cs** elemben található üres `class ToDoList` tartalmát cserélje a következőre:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Ezután hozza létre a feladatlista elemeit követő Service Fabric-szolgáltatást.

## <a name="create-the-back-end-service"></a>A háttérszolgáltatás létrehozása

A Visual Studio **Solution Explorer** (Megoldáskezelő) ablakában kattintson a jobb gombbal a **todolistapp** elemre, majd az **Add** > **New Service Fabric Service...** (Hozzáadás > Új Service Fabric-szolgáltatás...) lehetőségre

Ekkor megjelenik a **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanel. Válassza ki az **ASP.NET Core** projekttípust, és ügyeljen rá, hogy a **Container OS** (Tároló operációs rendszere) beállítás **Windows** értékre van állítva. Állítsa a **Service Name** (Szolgáltatás neve) beállítást **ToDoService** értékre. Az ASP.NET Core szolgáltatás létrehozásához kattintson az **OK** gombra.

Ezután megjelenik az **New ASP.NET Core Web Application** (Új ASP.NET Core-webalkalmazás) párbeszédpanel. A párbeszédpanelen válassza ki a **API-t**, majd kattintson az **OK** gombra. A szolgáltatás projektje ekkor a szolgáltatás részévé válik.

![Visual Studio új ASP.NET Core-alkalmazás](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Mivel a háttérszolgáltatáshoz nem tartozik felhasználói felület, kapcsolja ki a szolgáltatás indulásakor elinduló böngésző beállítást. A **Solution Explorer** (Megoldáskezelő) ablakban kattintson a jobb gombbal a **ToDoService** elemre, és válassza a **Properties** (Tulajdonságok) lehetőséget. A megjelenő ablakban kattintson a bal oldali **Debug** (Hibakeresés) lapra, és törölje a jelölést a **Launch browser** (Böngésző indítása) jelölőnégyzetből. A módosítás mentéséhez nyomja le a **Ctrl+S** billentyűkombinációt.

Mivel ez a szolgáltatás tartja fenn a feladatlista adatait, hozzá kell adnia egy referenciát a Model osztálytárjához. A Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **ToDoService** elemre, majd válassza az **Add** > **Reference...** (Hozzáadás > Referencia...) lehetőséget. Ekkor megjelenik a **Reference Manager** (Referenciakezelő) párbeszédpanel.

A **Reference Manager** párbeszédpanelen jelölje be a **Model** jelölőnégyzetét, majd kattintson az **OK** gombra.

### <a name="add-a-data-context"></a>Adatkörnyezet hozzáadása

Ezt követően hozzon létre egy olyan adatkörnyezetet, amely koordinálja az adatmodell adatainak kiszolgálását.

Az adatkörnyezet osztályának hozzáadásához a Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **ToDoService** elemre, majd válassza az **Add** > **Class** (Hozzáadás > Osztály) lehetőséget.
A megjelenő **Add New Item** (Új elem hozzáadása) párbeszédpanelen ügyeljen rá, hogy ki van jelölve a **Class** (Osztály) elem, és állítsa a **Name** (Név) elemet `DataContext.cs` értékre, végül kattintson az **Add** (Hozzáadás) lehetőségre.

A **DataContext.cs** elemben található üres `class DataContext` tartalmát cserélje a következőre:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Ez a minimális adatkörnyezet feltölt néhány minta feladatlista-elemet, és hozzáférést biztosít hozzájuk.

### <a name="add-a-controller"></a>Vezérlő hozzáadása

A **ToDoService** projekt létrehozásakor a sablon megadott egy alapértelmezett vezérlőt, amely a HTTP-kérelmeket kezeli, és a HTTP-válaszokat létrehozza. A **Solution Explorer** (Megoldáskezelő) **ToDoService** területén nyissa meg a **Controllers** (Vezérlők) mappát, amelyben megjelenik a **ValuesController.cs** fájl. 

Kattintson a jobb gombbal **ValuesController.cs** elemre, majd a **Rename** (Átnevezés) lehetőségre. Nevezze át a fájlt a következőre: `ToDoController.cs`. Ha egy felugró üzenet megkérdezi, hogy minden referenciát át szeretne-e nevezni, válassza a **Yes** (Igen) lehetőséget.

Nyissa meg a **ToDoController.cs** fájlt, és cserélje le a(z) `class ToDoController` tartalmát a következőre:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Ez az oktatóanyag nem tartalmazza a hozzáadásról, törlésről és egyéb műveletekről szóló ismertetőket, így az egyéb szolgáltatásokkal való kommunikáció maradhat a középpontban.

## <a name="create-the-web-page-that-displays-to-do-items"></a>A feladatlista elemeit megjelenítő weblap létrehozása

A háttérszolgáltatás implementálása után kódolnia kell a feladatlista elemeit megjelenítő webhelyet. A következő lépések a **WebFrontEnd** projektben végezendők el.

A feladatlista elemeit megjelenítő weblapnak hozzáférésre van szüksége a **ToDoItem** osztályhoz és listához.
A **Solution Explorerben** adjon hozzá egy referenciát a Model projekthez. Ehhez kattintson a jobb gombbal a **WebFrontEnd** elemre, majd válassza az **Add** > **Reference...** (Hozzáadás > Referencia...) lehetőséget Ekkor megjelenik a **Reference Manager** (Referenciakezelő) párbeszédpanel.

A **Reference Manager** (Referenciakezelő) párbeszédpanelen jelölje be a **Model** jelölőnégyzetét, majd kattintson az **OK** gombra.

A **Solution Explorerben** nyissa meg az Index.cshtml lapot a **WebFrontEnd** > **Pages** > **Index.cshtml** elemre való navigálással. Nyissa meg az **Index.cshtml-t**.

Cserélje le a teljes fájl tartalmát a következő HTML-re, amely egy egyszerű, a feladatlista elemeit megjelenítő táblázatot definiál:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Kattintson a legördülő listában az ikonra a **Index.cshtml** fájlt a **Megoldáskezelőben** és nyissa meg **Index.cshtml.cs**.

Az **Index.cshtml.cs** tetején adja hozzá a következőt: `using System.Net.Http;`

A(z) `public class IndexModel` tartalmát cserélje a következőre:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Környezeti változók létrehozása

A háttérszolgáltatás URL-címére szükség van a szolgáltatással való kommunikációhoz. A jelen oktatóanyag esetében a következő kódrészlet (amely fent az IndexModel részeként lett definiálva) a környezeti változókat beolvasva állítja elő az URL-t:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Az URL-cím a szolgáltatás nevéből és a portból áll. Ezek az adatok megtalálhatók a **ToDoService** projekt service.yaml fájljában.

> [!IMPORTANT]
> A következő lépésekben YAML-fájlok módosítva lesz.
> A service.yaml fájl változóit szóközökkel, és nem tabulátorokkal kell behúzni, különben nem fognak működni. Előfordulhat, hogy a Visual Studio beszúr tabulátorokat a környezeti változók létrehozásakor. Ezeket cserélje szóközökre. Bár a hibák a látni fogja a **hozhat létre** hibakeresési kimenet, az alkalmazás továbbra is elindul, de azt nem, amíg a lapok átalakítása szóközöket, és építse újra. Annak érdekében, hogy ne maradjanak tabulátorok a service.yaml fájlban, láthatóvá teheti a térközöket a Visual Studio szerkesztőben az **Edit**  > **Advanced**  > **View White Space** (Szerkesztés > Speciális > Térközök megjelenítése) lehetőséggel.
> Vegye figyelembe, hogy service.yaml fájlok feldolgozása az angol területi beállítással történik. Ha decimális elválasztójelként használja van szüksége, használja időszak helyett egy vesszővel tagolt például.

A **Solution Explorerben** lépjen a **ToDoService** projektre, és nyissa meg a **Service Resources** > **service.yaml** (Szolgáltatás-erőforrások > service.yaml) fájlt.

![1. ábra – A ToDoService service.yaml fájlja](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* A szolgáltatásnév `ToDoService`, található `services:` (1) lásd a fenti ábrán.
* A port `80`, található `endpoints:` (2) lásd a fenti ábrán. A projekt portszám valószínűleg eltérő lesz.

Következő lépésként környezeti változókat, amely a szolgáltatás nevét és portszámát a WebFrontEnd projektet, a háttér-szolgáltatás meghívására alkalmas.

A **Solution Explorerben** lépjen a **WebFrontEnd** > **Service Resources** > **service.yaml** (WebFrontEnd > Szolgáltatás-erőforrások > service.yaml) területre a háttérszolgáltatás címét meghatározó változók definiálásához.

A service.yaml fájlban adja hozzá a következő változók alapján `environmentVariables:` (, először távolítsa el a `#` állítsa vissza a `environmentVariables:`) a szóközök fontos így igazítása a változók hozzáadása alatt a többi változókkal `environmentVariables:`. Nagyon fontos, hogy ApiHostPort értéke megfelel a port értékének ToDoServiceListener, amely korábban jelent a ToDoService service.yaml fájljában.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Kétféleképpen értéket adja meg a `ToDoServiceName`: 
> - Csak a szolgáltatás nevét, amely feloldja is a Windows 10- és hibakeresési forgatókönyvet, amikor telepíti a szolgáltatást, az Azure Service Fabric-háló.
> - Teljesen minősített servicename.appname. Ez csak akkor fog működni, ha a Windows 10-hibakeresés.
> Tanácsos csak a szolgáltatás névfeloldási szolgáltatás a nevet használja.

A **WebFrontEnd** projekt **service.yaml** fájljának ehhez hasonlónak kell lennie, bár a(z) `ApiHostPort` érték az Ön esetében valószínűleg eltérő lesz:

![A service.yaml a WebFrontEnd projektben](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Most már készen áll a Service Fabric mesh-alkalmazás, valamint a háttér-webszolgáltatás fejlesztésére és a helyi fürtön való üzembe helyezésére.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Egy ASP.NET webes előtérrendszerrel rendelkező Service Fabric Mesh-alkalmazás létrehozása.
> * Egy feladatlista-elemeket megjelenítő modell létrehozása.
> * Egy háttérszolgáltatás létrehozása, majd adatok lekérése.
> * Vezérlő és DataContext környezet hozzáadása a háttérszolgáltatás Model-View-Controller elrendezésének részeként.
> * Weblap létrehozása a feladatlista elemeinek megjelenítéséhez.
> * A háttérszolgáltatást azonosító környezeti változók létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)