---
title: Új jelentés létrehozása adatkészletből a Power BI-Munkaterületcsoportok |} A Microsoft Docs
description: A Power BI-Munkaterületcsoport jelentések mostantól a saját alkalmazásában egy adatkészletből hozható létre.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: dbfe8c98dce1879a2e0956fafba32ab6e608bfdb
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046654"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Új jelentés létrehozása adatkészletből a Power BI munkaterületi gyűjteményekkel

A Power BI-Munkaterületcsoport jelentések mostantól a saját alkalmazásában egy adatkészletből hozható létre.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A hitelesítési módszer a jelentések beágyazásáról hasonló. Hozzáférési jogkivonatok, amelyek adott adatkészlet alapján. A powerbi.com-on használt tokenek kibocsátott által az Azure Active Directory (AAD). A Power BI-Munkaterületcsoport jogkivonatok adják ki alkalmazását.

Egy beágyazott jelentés létrehozásakor a kiállított jogkivonatok egy adott adatkészlet. Jogkivonatok a beágyazási URL-címet az azonos elem a mindegyik rendelkezik egy egyedi token társítva kell lennie. Annak érdekében, hogy hozzon létre egy beágyazott jelentést *Dataset.Read és Workspace.Report.Create* hatókörök meg kell adnia a hozzáférési jogkivonatot.

## <a name="create-access-token-needed-to-create-new-report"></a>Új jelentés létrehozásához szükséges hozzáférési jogkivonat létrehozása

A Power BI munkaterületi gyűjtemények használata egy beágyazási token, amely HMAC-val aláírt JSON webes jogkivonatainak. A jogkivonatok van bejelentkezve a Power BI-Munkaterületcsoport a hozzáférési kulcsára. Beágyazási tokenek, alapértelmezés szerint, egy jelentés beágyazása egy alkalmazásba való csak olvasási hozzáférést biztosítanak. Beágyazási tokenek egy adott jelentéshez kibocsátott, és egy beágyazási URL-címet társítva kell lennie.

Hozzáférési jogkivonatokat a kiszolgálón kell létrehozni a hozzáférési kulcsokat használ a tokenek bejelentkezési/titkosításához. A hozzáférési jogkivonat létrehozása információkért lásd: [Authenticating and authorizing with és a Power BI-Munkaterületcsoportok](app-token-flow.md). Emellett áttekintheti a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metódust. Íme egy példa, Mi ez módon jelenik meg a Power bi-ban a .NET SDK használatával.

Ebben a példában az adathalmaz-azonosító, amely az új jelentés létrehozása a kívánt van. Is hozzá kell adnunk a a hatókörök *Dataset.Read és Workspace.Report.Create*.

A *PowerBIToken osztály* kell telepíteni a [Power BI fő NuGut csomagot](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.Core
```

**C#-kód**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Hozzon létre egy új üres jelentés

Annak érdekében, hogy létrehoz egy új jelentést, meg kell adni a konfigurációjának létrehozása. Ennek tartalmaznia kell a hozzáférési jogkivonatot, a embedURL és a datasetID, amelyet meg szeretnénk szemben a jelentés létrehozásához. Ehhez az szükséges, hogy telepítse a nuget [Power BI JavaScript csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). A embedUrl lesz, https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Használhatja a [JavaScript Jelentésbeágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/) funkció teszteléséhez. Hitelesítésikód-példák a különböző műveletek elérhető is biztosít.

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kódot**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Hívó *powerbi.createReport()* lehetővé teszi egy üres vászonból szerkesztési módban belül jelennek meg a *div* elemet.

![Új üres jelentés](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Új jelentések mentése

A jelentés nem jön létre, amíg meg nem hívja a **Mentés másként** műveletet. Ez a Fájl menüből, és a JavaScript-alapú elvégezhető.

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Új jelentés létrehozása után csak **Mentés másként** nevezzük. Miután menti, a vászon továbbra is megjeleníti az adatkészlet szerkesztési mód és a nem a jelentésben. Töltse be újra az új jelentést, mint bármilyen más jelentés kell.

![Menü - fájl mentése másként](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Az új jelentés betöltése

Annak érdekében, hogy kommunikáljanak az új jelentést szeretne beágyazhatja a ugyanúgy, mint az alkalmazás beágyazása egy hagyományos jelentésben, ami azt jelenti, egy új jogkivonatot kell kiállítani. kifejezetten az új jelentést, és majd hívja meg a beágyazási metódust.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Mentés automatizálását és a "mentett" esemény használatával egy új jelentés betöltése

Annak érdekében, hogy automatizálhatják a "Mentés másként" és az új jelentést, majd betöltéséhez használja az "mentett" esemény teheti. Ezt az eseményt a mentési művelet befejeződött, és adja vissza, egy Json-objektum, amely tartalmazza az új Jelentésazonosító, a jelentés neve, a régi Jelentésazonosító (ha volt ilyen), és ha a művelet volt a Mentés másként vagy menthet.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Segítségével automatizálja a folyamatot a "mentett" eseményt figyelni, az új Jelentésazonosító igénybe, az új jogkivonat létrehozása és az új jelentés beágyazása.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Lásd még

[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentések mentése](save-reports.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[A Power BI alapvető NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[A Power BI JavaScript-csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)