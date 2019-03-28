---
title: Jelentések mentése a Power BI-Munkaterületcsoportok |} A Microsoft Docs
description: Megtudhatja, hogyan menteni a Power BI-Munkaterületcsoportok jelentéseihez. Ehhez a megfelelő engedélyekkel ahhoz, hogy sikeresen működik.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bc3c9f71900abcbba440db259d92df53a4c7a586
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516450"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Jelentések mentése a Power BI munkaterületi gyűjteményekkel

Megtudhatja, hogyan menteni a Power BI-Munkaterületcsoportok jelentéseihez. Jelentések mentése van szükség a megfelelő engedélyekkel ahhoz, hogy sikeresen működik.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Belül a Power BI-Munkaterületcsoportok szerkesztheti a meglévő jelentések, és menti azokat. Is új jelentés létrehozása és mentése új jelentésként hozhat létre egyet.

Ahhoz, hogy a jelentést, először az adott jelentéshez token létrehozása a megfelelő hatókörökkel:

* Ahhoz, hogy mentse Report.ReadWrite hatókörét kötelező megadni.
* Engedélyezi a Mentés másként, Report.Read és Workspace.Report.Copy hatókörök szükségesek
* Mentés másként engedélyezéséhez Report.ReadWrite és Workspace.Report.Copy szükség

Jelölik, és engedélyezze a jobb save/save gombként meg kell adnia a beágyazás konfigurációjában a megfelelő engedéllyel a jelentés beágyazása a Fájl menüben:

* models.Permissions.ReadWrite
* modellek. Permissions.Copy
* modellek. Permissions.All

> [!NOTE]
> A hozzáférési jogkivonatot kell a megfelelő hatókörök is. További információkért lásd: [hatókörök](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Jelentés beágyazása a szerkesztési módban

Tegyük fel, szeretne jelentést beágyazni az alkalmazásban, csak adja át a megfelelő tulajdonságok beágyazási konfiguráció, és hívja powerbi.embed() szerkesztési módban. Adja meg az engedélyeket és a egy viewMode annak érdekében, hogy tekintse meg a mentési és a Mentés másként gombok szerkesztési módban. További információkért lásd: [beágyazási konfiguráció részletei](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Ha például a JavaScript:

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Mostantól egy jelentés beágyazott szerkesztési módban az alkalmazásban.

## <a name="save-report"></a>Jelentés mentése

Szerkesztési mód a megfelelő jogkivonattal és engedélyekkel a jelentés beágyazása, miután a Fájl menüből vagy a JavaScript-alapú mentheti a jelentést:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Mentés másként

```javascript
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Csak azután *Mentés másként* egy új jelentés létrehozása. A mentés után a vászon van továbbra is látható a régi jelentést a szerkesztési mód és az új jelentést. A létrehozott új jelentést beágyazni. Új hozzáférési jogkivonatot az új jelentés beágyazásához szükséges, jelentésenként létrehozott.

Kell betölteni az új jelentés után egy *Mentés másként*. Az új jelentés betöltése hasonlít bármilyen jelentés beágyazásához.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="see-also"></a>Lásd még

[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentés beágyazása](embed-report.md)  
[Új jelentés létrehozása adatkészletből](create-report-from-dataset.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)

