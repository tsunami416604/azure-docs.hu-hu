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
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132590"
---
A Functions megkönnyíti az Application Insights-integráció hozzáadását egy függvényalkalmazáshoz az [Azure Portalról.]

1. Az Azure Portal [portálon][Azure Portal]írja be `Function Apps` a lap tetején található keresősávot, válassza ki a függvényalkalmazást, majd válassza ki, hogy az Application Insights nincs **konfigurálva** az ablak tetején lévő szalagcím. Ha nem látja ezt a szalagcímet, akkor az alkalmazás már rendelkezik az Application Insights engedélyezve van.

    ![Az Application Insights engedélyezése a portálról](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Hozzon létre egy Application Insights-erőforrást a kép alatti táblázatban megadott beállításokkal.

   ![Application Insights-erőforrás létrehozása](media/functions-connect-new-app-insights/ai-general.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Egyedi alkalmazásnév | A legegyszerűbb, ha ugyanazt a nevet használja, mint a függvényalkalmazás, amelynek egyedinek kell lennie az előfizetésben. | 
    | **Helyen** | Nyugat-Európa | Ha lehetséges, használja ugyanazt a [régiót,](https://azure.microsoft.com/regions/) mint a függvényalkalmazás, vagy az adott régió közelében. |

1. Válassza **az OK gombot.** Az Application Insights-erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a függvényalkalmazás. Az erőforrás létrehozása után zárja be az Application Insights ablakot.

1. A függvényalkalmazásban válassza az **Alkalmazás beállításai**lehetőséget, majd görgessen le az Alkalmazás **beállításai hoz.** Ha megjelenik egy `APPINSIGHTS_INSTRUMENTATIONKEY`nevű beállítás, Application Insights-integráció engedélyezve van a függvényalkalmazás az Azure-ban futó.

[Azure Portal]: https://portal.azure.com
