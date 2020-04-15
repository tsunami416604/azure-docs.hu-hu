---
title: Adatok lekérdezése GA-környezetből C# kód használatával - Azure Time Series Insights | Microsoft dokumentumok
description: Megtudhatja, hogyan lehet adatokat lekérdezni egy Azure Time Series Insights-környezetből a C#-ban írt egyéni alkalmazás használatával.
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
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383892"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Adatok lekérdezése az Azure Time Series Insights GA-környezetből c használatával #

Ez a C# példa bemutatja, hogyan használhatja a [ga query API-k](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) at az Azure Time Series Insights GA-környezetekből származó adatok lekérdezéséhez.

> [!TIP]
> GA C# kódminták [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)megtekintése a itt.

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő jellemzőket mutatja be:

* Hozzáférési jogkivonat beszerzése az Azure Active Directoryn keresztül a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.

* Hogyan adja át a beszerzett `Authorization` hozzáférési jogkivonatot a későbbi Query API-kérelmek fejlécében. 

* A minta meghívja a ga query API-k mindegyikét, amelyek bemutatják, hogyan történik a HTTP-kérelmek a:
    * [A Környezetek API beküldése](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) azokhoz a környezetekhez, amelyekhez a felhasználó hozzáfér
    * [Környezeti rendelkezésre állási API beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Környezeti metaadat-API beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) a környezet metaadatainak beolvasásához
    * [Környezetesemények API beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Környezeti összesítések API beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* A ga query API-k használata a WSS használatával a következő üzenetküldéshez:

   * [Környezeti események streamelt API-jának beszereznie](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Környezeti összesítések streamelt API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt hajtsa végre az alábbi lépéseket:

1. [GA Azure Time Series Insights-környezet kiépítése.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Konfigurálja az Azure Time Series Insights környezetet az Azure Active Directoryhoz a [hitelesítés és engedélyezés leírtak](time-series-insights-authentication-and-authorization.md)szerint. 
1. Telepítse a szükséges projektfüggőségeket.
1. Az alábbi mintakód szerkesztésével minden **#DUMMY#** helyett a megfelelő környezeti azonosítót.
1. A kód végrehajtása a Visual Studio programban.

## <a name="project-dependencies"></a>Projektfüggőségek

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Verzió 16.4.2+

A mintakód két szükséges függőséget rendelkezik:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) – 3.13.9 csomag.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 csomag.

Töltse le a csomagokat a Visual Studio 2019-ben a **Build** > **Solution** lehetőség kiválasztásával.

Másik lehetőségként adja hozzá a csomagokat a [NuGet 2.12+](https://www.nuget.org/)használatával:

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# mintakód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a lekérdezésről, olvassa el a [Query API hivatkozását.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Olvassa el, hogyan [csatlakoztathat javascript-alkalmazásokat az ügyfél SDK-val a](https://github.com/microsoft/tsiclient) Time Series Insights-hoz.
