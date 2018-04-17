---
title: Ismerkedés egy minta segítségével
description: Ebben a cikkben azt fogja megismerteti a Power BI munkaterület gyűjtemények get elindított minta.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 4daa675092bed1788025e7e3f7e7e11d02ef1671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Bevezetés a Power BI munkaterület gyűjtemények minta használatába

A **Microsoft Power BI munkaterület gyűjtemények**, akkor integrálható a Power BI-jelentéseket jobbra a webhelyen vagy az alkalmazásokat. Ebben a cikkben bemutatása után, hogy a **Power BI munkaterület gyűjtemények** get elindított minta.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Ahhoz, hogy nyissa meg a további, szeretné-e menteni a következőket: ezek segítséget a Power BI-jelentéseket integrációja a mintaalkalmazás és a saját alkalmazásai túl.

* [Munkaterület webes mintaalkalmazás](http://go.microsoft.com/fwlink/?LinkId=761493)
* [A Power BI munkaterület gyűjtemények API-referencia](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [A Power BI .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (NuGet keresztül érhető el)
* [JavaScript-jelentés beágyazása minta](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Mielőtt is beállíthat, és futtassa a Power BI munkaterület gyűjtemények első lépések minta, legalább egy létrehozásához szükséges **munkaterület-csoport** az Azure-előfizetésben. Hogyan hozzon létre egy **munkaterület-csoportok** az Azure portálon, lásd: [első lépések a Power BI munkaterület gyűjtemények](get-started.md).

## <a name="configure-the-sample-app"></a>A mintaalkalmazás konfigurálása

Bemutatjuk, keresztül érik el a mintaalkalmazás futtatásához szükséges összetevőket a Visual Studio fejlesztési környezet beállítása.

1. Töltse le és csomagolja ki a [Power BI munkaterület gyűjtemények - jelentés integrálásához webalkalmazás](http://go.microsoft.com/fwlink/?LinkId=761493) mintát a Githubon.
2. Nyissa meg **Power bi-embedded.sln** a Visual Studióban. Szükség lehet végrehajtani a **-csomag** a NuGet-Csomagkezelő konzol frissítéséhez a csomagokat a megoldásban használt parancsot.
3. Hozza létre a megoldást.
4. Futtassa a **ProvisionSample** konzolalkalmazást. A minta-Konzolalkalmazás munkaterület kiépíteni, és PBIX-fájl importálása.
5. Kiépítéséhez egy új **munkaterület**, válassza az 1. lehetőség – **gyűjtési felügyeleti**, és választhatja meg a 6., **új munkaterület kiépítése**
6. Importálhatja egy új **jelentés**, válassza ki a 2. lehetőség – **jelentést a felügyeleti**, majd válassza ki a 3. lehetőség – **pbix-fájlt asztali importálása fájlból egy munkaterület**.

7. Adja meg a **munkaterület-csoportok** nevét, és **hozzáférési kulcs**. Ezek kaphat a **Azure-portálon**. További információt az beszerzése a **a hozzáférési kulcsot**, lásd: [Power BI API elérési kulcsainak megtekintése](get-started.md#view-power-bi-api-access-keys) a Ismerkedés a Microsoft Power BI Embedded.

    ![Tárelérési kulcsok az Azure portálon](media/get-started-sample/azure-portal.png)
8. Másolja ki és mentse az újonnan létrehozott **munkaterület azonosítója** használatára a cikk későbbi részében. Után a **munkaterület azonosítója** van létrehozva, megtalálja a **Azure-portálon**.

    ![Munkaterületének Azonosítóját az Azure portálon](media/get-started-sample/workspace-id.png)
9. A PBIX-fájl importálása a **munkaterület**, a beállításnak a **6. Egy meglévő munkaterületre pbix-fájlt asztali importfájl**. Ha még nem rendelkezik a PBIX-fájl lesz szüksége, letöltheti a [kiskereskedelmi elemzési minta pbix-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Ha a rendszer kéri, adjon meg egy rövid nevet a **Dataset**.

A következőhöz hasonló választ kell megjelennie:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> A PBIX-fájl tartalmazza a közvetlen lekérdezés kapcsolatokat, ha futtatja a kapcsolót 7 a kapcsolati karakterláncok frissítésére.

Ezen a ponton rendelkezik egy Power BI pbix-fájlt a jelentés importálni a **munkaterület**. Most hogyan futtathat vizsgáljuk meg a **Power BI munkaterület gyűjtemények** elindított minta webalkalmazás beolvasása.

## <a name="run-the-sample-web-app"></a>A mintául szolgáló webalkalmazás futtatása

A webes alkalmazás minta egy jeleníti meg az importált jelentések mintaalkalmazás a **munkaterület**. Ez a webes alkalmazás minta konfigurálása.

1. Az a **Power bi embedded** Visual Studio megoldás, kattintson a jobb gombbal a **EmbedSample** webalkalmazást, és válassza a **beállítás kezdőprojektként**.
2. A **web.config**, a a **EmbedSample** webalkalmazást, módosítsa a **appSettings**: **AccessKey**,  **WorkspaceCollection** nevét, és **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Futtassa a **EmbedSample** webalkalmazást.

Miután lefuttatta a **EmbedSample** webalkalmazás, a bal oldali navigációs panelen tartalmaznia kell egy **jelentések** menü. Az importált jelentés megtekintéséhez bontsa ki a **jelentések**, és kattintson a jelentés. Ha importálta a [kiskereskedelmi elemzési minta pbix-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547), a minta webalkalmazás néz ki:

![A minta bal oldali navigációs belül mintaalkalmazás](media/get-started-sample/sample-left-nav.png)

Miután rákattintott egy jelentést, a **EmbedSample** webalkalmazás kell kinéznie ezt:

![A mintajelentés alkalmazáson belül megjelenítése](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>A mintakód felfedezés

A **Microsoft Power BI munkaterület gyűjtemények** minta egy példa-webalkalmazást, amely bemutatja, hogyan integrálható **Power BI** jelentések az alkalmazásba. Egy Model-View-Controller (MVC) tervezési mintát használ, ajánlott eljárások bemutatásához. Ez a szakasz a mintakódot, amely belül részei kiemeli a **Power bi embedded** web app megoldás. A Model-View-Controller (MVC) mintát elválasztja a tartományhoz, a megjelenítési és a műveletek a felhasználói bevitel három különálló osztályokba alapján modellezési: modell, megtekintése és vezérlő. MVC kapcsolatos további információkért lásd: [ismerje meg, az ASP.NET kapcsolatos](http://www.asp.net/mvc).

A **Microsoft Power BI munkaterület gyűjtemények** mintakód választja el az alábbiak szerint. Az egyes szakaszokon a fájl nevét, hogy könnyedén megtalálhatja a kód a minta a Power bi-embedded.sln megoldás tartalmazza.

> [!NOTE]
> Ez a szakasz a mintakód bemutatja, hogyan bejegyzésre kerültek-e a kód összegzését. A teljes minta megtekintéséhez töltse be a Power bi-embedded.sln megoldást a Visual Studióban.

### <a name="model"></a>Modell

A minta egy **ReportsViewModel** és **ReportViewModel**.

**ReportsViewModel.cs**: jelenti. a Power BI-jelentéseket.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: a Power BI-jelentés jelöli.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Kapcsolati karakterlánc

A kapcsolati karakterláncnak a következő formátumúnak kell lennie:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Közös server és adatbázis attribútumok sikertelen lesz. Például: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Nézet

A **nézet** kezeli a képernyőt a Power bi **jelentések** és a Power BI **jelentés**.

**Reports.cshtml**: ismétlés **Model.Reports** létrehozásához egy **ActionLink**. A **ActionLink** össze a következőképpen történik:

| Rész | Leírás |
| --- | --- |
| Beosztás |A jelentés nevét. |
| Lekérdezési karakterlánc |A jelentés azonosítója mutató hivatkozás |

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

Report.cshtml: Állítsa be a **Model.AccessToken**, és a Lambda kifejezést **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Vezérlő

**DashboardController.cs**: egy PowerBIClient sikeres létrehoz egy **alkalmazási jogkivonatának**. A JSON webes jogkivonat (JWT) jön létre a **aláírási kulcs** lekérni a **hitelesítő adatok**. A **hitelesítő adatok** példányának létrehozásához használt **PowerBIClient**. Ha egy példánya már **PowerBIClient**, GetReports() és GetReportsAsync() hívása.

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


A feladat<ActionResult> jelentés (karakterlánc reportId)

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

### <a name="integrate-a-report-into-your-app"></a>A jelentések integrálásához az alkalmazásba

Ha már van egy **jelentés**, használhat egy **IFrame** beágyazása a Power BI **jelentés**. Íme egy kódrészletet a a powerbi.js a **Microsoft Power BI munkaterület gyűjtemények** minta.

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

## <a name="filter-reports-embedded-in-your-application"></a>Az alkalmazásba beágyazandó szűrő jelentések

A beágyazott jelentések szűrését elvégezheti egy URL-cím szintaxisával. Ehhez az szükséges, hozzáadhat egy **$filter** lekérdezési karakterlánc paraméter egy **eq** az iFrame src URL-cím meghatározott szűrőt tartalmazó operátort. A szűrő lekérdezés szintaxisa a következő:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> A {tableName/fieldName} nem tartalmazhat szóközt és speciális karaktereket. A {fieldValue} egyetlen kategorikus értéket enged.  

## <a name="see-also"></a>Lásd még

[Microsoft Power BI-Munkaterületcsoport gyakori helyzetek](scenarios.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Jelentés beágyazása](embed-report.md)  
[Új jelentés létrehozása adatkészletből](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
