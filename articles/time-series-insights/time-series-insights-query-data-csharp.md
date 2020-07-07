---
title: Adatok lekérdezése egy GA-környezetből C# Code-Azure Time Series Insights használatával | Microsoft Docs
description: Megtudhatja, hogyan kérdezheti le a Azure Time Series Insights-környezet adatait egy C# nyelven írt egyéni alkalmazás használatával.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81383892"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Adatok lekérdezése a Azure Time Series Insights GA-környezetből C használatával #

Ez a C# példa azt szemlélteti, hogyan használható a [GA lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) -k Azure Time Series Insights GA-környezetek adatainak lekérdezéséhez.

> [!TIP]
> A GA C# kód mintáinak megtekintése a következő helyen: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample) .

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Hozzáférési token beszerzése Azure Active Directory a [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)használatával.

* A beszerzett hozzáférési jogkivonat átadása a `Authorization` következő lekérdezési API-kérelmek fejlécében. 

* A minta meghívja a GA lekérdezési API-kat, amely bemutatja, hogyan történik a HTTP-kérések elvégzése:
    * A [környezetek API beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) azon környezetek visszaküldéséhez, amelyekhez a felhasználónak hozzáférése van
    * [Környezet rendelkezésre állási API-k beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Környezeti metaadatok](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) beolvasása a környezeti metaadatok lekéréséhez
    * [Környezetek eseményeinek beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Környezeti összesítések beolvasása API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* A GA lekérdezési API-k használata a WSS használatával a következő üzenettel:

   * [Környezeti események beolvasása – adatfolyamként továbbított API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Környezet összesített adatfolyamának beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt végezze el a következő lépéseket:

1. [GA Azure Time Series Insights környezet kiépítése](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .
1. Konfigurálja Azure Time Series Insights-környezetét Azure Active Directory a [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md)című témakörben leírtak szerint. 
1. Telepítse a szükséges projekt-függőségeket.
1. Szerkessze az alábbi mintakód összes **#DUMMY #** helyére a megfelelő környezeti azonosítóval.
1. Futtassa a kódot a Visual Studióban.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió

A mintakód két kötelező függőséggel rendelkezik:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 csomag.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1 csomag.

Töltse le a csomagokat a Visual Studio 2019-ban a **Build**  >  **Build megoldás** kiválasztásával.

Azt is megteheti, hogy hozzáadja a csomagokat a [NuGet 2.12 +](https://www.nuget.org/)használatával:

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# mintakód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>További lépések

- A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Time Series Insights.
