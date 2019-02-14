---
title: Az Azure Power BI-Munkaterületcsoportok-jelentés beágyazása |} A Microsoft Docs
description: Ismerje meg, hogyan ágyazhat be egy jelentést, amely a Power BI munkaterületi gyűjtemények az alkalmazásba.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 7a83a56a63fbd8a74e6a7e6d6f5158e3b5a8c2a4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232729"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Jelentés beágyazása Power BI munkaterületi gyűjteményekkel

Ismerje meg, hogyan ágyazhat be egy jelentést, amely a Power BI munkaterületi gyűjtemények az alkalmazásba.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Áttekintjük, hogyan lehet valójában egy jelentés beágyazása az alkalmazásba. Ennek feltétele, hogy már rendelkezik egy jelentést, amely létezik a munkaterületen belül a munkaterület-csoport. Ha még nem tette ezt a lépést, [Ismerkedés a Power BI-Munkaterületcsoportok](get-started.md).

Használhatja a .NET (C#) vagy a Node.js SDK-val, és a JavaScript, hogy építsenek a Power BI-Munkaterületcsoportok segítségével.

## <a name="using-the-access-keys-to-use-rest-apis"></a>A hozzáférési kulcsok használatával REST API-k

Annak érdekében, hogy a hívás a REST API-t, adja át a hozzáférési kulcsot, amely el egy adott munkaterület-csoport az Azure Portalon. További információkért lásd: [Ismerkedés a Power BI-Munkaterületcsoportok](get-started.md).

## <a name="get-a-report-id"></a>A jelentés azonosító beszerzése

Minden hozzáférési jogkivonatot a jelentés alapul. Az adott jelentés azonosító beszerzése a jelentés, amely a beágyazni kívánt kell. Ez alapján hívásainak végezhető a [jelentések lekérése](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API-t. Ez a jelentés azonosítója és a beágyazási URL-címet adja vissza. Ezt megteheti, a Power BI .NET SDK használatával, vagy közvetlenül a REST API-t hívná.

### <a name="using-the-power-bi-net-sdk"></a>A Power BI .NET SDK használatával

A .NET SDK használatával, ha szeretne létrehozni egy token hitelesítő adatot, amely az elérési kulcsot kap az Azure Portalról alapul. Ehhez az szükséges, hogy telepítse a [Power BI API NuGet-csomag](https://www.nuget.org/profiles/powerbi).

**NuGet-csomag telepítése**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**C#-kód**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>A REST API közvetlen hívása

```csharp
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

## <a name="create-an-access-token"></a>Hozzáférési jogkivonat létrehozása

A Power BI-Munkaterületcsoportok használható beágyazási tokenek, amely HMAC-val aláírt JSON webes jogkivonatainak. A jogkivonatok van bejelentkezve a Power BI-Munkaterületcsoport a hozzáférési kulcsára. Beágyazási tokenek, alapértelmezés szerint, egy jelentés beágyazása egy alkalmazásba való csak olvasási hozzáférést biztosítanak. Beágyazási tokenek egy adott jelentéshez kibocsátott, és egy beágyazási URL-címet társítva kell lennie.

Hozzáférési jogkivonatokat a kiszolgálón kell létrehozni a hozzáférési kulcsokat használ a tokenek bejelentkezési/titkosításához. A hozzáférési jogkivonat létrehozása információkért lásd: [Authenticating and authorizing with és a Power BI-Munkaterületcsoportok](app-token-flow.md). Emellett áttekintheti a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metódust. Íme egy példa, Mi ez módon jelenik meg a Power bi-ban a .NET SDK használatával.

A jelentés azonosítója, amely a korábban kapott használhatja. A beágyazási token létrehozása után a hozzáférési kulcsot használ majd fog hozza létre a jogkivonatot, amelyekkel javascript szempontjából. A *PowerBIToken osztály* kell telepíteni a [Power BI fő NuGut csomagot](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-csomag telepítése**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#-kód**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Engedélyek hatókörei a beágyazási tokenek hozzáadása

Beágyazási token használatakor előfordulhat, hogy korlátozni szeretné a erőforrások hozzáférést biztosít. Ebből kifolyólag a jogkivonatot az hatókörbe tartozó engedélyeket is létrehozhat. További információkért lásd: [hatókörök](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Beágyazás a JavaScript használatával

Megvan a hozzáférési jogkivonatot, és a jelentés azonosítója, hogy a jelentés JavaScript használatával ágyazhatók be. Ehhez az szükséges, hogy telepítse a NuGet [Power BI JavaScript csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). A embedUrl lesz, https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Használhatja a [JavaScript Jelentésbeágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/) funkció teszteléséhez. Hitelesítésikód-példák a különböző műveletek elérhető is biztosít.

**NuGet-csomag telepítése**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-kódot**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>Beágyazott elemek méretének beállítása

A jelentést a rendszer automatikusan beágyazza a tároló mérete alapján. Felülbírálja az alapértelmezett méret beágyazott elem, egyszerűen adjon hozzá egy CSS osztály attribútumot, vagy a beágyazott stílusai szélességét és magasságát.

## <a name="see-also"></a>Lásd még

[Bevezetés a minta használatába](get-started-sample.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[A Power BI JavaScript-csomag](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[A Power BI API NuGet-csomag](https://www.nuget.org/profiles/powerbi)
[Power BI alapvető NuGut csomag](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git Repo](https://github.com/Microsoft/PowerBI-Node)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)
