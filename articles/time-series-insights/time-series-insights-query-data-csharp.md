---
title: Az Azure Time Series Insights általánosan elérhető környezet használatával adatokat kérdezhet le C# kód |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan kérdezhet le adatokat Azure Time Series Insights-környezetből kódolása C# (C-éles) .NET nyelven írt egyéni alkalmazás által.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 250dd691c3ef3146d6768123de52bf0628b10e42
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728964"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Az Azure Time Series Insights általánosan elérhető környezet használatával adatokat kérdezhet leC#

Ez C# példa bemutatja, hogyan kérdezhet le adatokat az Azure Time Series Insights GA környezetből.

A minta számos alapszintű példát is tartalmat a Query API használatára:

1. Az előkészítő lépésben beszerezni a hozzáférési jogkivonatot az Azure Active Directory API segítségével. A token továbbítja a `Authorization` minden Query API-kérés fejlécébe. Nem interaktív alkalmazások beállításához, tekintse meg a [hitelesítési és engedélyezési](time-series-insights-authentication-and-authorization.md). Gondoskodjon arról is, a minta elején definiált összes állandók megfelelően vannak beállítva.
1. A környezetek listáját tartalmazza, amely a felhasználó hozzáféréssel rendelkezik kapjuk meg. Az egyik környezet a környezet összesített eseményadatait van kiválasztva, és további adatok lekérdezése követi, ebben a környezetben.
1. A HTTPS-kérés példájaként a rendszer lekéri az érintett környezet rendelkezésreállási adatait.
1. A websocket-kérés példájaként a rendszer lekéri az érintett környezet összesített eseményadatait. Az adatokat a rendszer a teljes rendelkezésre állási időtartamra lekéri.

> [!NOTE]
> A mintakód elérhető legyen [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Projekt függőségeinek

Adja hozzá a NuGet-csomagok `Microsoft.IdentityModel.Clients.ActiveDirectory` és `Newtonsoft.Json`.

## <a name="c-example"></a>C#-példa

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>További lépések

- Lekérdezésével kapcsolatos további tudnivalókért olvassa el a [lekérdezési API-referencia](/rest/api/time-series-insights/ga-query-api).

- További ismertetés a [JavaScript egyoldalas alkalmazás csatlakoztatása](tutorial-create-tsi-sample-spa.md) a Time Series Insights.