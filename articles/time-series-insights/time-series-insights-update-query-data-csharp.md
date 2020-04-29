---
title: Adatok lekérdezése előzetes verziójú környezetből C#-Azure Time Series Insights használatával | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le a Azure Time Series Insights-környezet adatait egy C# nyelven írt alkalmazás használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379814"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Adatok lekérdezése a Azure Time Series Insights Preview-környezetből C használatával #

Ez a C# példa azt szemlélteti, hogyan lehet lekérdezni az adatok [megtekintését az előzetes verziójú adathozzáférési API](https://docs.microsoft.com/rest/api/time-series-insights/preview) -kkal Azure Time Series Insights előzetes verziójú környezetekben.

> [!TIP]
> Tekintse meg a C#- [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)kód mintáit a címen.

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Az SDK automatikus létrehozásának támogatása az [Azure autorest](https://github.com/Azure/AutoRest)-ből.
* Hozzáférési token beszerzése Azure Active Directory a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.
* A beszerzett hozzáférési jogkivonat átadása a `Authorization` későbbi adathozzáférési API-kérelmek fejlécében. 
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
1. Nyissa `TSIPreviewDataPlaneclient.sln` meg a megoldást `DataPlaneClientSampleApp` , és állítsa be alapértelmezett projektként a Visual Studióban.
1. Telepítse a szükséges projekt-függőségeket az [alább](#project-dependencies) ismertetett lépésekkel, és állítsa össze a `.exe` példát egy végrehajtható fájlra.
1. A `.exe` fájl futtatásához kattintson rá duplán.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió

A mintakód több szükséges függőséggel rendelkezik, amelyek megtekinthetők a [packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) fájlban.

Töltse le a csomagokat a Visual Studio 2019-ban a **Build** > **Build megoldás** kiválasztásával. 

Másik lehetőségként vegyen fel minden csomagot az [NuGet 2.12 +](https://www.nuget.org/)használatával. Például:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# mintakód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A kód mintát az alapértelmezett környezeti változók módosítása nélkül lehet végrehajtani.
> * A kód minta egy .NET-es végrehajtható konzol alkalmazásban lesz lefordítva.

## <a name="next-steps"></a>További lépések

- A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Time Series Insights.
