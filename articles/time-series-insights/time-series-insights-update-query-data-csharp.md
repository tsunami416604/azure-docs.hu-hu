---
title: Adatok lekérdezése egy előnézeti környezetből c# használatával - Azure Time Series Insights | Microsoft dokumentumok
description: Megtudhatja, hogyan lehet adatokat lekérdezni egy Azure Time Series Insights-környezetből a C#-ban írt alkalmazás használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980987"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Adatok lekérdezése az Azure Time Series Insights előzetes környezetből a C használatával #

Ez a C# példa bemutatja, hogyan lehet adatokat lekérdezni az Azure Time Series Insights előzetes környezetében az [előzetes adatelérési API-kból.](https://docs.microsoft.com/rest/api/time-series-insights/preview)

> [!TIP]
> Tekintse meg az Előnézet [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)C# kódmintákat a rendszerben.

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő jellemzőket mutatja be:

* Az SDK automatikus generálásának támogatása az [Azure AutoRest](https://github.com/Azure/AutoRest)szolgáltatásból.
* Hozzáférési jogkivonat beszerzése az Azure Active Directoryn keresztül a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.
* A beszerzett hozzáférési jogkivonat `Authorization` átolvasása a későbbi Data Access API-kérelmek fejlécében. 
* A minta konzolfelületet biztosít, amely bemutatja, hogyan történik a HTTP-kérelmek a következő:

    * [A környezetek előzetes verziója API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Környezetek rendelkezésre állási API-jának beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) és [eseményséma API beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Lekérdezési API előnézete](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Események API beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [sorozatos API be-](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)és [beszereznie az összesítő sorozat API-t](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Idősorozatmodell API-k](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Hierarchiák API-](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) és [hierarchia-kötegelt API-jának beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * Típusok API és [típusok kötegelt API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch) [beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)
        * [Példányok API-](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) és [példányköteges API-jának beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Speciális [keresési](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) és [TSX-képességek.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt hajtsa végre az alábbi lépéseket:

1. [Előzetes Azure Time Series Insights-környezet kiépítése.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Konfigurálja az Azure Time Series Insights környezetet az Azure Active Directoryhoz a [hitelesítés és engedélyezés leírtak](time-series-insights-authentication-and-authorization.md)szerint. 
1. Futtassa a [GenerateCode.bat-ot](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) a [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) a Time Series Insights előzetes ügyfélfüggőségek létrehozásához.
1. Nyissa `TSIPreviewDataPlaneclient.sln` meg a `DataPlaneClientSampleApp` megoldást, és állítsa be alapértelmezett projektként a Visual Studióban.
1. Telepítse a szükséges projektfüggőségeket az [alábbi](#project-dependencies) lépések segítségével, `.exe` és fordítsa le a példát egy végrehajtható fájlba.
1. Futtassa a `.exe` fájlt dupla kattintással.

## <a name="project-dependencies"></a>Projektfüggőségek

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Verzió 16.4.2+

A mintakód nak több szükséges függősége van, amelyek megtekinthetők a [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) fájlban.

Töltse le a csomagokat a Visual Studio 2019-ben a **Build** > **Solution** lehetőség kiválasztásával. 

Másik lehetőségként adja hozzá az egyes csomagokat a [NuGet 2.12+ használatával.](https://www.nuget.org/) Példa:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# mintakód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A kódminta az alapértelmezett környezeti változók módosítása nélkül is végrehajtható.
> * A kódminta lefordítja a .NET végrehajtható konzolalkalmazást.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a lekérdezésről, olvassa el a [Query API hivatkozását.](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)

- Olvassa el, hogyan [csatlakoztathat javascript-alkalmazásokat az ügyfél SDK-val a](https://github.com/microsoft/tsiclient) Time Series Insights-hoz.
