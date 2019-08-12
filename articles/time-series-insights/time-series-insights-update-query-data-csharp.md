---
title: Adatok lekérdezése Azure Time Series Insights előnézeti környezetből kód használatával C# | Microsoft Docs
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
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 8fdfd39527ccd298b78ef2c4c895d1f667d8d33b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677617"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Adatok lekérdezése a Azure Time Series Insights előnézet környezetből a következő használatával:C#

Ez C# a példa azt mutatja be, hogyan lehet lekérdezni az adatok a Azure Time Series Insights előzetes verziójának környezetét.

A minta számos alapszintű példát is tartalmat a Query API használatára:

1. Előkészítési lépésként szerezze be a hozzáférési jogkivonatot a Azure Active Directory API-n keresztül. Adja át ezt a tokent minden lekérdezési API- `Authorization` kérelem fejlécében. Nem interaktív alkalmazások beállításával kapcsolatban lásd: [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md). Győződjön meg arról is, hogy a minta elején definiált összes állandó megfelelően van beállítva.
1. Azon környezetek listája, amelyekhez a felhasználónak hozzáférése van. Az egyik környezet a fontos környezet, a további adatlekérdezések pedig erre a környezetre vannak lekérdezve.
1. A HTTPS-kérés példájaként a rendszer lekéri az érintett környezet rendelkezésreállási adatait.
1. A websocket-kérés példájaként a rendszer lekéri az érintett környezet összesített eseményadatait. Az adatokat a rendszer a teljes rendelkezésre állási időtartamra lekéri.

> [!NOTE]
> Ez a mintakód a következő címen is [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)elérhető:.

## <a name="c-example"></a>C#például

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> A fenti mintakód az alapértelmezett környezeti értékek módosítása nélkül is futtatható.

## <a name="next-steps"></a>További lépések

- A lekérdezéssel kapcsolatos további információkért olvassa el a [lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)-referenciát.

- Olvassa el, hogyan [csatlakozhat egy JavaScript egyoldalas alkalmazást](tutorial-create-tsi-sample-spa.md) Time Series Insightshoz.