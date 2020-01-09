---
title: Váltás a jelentések megtekintési és szerkesztési módja között
description: Megtudhatja, hogyan válthat a jelentések megtekintési és szerkesztési módja között Power BI munkaterület-gyűjteményekben.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357664"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Váltás a nézet és a szerkesztési mód között Power BI munkaterület-gyűjtemények jelentéseihez

Megtudhatja, hogyan válthat a jelentések megtekintési és szerkesztési módja között Power BI munkaterület-gyűjteményekben.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

## <a name="creating-an-access-token"></a>Hozzáférési jogkivonat létrehozása

Létre kell hoznia egy hozzáférési jogkivonatot, amely lehetővé teszi a jelentések megtekintését és szerkesztését. A jelentések szerkesztéséhez és mentéséhez a **jelentés. READWRITE** -jogkivonat engedély szükséges. További információ: [Power bi munkaterület-gyűjtemények hitelesítése és engedélyezése](app-token-flow.md).

> [!NOTE]
> Ez lehetővé teszi egy meglévő jelentés módosításainak szerkesztését és mentését. Ha a **Mentés másként**funkciót is szeretné támogatni, további engedélyeket kell megadnia. További információ: [hatókörök](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Beágyazási konfiguráció

Ahhoz, hogy a Mentés gomb szerkesztési módban megjelenjen, meg kell adnia az engedélyeket és a viewMode. További információ: a [konfigurációs adatok beágyazása](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Például a JavaScriptben:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Ez azt jelzi, hogy a jelentés a modellekhez **viewMode** alapuló nézet módban ágyazható be **. ViewMode. View**.

## <a name="view-mode"></a>Megtekintési mód

A következő JavaScriptet használhatja a megtekintés módba váltáshoz, ha szerkesztési módban van.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Szerkesztési mód

A következő JavaScript használatával válthat szerkesztési módba, ha megtekintési módban van.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Lásd még:

[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI – CSharp git-tárház](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI – Node git-tárház](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)
