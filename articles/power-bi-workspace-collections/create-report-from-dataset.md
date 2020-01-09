---
title: Jelentés létrehozása adatkészletből – Power BI munkaterület-gyűjteményekből
description: A Power BI-munkaterületi jelentések mostantól létrehozhatók a saját alkalmazásbeli adatkészletből.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427063"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Új jelentés létrehozása adatkészletből Power BI munkaterület-gyűjteményekből

A Power BI-munkaterületi jelentések mostantól létrehozhatók a saját alkalmazásbeli adatkészletből.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A hitelesítési módszer hasonló a jelentés beágyazásához. Az adatkészletre jellemző hozzáférési jogkivonatokon alapul. A PowerBI.com használt jogkivonatokat a Azure Active Directory (HRE) állítja ki. Power BI munkaterület-gyűjtemény jogkivonatait a saját alkalmazás állítja ki.

Beágyazott jelentés létrehozásakor a kibocsátott tokenek egy adott adatkészlethez tartoznak. A jogkivonatokat ugyanahhoz az elemhez tartozó beágyazási URL-címhez kell társítani, hogy mindegyik egyedi jogkivonattal rendelkezzen. Beágyazott jelentés létrehozásához *adatkészlet. Read és Workspace. report. Create* hatóköröket kell megadni a hozzáférési jogkivonatban.

## <a name="create-access-token-needed-to-create-new-report"></a>Új jelentés létrehozásához szükséges hozzáférési jogkivonat létrehozása

Power BI munkaterület-gyűjtemények beágyazási tokent használnak, amely HMAC aláírt JSON webes jogkivonatokat használ. A tokenek a Power BI munkaterület-gyűjtemény hozzáférési kulcsával vannak aláírva. A beágyazási tokenek alapértelmezés szerint csak olvasási hozzáférést biztosítanak a jelentésekhez az alkalmazásba való beágyazáshoz. A beágyazási tokenek egy adott jelentéshez lettek kiadva, és egy beágyazási URL-címmel kell társítani őket.

A hozzáférési jogkivonatokat a kiszolgálón kell létrehozni, mivel a jogkivonatok aláírására/titkosítására a hozzáférési kulcsok használhatók. A hozzáférési jogkivonat létrehozásával kapcsolatos információkért lásd: [Power bi munkaterület-gyűjtemények hitelesítése és engedélyezése](app-token-flow.md). A [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metódust is ellenőrizheti. Íme egy példa arra, hogy ez hogyan fog kinézni a .NET SDK for Power BI használatával.

Ebben a példában az adatkészlet-azonosítót szeretnénk létrehozni az új jelentés létrehozásához. Emellett hozzá kell adnia az adatkészlet hatóköreit *. olvasás és munkaterület. jelentés. létrehozás*.

A *PowerBIToken osztályhoz* telepítenie kell az [Power bi Core NuGut csomagot](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-csomag telepítése**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#kód**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Új üres jelentés létrehozása

Új jelentés létrehozásához meg kell adni a létrehozási konfigurációt. Ennek tartalmaznia kell a hozzáférési jogkivonatot, a embedURL és azt a datasetID, amelyhez a jelentést létre kívánja hozni. Ehhez telepítenie kell a nuget [Power bi JavaScript-csomagot](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). A embedUrl csak https://embedded.powerbi.com/appTokenReportEmbed lesz.

> [!NOTE]
> A [JavaScript-jelentés beágyazási mintája](https://microsoft.github.io/PowerBI-JavaScript/demo/) használható a funkciók tesztelésére. Emellett az elérhető különböző műveletekhez is tartalmaz példákat.

**NuGet-csomag telepítése**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kód**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

A *powerbi. createReport ()* hívása egy üres vászonat tesz lehetővé szerkesztési módban, amely a *div* elemen belül jelenik meg.

![Új üres jelentés](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Új jelentések mentése

A jelentés addig nem jön létre, amíg meg nem hívja a **Mentés másként** műveletet. Ezt a fájl menüből vagy a JavaScriptből teheti meg.

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
> A rendszer csak a **Mentés másként** nevű új jelentést hozza létre. A mentés után a vászon továbbra is szerkesztési módban jeleníti meg az adatkészletet, nem pedig a jelentést. Újra kell töltenie az új jelentést, mint bármilyen más jelentést.

![Fájl menü – Mentés másként](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Az új jelentés betöltése

Ahhoz, hogy együttműködjön az új jelentéssel, a beágyazást ugyanúgy be kell ágyazni, ahogy az alkalmazás egy normál jelentést ágyaz be, ami azt jelenti, hogy az új jogkivonatot kifejezetten az új jelentéshez kell kiadni, majd meg kell hívnia a beágyazási módszert.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Új jelentés mentésének és betöltésének automatizálása a "Saved" esemény használatával

A "Mentés másként" folyamatának automatizálásához, majd az új jelentés betöltéséhez használhatja a "Saved" eseményt. Ez az esemény akkor aktiválódik, ha a mentési művelet befejeződött, és egy olyan JSON-objektumot ad vissza, amely tartalmazza az új Jelentésazonosító, a jelentés nevét, a régi Jelentésazonosító (ha van ilyen), és ha a művelet mentés vagy Mentés volt.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

A "Saved" esemény figyelésére szolgáló folyamat automatizálásához hajtsa végre az új Jelentésazonosító, hozza létre az új jogkivonatot, és ágyazza be az új jelentést.

```html
<div id="reportContainer"></div>
<script>
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
</script>
```

## <a name="see-also"></a>Lásd még:

[Bevezetés a minta használatába](get-started-sample.md)  
[Jelentések mentése](save-reports.md)  
[Jelentés beágyazása](embed-report.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI Core NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript-csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](https://community.powerbi.com/)
