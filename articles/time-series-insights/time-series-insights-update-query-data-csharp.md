---
title: Adatok lekérdezése Gen2-környezetből C#-Azure Time Series Insights használatával | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le az adatok egy Azure Time Series Insights Gen2-környezetből a C# nyelven írt alkalmazás használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: ac65f1927aa890e5eaed757d7db054f2c2bbad3d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243734"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Adatok lekérdezése a Azure Time Series Insights Gen2-környezetből C Sharp használatával

Ez a C# példa azt szemlélteti, hogyan lehet lekérdezni a [Gen2 adatelérési API](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) -k adatait Azure Time Series Insights Gen2 környezetekben.

> [!TIP]
> A C#-Gen2 megtekintése a következő helyen: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Az SDK automatikus létrehozásának támogatása az [Azure autorest](https://github.com/Azure/AutoRest)-ből.
* Hozzáférési token beszerzése Azure Active Directory a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.
* A beszerzett hozzáférési jogkivonat átadása a `Authorization` későbbi adathozzáférési API-kérelmek fejlécében.
* A minta egy konzol felületét mutatja be, amely bemutatja, hogyan történik a HTTP-kérések elvégzése a következő módon:
  * [Gen2 Environments API](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * A [környezet rendelkezésre állási API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) beszerzése és az [Event Schema API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Gen2 lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * Az [Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)beszerzése, a [Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)beolvasása és az [aggregált Series API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Idősorozat-modell API-k](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Hierarchiák API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) -és [hierarchiáinak](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch) BEolvasása batch API
    * [Get types API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) és [TYPEs batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Példányok API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) -k és példányok beolvasása [Batch API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* Speciális [keresési](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) és [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) képességek.

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt végezze el a következő lépéseket:

1. [Gen2 Azure Time Series Insights-környezet kiépítése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Konfigurálja Azure Time Series Insights-környezetét Azure Active Directory a [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md)című témakörben leírtak szerint.
1. Futtassa a [readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) megadott [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) a Azure Time Series Insights Gen2-ügyfél függőségeinek létrehozásához.
1. Nyissa meg a `TSIPreviewDataPlaneclient.sln` megoldást, és állítsa be `DataPlaneClientSampleApp` alapértelmezett projektként a Visual Studióban.
1. Telepítse a szükséges projekt-függőségeket az [alább](#project-dependencies) ismertetett lépésekkel, és állítsa össze a példát egy végrehajtható `.exe` fájlra.
1. A fájl futtatásához kattintson `.exe` rá duplán.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió

A mintakód több szükséges függőséggel rendelkezik, amelyek megtekinthetők a [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) fájlban.

Töltse le a csomagokat a Visual Studio 2019-ban a **Build**  >  **Build megoldás** kiválasztásával.

Másik lehetőségként vegyen fel minden csomagot az [NuGet 2.12 +](https://www.nuget.org/)használatával. Például:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# mintakód

A C# mintakód a következő helyen található: [csharpquery-example](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample)

> [!NOTE]
>
> * A kód mintát az alapértelmezett környezeti változók módosítása nélkül lehet végrehajtani.
> * A kód minta egy .NET-es végrehajtható konzol alkalmazásban lesz lefordítva.

## <a name="next-steps"></a>További lépések

* A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).

* Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Azure Time Series Insights.
