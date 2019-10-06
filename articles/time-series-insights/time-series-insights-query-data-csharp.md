---
title: Adatok lekérdezése Azure Time Series Insights GA-környezetből C# kód használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezni egy Azure Time Series Insights-környezet adatait egy C# (c-Sharp) .net nyelven írt egyéni alkalmazás kódolásával.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 5928a8ee048b608d691ecea33d186838ec743e4b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981128"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Adatok lekérdezése a Azure Time Series Insights GA-környezetből a következő használatával:C#

Ez C# a példa azt szemlélteti, hogyan lehet lekérdezni az Azure Time Series Insights GA-környezet adatait.

A minta számos alapszintű példát is tartalmat a Query API használatára:

1. Előkészítési lépésként szerezze be a hozzáférési jogkivonatot a Azure Active Directory API-n keresztül. Adja át ezt a tokent minden lekérdezési API-kérelem `Authorization` fejlécében. Nem interaktív alkalmazások beállításával kapcsolatban lásd: [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md). Győződjön meg arról is, hogy a minta elején definiált összes állandó megfelelően van beállítva.
1. Azon környezetek listája, amelyekhez a felhasználónak hozzáférése van. Az egyik környezet a fontos környezet, a további adatlekérdezések pedig erre a környezetre vannak lekérdezve.
1. A HTTPS-kérés példájaként a rendszer lekéri az érintett környezet rendelkezésreállási adatait.
1. A websocket-kérés példájaként a rendszer lekéri az érintett környezet összesített eseményadatait. Az adatokat a rendszer a teljes rendelkezésre állási időtartamra lekéri.

> [!NOTE]
> A példában szereplő kód a következő címen érhető el: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Projekt függőségei

Adja hozzá a NuGet-csomagokat `Microsoft.IdentityModel.Clients.ActiveDirectory` és `Newtonsoft.Json`.

## <a name="c-example"></a>C#például

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>További lépések

- A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API-referenciát](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Olvassa el, hogyan [csatlakozhat egy JavaScript-alkalmazást az ügyfél-SDK](https://github.com/microsoft/tsiclient) -val Time Series Insights.