---
title: Új jelentés létrehozása a Power BI munkaterület gyűjtemények egy adatkészletből |} Microsoft Docs
description: A Power BI-Munkaterületcsoport jelentések most hozhatók létre a saját alkalmazásban egy adatkészletből.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 5c6a52edd708b6077820f2004e83ac7dee945610
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>A Power BI munkaterület gyűjtemények egy adatkészletből új jelentés létrehozása

A Power BI-Munkaterületcsoport jelentések most hozhatók létre a saját alkalmazásban egy adatkészletből.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A hitelesítési módszert, valamint egy jelentés beágyazása hasonlít. A DataSet adatkészlet vonatkozó hozzáférési jogkivonatok alapul. A powerbi.com webhelyen használt jogkivonatok kiállított által Azure Active Directory (AAD). A Power BI-Munkaterületcsoport jogkivonatok saját alkalmazás által kiállított.

Egy beágyazott jelentés létrehozásakor a kiadott jogkivonatok egy adott adatkészlet. Jogkivonatok beágyazási URL-címet egy elemhez mindegyike rendelkezik egy egyedi token társítva kell lennie. Egy beágyazott jelentés létrehozásához *Dataset.Read és Workspace.Report.Create* hatókörök meg kell adni a hozzáférési jogkivonat.

## <a name="create-access-token-needed-to-create-new-report"></a>Új jelentés létrehozásához szükséges hozzáférési jogkivonat létrehozása

A Power BI munkaterület gyűjtemények használata egy beágyazási jogkivonatot, amely HMAC aláírt JSON webes jogkivonatainak. A jogkivonatok az access-kulcsot a Power BI-Munkaterületcsoport vannak aláírva. Beágyazása a jogkivonatokat, alapértelmezés szerint, egy jelentés beágyazása egy alkalmazásba, csak olvasható hozzáférést biztosítja. Beágyazása jogkivonatok egy adott jelentés ki, és lehet társítva egy beágyazási URL-CÍMÉT.

Hozzáférési jogkivonatok a kiszolgálón kell létrehozni a tárelérési kulcsokat a jogkivonatok bejelentkezési/titkosítására használja. Olyan hozzáférési jogkivonatot létrehozásával kapcsolatos további információkért lásd: [Authenticating és engedélyezése a Power BI-munkaterület gyűjteményekkel](app-token-flow.md). Emellett áttekintheti a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metódust. Íme egy példa Mi ez alábbihoz hasonlóan fog kinézni a .NET SDK használatával a Power BI.

Ebben a példában az adathalmaz-azonosító, amely az új jelentés létrehozása a szeretnénk van. Azt is meg kell adnia a a hatókörök *Dataset.Read és Workspace.Report.Create*.

A *PowerBIToken osztály* kell telepíteni a [Power BI Core NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.Core
```

**C#-kódban**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Üres új jelentés létrehozása

Ahhoz, hogy az új jelentés létrehozása a létrehozás konfigurációs kell megadni. Ennek tartalmaznia kell a hozzáférési jogkivonat, a embedURL és a datasetID kell létrehozni a jelentéskészítés vonatkozni fog. Ehhez szükséges, hogy telepítse a nuget [Power BI JavaScript csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). A embedUrl fog majd https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Használhatja a [JavaScript-jelentés beágyazása minta](https://microsoft.github.io/PowerBI-JavaScript/demo/) funkciók tesztelésére. A különböző műveletek elérhető kódpéldák is biztosít.

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kód**

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

Hívása *powerbi.createReport()* üres vászonból szerkesztési módban szerepelhet lehetővé teszi a *div* elemet.

![Új üres jelentés](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Új jelentések menthetők

A jelentés nem jön létre, amíg meg nem hívja a **Mentés másként** műveletet. A Fájl menüből, vagy JavaScript végezhető.

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
> A rendszer létrehoz egy új jelentést csak után **Mentés másként** nevezik. Miután menti, a vászonra továbbra is megjeleníti a dataset szerkesztési módban és nem a jelentésben. Töltse be újra az új jelentést, mint bármely más jelentés kell.

![Menü - fájl mentése másként](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Az új jelentés betöltése

Kommunikálni az új jelentést kell úgy, ahogy az alkalmazás beágyazza egy rendszeres jelentés beágyazása, ami azt jelenti, hogy egy új jogkivonatot kell kiállítani, kifejezetten az új jelentést a, és ezután hívja meg a beágyazási metódust.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Mentés automatizálhatja, és betölti az új jelentést a "mentett" esemény használatával

Ahhoz, hogy a "Mentés másként" automatizálásához és az új jelentést, majd betöltése biztosíthatja, hogy a "mentett" esemény használja. Ezt az eseményt a mentési művelet befejeződik, és adja vissza, egy Json-objektum, amely tartalmazza az új reportId, a jelentés nevét, a régi reportId (ha volt ilyen), és ha a művelet nem saveAs vagy mentse.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

A folyamat automatizálására figyelni a "mentett" esemény, az új reportId igénybe, az új jogkivonat létrehozása és az új jelentés beágyazása.

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
[A Power BI Core NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[A Power BI JavaScript-csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)