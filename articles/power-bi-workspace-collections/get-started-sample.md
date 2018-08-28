---
title: Ismerkedés egy minta segítségével
description: Ebben a cikkben a Power BI-Munkaterületcsoportok get elindított mintához nyomán meg.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: cd7da8d2bbd684df29432bb924bdae3cd9ac7e64
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044437"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Ismerkedés a Power BI-Munkaterületcsoportok minta

A **a Microsoft Power BI-Munkaterületcsoportok**, integrálható a Power BI-jelentéseket közvetlenül a webes vagy mobilalkalmazásaiba. Ebben a cikkben azt vezeti be, hogy a **Power BI-Munkaterületcsoportok** get elindított minta.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Mielőtt továbbmennénk, ahova menteni szeretné az alábbi forrásanyagokat: biztosítják, amikor a Power BI-jelentések integrálása a mintaalkalmazást, és a saját alkalmazások túl.

* [Munkaterület webes mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkId=761493)
* [A Power BI munkaterületi gyűjtemények API-referencia](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [A Power BI .NET SDK-val ](http://go.microsoft.com/fwlink/?LinkId=746472) (NuGet-n keresztül érhető el)
* [A jelentés JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Konfigurálhatja, és futtassa a Power BI-Munkaterületcsoportok első lépések minta, meg kell hoznia legalább egy **munkaterület-csoport** az Azure-előfizetésében. Megtudhatja, hogyan hozhat létre egy **munkaterület-csoport** tekintse meg az Azure Portalon [– első lépések a Power BI-Munkaterületcsoportok](get-started.md).

## <a name="configure-the-sample-app"></a>A mintaalkalmazás konfigurálása

Vegyük végig a Visual Studio fejlesztési környezet beállítása a mintaalkalmazás futtatásához szükséges összetevőinek eléréséhez.

1. Töltse le és csomagolja ki a [Power BI-Munkaterületcsoportok - jelentés integrálása egy webalkalmazásba](http://go.microsoft.com/fwlink/?LinkId=761493) mintát a Githubon.
2. Nyissa meg **Power bi-embedded.sln** a Visual Studióban. Szükség lehet végrehajtani a **-csomag** a NuGet Package Manager konzol frissítéséhez a csomagokat a megoldásban használt parancsot.
3. Hozza létre a megoldást.
4. Futtassa a **ProvisionSample** konzolalkalmazást. A mintaalkalmazás konzol egy munkaterület kiépítése és a PBIX-fájl importálása.
5. Kiépítéséhez egy új **munkaterület**, válassza ki az 1. lehetőség – **gyűjtési felügyeleti**, és válassza ki a lehetőséget 6, **új munkaterület kiépítése**
6. Egy új importálása **jelentés**, válassza ki a 2. lehetőség **jelentse a felügyeleti**, majd válassza ki a 3. lehetőség – **importálja. PBIX asztali fájl a munkaterületre**.

7. Adja meg a **munkaterület-csoport** nevét, és **hívóbetű**. Olvashatók be ezeket a **az Azure portal**. További információk beszerzése a **hozzáférési kulcs**, lásd: [Power BI API elérési kulcsainak megtekintése](get-started.md#view-power-bi-api-access-keys) a használatának első lépései a Microsoft Power BI Embedded.

    ![Hozzáférési kulcsok Azure Portalon](media/get-started-sample/azure-portal.png)
8. Másolja ki és mentse az újonnan létrehozott **munkaterület-Azonosítót** a cikk későbbi részében használni. Után a **munkaterület-Azonosítót** van létrehozva, akkor is megtalálhatják azt a **az Azure portal**.

    ![Munkaterület-Azonosítót az Azure Portalon](media/get-started-sample/workspace-id.png)
9. Azokat a PBIX-fájl importálása az **munkaterület**, lehetőséggel **6. Import PBIX Desktop-fájlt egy meglévő munkaterületet,**. Ha nem rendelkezik egy PBIX-fájl hasznos, letöltheti a [kiskereskedelmi elemzési minta pbix-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Ha a rendszer kéri, adjon meg egy rövid nevet a **adatkészlet**.

A következőhöz hasonló választ kell megjelennie:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Ha a PBIX-fájlt tartalmaz a közvetlen lekérdezés kapcsolatokat, futtassa a 7 lehetőséget, ha a kapcsolati karakterláncokat.

Importálja a Power BI PBIX jelentés van ezen a ponton a **munkaterület**. Most nézzük, hogyan futtathat a **Power BI-Munkaterületcsoportok** első lépésekhez mintául szolgáló webalkalmazást.

## <a name="run-the-sample-web-app"></a>A mintául szolgáló webalkalmazás futtatása

A web app minta egy mintaalkalmazás jeleníti meg az importált jelentések a **munkaterület**. Íme a webes alkalmazás minta konfigurálása.

1. Az a **Power bi embedded** Visual Studio-megoldás, kattintson a jobb gombbal a **EmbedSample** webalkalmazást, és válassza a **beállítás kezdőprojektként**.
2. A **web.config**, a a **EmbedSample** webalkalmazást, módosítsa a **appSettings**: **AccessKey**,  **WorkspaceCollection** nevét, és **munkaterület azonosítója**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Futtassa a **EmbedSample** webes alkalmazás.

Futtatása után a **EmbedSample** webalkalmazást, a bal oldali navigációs panelen tartalmaznia kell egy **jelentések** menü. Az importált jelentés megtekintéséhez bontsa ki a **jelentések**, és kattintson a jelentés. Ha importálta a [kiskereskedelmi elemzési minta pbix-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547), mintául szolgáló webalkalmazást a következő lenne:

![Minta bal oldali navigációs belül mintaalkalmazás](media/get-started-sample/sample-left-nav.png)

Miután rákattintott egy jelentés, a **EmbedSample** webalkalmazás kell kinéznie ezt:

![Mintajelentés megjelenítése az alkalmazásban](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Áttekintjük a mintakódot

A **a Microsoft Power BI-Munkaterületcsoportok** minta egy példa-webalkalmazást, amely bemutatja, hogyan integrálható a **Power BI** jelentéseket az alkalmazásba. A Model-View-Controller (MVC) tervezési minta használatával ajánlott eljárásokat mutatnak be. Ez a szakasz kiemeli a mintakód belül tárgyaljuk is részei a **Power bi embedded** webes alkalmazás megoldás. A Model-View-Controller (MVC) minta elkülöníti a tartományhoz, a bemutató és a felhasználói bevitel három különálló osztályokba alapján műveleteket modellezés: modell, a nézet és a vezérlő. MVC kapcsolatos további információkért lásd: [megismerheti az ASP.NET kapcsolatos](http://www.asp.net/mvc).

A **a Microsoft Power BI-Munkaterületcsoportok** mintakód a következő választja el. Minden szakasz a fájl nevét, hogy könnyen találhatja meg a kód a minta a Power bi-embedded.sln megoldás tartalmazza.

> [!NOTE]
> Ebben a szakaszban található egy összefoglaló, amely bemutatja, hogyan a kód írásának mintakódot. A teljes minta megtekintéséhez töltse be a Power bi-embedded.sln megoldást a Visual Studióban.

### <a name="model"></a>Modell

A minta egy **ReportsViewModel** és **ReportViewModel**.

**ReportsViewModel.cs**: Power BI-jelentések jelöli.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Power BI-jelentés jelöli.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Kapcsolati sztring

A kapcsolati karakterláncot a következő formátumban kell lennie:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Kiszolgáló és az adatbázis közös használatával attribútumok sikertelen. Például: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Nézet

A **nézet** kezeli a Power BI megjelenítésének **jelentések** és a egy Power BI **jelentés**.

**Reports.cshtml**: Ciklustevékenység **Model.Reports** hozhat létre egy **ActionLink**. A **ActionLink** tevődik össze a következőképpen történik:

| Rész | Leírás |
| --- | --- |
| Beosztás |A jelentés neve. |
| A lekérdezési karakterlánc |A jelentés azonosítója mutató hivatkozás |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Állítsa be a **Model.AccessToken**, és a Lambda kifejezésnek a **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Vezérlő

**DashboardController.cs**: egy PowerBIClient megadásának létrehoz egy **alkalmazás-jogkivonatára**. Egy JSON webes jogkivonat (JWT) hoz létre a **aláíró kulcs** beolvasni a **hitelesítő adatok**. A **hitelesítő adatok** segítségével hozzon létre egy példányt **PowerBIClient**. Ha már van egy példányát **PowerBIClient**, GetReports() és GetReportsAsync() hívása.

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


A feladat<ActionResult> jelentés (karakterlánc Jelentésazonosító)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Jelentés integrálása egy alkalmazásba

Miután egy **jelentés**, használhatja egy **IFrame** beágyazni a Power BI **jelentés**. Íme a powerbi.js a kódtöredék a **a Microsoft Power BI-Munkaterületcsoportok** minta.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Az alkalmazás a beágyazott szűrő jelentések

A beágyazott jelentések szűrését elvégezheti egy URL-cím szintaxisával. Ehhez adja hozzá a **$filter** lekérdezési karakterlánc paraméterrel egy **eq** az iFrame src URL-címre, meghatározott szűrőt tartalmazó operátor. A szűrő-lekérdezés szintaxisa a következő:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> A {tableName/fieldName} nem tartalmazhat szóközt és speciális karaktereket. A {fieldValue} egyetlen kategorikus értéket enged.  

## <a name="see-also"></a>Lásd még

[A Microsoft Power BI-Munkaterületcsoport gyakori helyzetek](scenarios.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Jelentés beágyazása](embed-report.md)  
[Új jelentés létrehozása adatkészletből](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
