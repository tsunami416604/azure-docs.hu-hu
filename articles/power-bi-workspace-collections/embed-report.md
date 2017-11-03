---
title: "A jelentés beágyazása a Azure Power BI munkaterület gyűjtemények |} Microsoft Docs"
description: "Útmutató egy jelentést, amely a Power BI munkaterület gyűjtemények az alkalmazásba beágyazandó."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 56e7ca90132527c0ef9d4bd478e99b75ca055272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>A jelentés beágyazása a Power BI munkaterület gyűjtemények

Útmutató egy jelentést, amely a Power BI munkaterület gyűjtemények az alkalmazásba beágyazandó.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

How to ténylegesen jelentés beágyazása az alkalmazásba fog keresni. Ennek feltétele, hogy már rendelkezik egy jelentést, amely létezik egy munkaterület belül a munkaterület-csoportot. Ha a lépés még nem végzett, lásd: [Ismerkedés a Power BI munkaterület gyűjtemények](get-started.md).

A .NET (C#) vagy a Node.js SDK, JavaScript, valamint segítségével egyszerűen hozhatók létre az alkalmazás a Power BI munkaterület gyűjteményéhez.

## <a name="using-the-access-keys-to-use-rest-apis"></a>A tárelérési kulcsok segítségével REST API-k

Ahhoz, hogy a REST API hívása, adja át az elérési kulcsot, amely letölthető az Azure-portál a megadott munkaterület-csoport. További információkért lásd: [Ismerkedés a Power BI munkaterület gyűjtemények](get-started.md).

## <a name="get-a-report-id"></a>A jelentés azonosító beszerzése

Minden hozzáférési jogkivonat jelentés alapul. Szüksége lesz az adott jelentés azonosító beszerzése a jelentés beágyazása kívánt. Ezt megteheti a hívások a [jelentések lekérése](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API-t. Ezzel visszatér a azonosítója és a beágyazási URL-címet. Ez a Power BI .NET SDK használatával, vagy hívja közvetlenül a REST API-t is végezhető.

### <a name="using-the-power-bi-net-sdk"></a>A Power BI .NET SDK használatával

Ha a .NET SDK használatával kell jogkivonat-hitelesítő adat, amely az elérési kulcsot kap az Azure-portálon alapul létrehozása. Ehhez szükséges, hogy telepítse a [Power BI API NuGet-csomag](https://www.nuget.org/profiles/powerbi).

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.Api
```

**C#-kódban**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Közvetlenül a REST API felület meghívásakor

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Hozzon létre egy hozzáférési jogkivonatot:

A Power BI munkaterület gyűjtemények használata beágyazása jogkivonatok, amelyek HMAC aláírt JSON webes jogkivonatainak. A jogkivonatok az access-kulcsot a Power BI-Munkaterületcsoport vannak aláírva. Beágyazása a jogkivonatokat, alapértelmezés szerint, egy jelentés beágyazása egy alkalmazásba, csak olvasható hozzáférést biztosítja. Beágyazása jogkivonatok egy adott jelentés ki, és lehet társítva egy beágyazási URL-CÍMÉT.

Hozzáférési jogkivonatok a kiszolgálón kell létrehozni a tárelérési kulcsokat a jogkivonatok bejelentkezési/titkosítására használja. Olyan hozzáférési jogkivonatot létrehozásával kapcsolatos további információkért lásd: [Authenticating és engedélyezése a Power BI-munkaterület gyűjteményekkel](app-token-flow.md). Emellett áttekintheti a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metódust. Íme egy példa Mi ez alábbihoz hasonlóan fog kinézni a .NET SDK használatával a Power BI.

A jelentés Azonosítóját, amely korábban kapott használnia. A beágyazási jogkivonatot létrehozása után, majd használja a hozzáférési kulcs létrehozásához a jogkivonatot, melyekkel javascript szempontjából. A *PowerBIToken osztály* kell telepíteni a [Power BI Core NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.Core
```

**C#-kódban**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Jogkivonatok beágyazható engedélyhatókörök hozzáadása

Beágyazási jogkivonatok használata esetén előfordulhat, hogy korlátozni szeretné a hozzáférést az erőforrások felhasználásának. Emiatt a hatókörbe tartozó engedélyek jogkivonatot is létrehozhat. További információkért lásd: [hatókörök](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Beágyazása a JavaScript használatával

A hozzáférési jogkivonat és az után az azonosítója, azt is be a jelentést, JavaScript használatával. Ehhez szükséges, hogy telepítse a NuGet [Power BI JavaScript csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). A embedUrl https://embedded.powerbi.com/appTokenReportEmbed fog majd.

> [!NOTE]
> Használhatja a [JavaScript-jelentés beágyazása minta](https://microsoft.github.io/PowerBI-JavaScript/demo/) funkciók tesztelésére. A különböző műveletek elérhető kódpéldák is biztosít.

**NuGet-csomag telepítése**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kód**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>A beágyazott elemek méretének beállítása

A jelentés automatikusan beágyazni kívánt tárolója mérete alapján. Bírálja felül a beágyazott elem alapértelmezett méretét, egyszerűen adja hozzá egy CSS osztály attribútuma vagy beágyazott stílusok szélességét és magasságát.

## <a name="see-also"></a>Lásd még:

[Bevezetés a minta használatába](get-started-sample.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[A Power BI JavaScript-csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[A Power BI API NuGet-csomag](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[A csharp nyelvű Power bi Git-tárház](https://github.com/Microsoft/PowerBI-CSharp)  
[Power bi-csomópont Git-tárház](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
