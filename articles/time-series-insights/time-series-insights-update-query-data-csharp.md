---
title: Adatok lekérdezése előnézeti környezetből a-Azure Time Series Insights használatával C# | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le az adatok egy Azure Time Series Insights- C#környezetből egy írt alkalmazás használatával.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980987"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Adatok lekérdezése a Azure Time Series Insights előnézet környezetből a következő használatával:C#

Ez C# a példa azt mutatja be, hogyan lehet lekérdezni az adatok az [előzetes verziójú adathozzáférési api](https://docs.microsoft.com/rest/api/time-series-insights/preview) -kkal Azure Time Series Insights előzetes verzió környezetekben.

> [!TIP]
> Tekintse C# meg az előnézeti kód mintáit [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Az SDK automatikus létrehozásának támogatása az [Azure autorest](https://github.com/Azure/AutoRest)-ből.
* Hozzáférési token beszerzése Azure Active Directory a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.
* A beszerzett hozzáférési jogkivonat átadása a következő adathozzáférési API-kérelmek `Authorization` fejlécében. 
* A minta egy konzol felületét mutatja be, amely bemutatja, hogyan történik a HTTP-kérelmek:

    * [Előnézet környezetek API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * A [környezet rendelkezésre állási API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) beszerzése és az [Event Schema API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Lekérdezési API előnézete](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * Az [Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)beszerzése, a [Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)beolvasása és az [aggregált Series API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Idősorozat-modell API-k](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) -és [hierarchiáinak](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch) BEolvasása batch API
        * [Get types API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) és [TYPEs batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Példányok API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) -k és példányok beolvasása [Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Speciális [keresési](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) és [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) képességek.

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt végezze el a következő lépéseket:

1. [Előzetes verziójú Azure Time Series Insights-környezet kiépítése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Konfigurálja Azure Time Series Insights-környezetét Azure Active Directory a [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md)című témakörben leírtak szerint. 
1. Futtassa a [GenerateCode. bat fájlt](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) a [readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) megadott módon a Time Series Insights előzetes verziójú ügyfél függőségeinek létrehozásához.
1. Nyissa meg a `TSIPreviewDataPlaneclient.sln` megoldást, és állítsa be a `DataPlaneClientSampleApp`t alapértelmezett projektként a Visual Studióban.
1. Telepítse a szükséges projekt-függőségeket az [alább](#project-dependencies) ismertetett lépésekkel, és fordítsa le a példát egy végrehajtható `.exe` fájlra.
1. A `.exe` fájl futtatásához kattintson duplán a gombra.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió

A mintakód több szükséges függőséggel rendelkezik, amelyek megtekinthetők a [packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) fájlban.

Töltse le a csomagokat a Visual Studio 2019-ben a **build** > **Build Solution (megoldás** létrehozása) lehetőség kiválasztásával. 

Másik lehetőségként vegyen fel minden csomagot az [NuGet 2.12 +](https://www.nuget.org/)használatával. Példa:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#mintakód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A kód mintát az alapértelmezett környezeti változók módosítása nélkül lehet végrehajtani.
> * A kód minta egy .NET-es végrehajtható konzol alkalmazásban lesz lefordítva.

## <a name="next-steps"></a>Következő lépések

- A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Time Series Insights.
