---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b1bbc11d7772e4f56d7dc6ead580b0a0cbd3cd8d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669671"
---
A függvények megkönnyítik Application Insights integráció hozzáadását egy Function-alkalmazáshoz a [Azure Portal].

1. A [portál][azure portal]válassza a **minden szolgáltatás > Function apps**lehetőséget, válassza ki a Function alkalmazást, majd válassza az ablak tetején található **Application Insights** szalagcímet.

    ![Application Insights engedélyezése a portálról](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Hozzon létre egy Application Insights erőforrást a rendszerkép alatti táblázatban megadott beállítások használatával.

   ![Application Insights-erőforrás létrehozása](media/functions-connect-new-app-insights/ai-general.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Egyedi alkalmazás neve | A legegyszerűbb, ha ugyanazt a nevet használja, mint a Function alkalmazás, amelynek egyedinek kell lennie az előfizetésében. | 
    | **Location** | Nyugat-Európa | Ha lehetséges, használja ugyanazt a [régiót](https://azure.microsoft.com/regions/) , mint a Function alkalmazás, vagy pedig az adott régióhoz közeledik. |

1. Kattintson az **OK** gombra. A Application Insights erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a Function alkalmazás. Az erőforrás létrehozása után zárjuk be a Application Insights ablakot.

1. Lépjen vissza a Function alkalmazásba, válassza az **Alkalmazásbeállítások**lehetőséget, majd görgessen le az **Alkalmazásbeállítások menüponthoz**. Ha megjelenik egy nevű `APPINSIGHTS_INSTRUMENTATIONKEY`beállítás, Application Insights integráció engedélyezve van az Azure-ban futó Function-alkalmazáshoz.

[Azure Portal]: https://portal.azure.com
