---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121547"
---
A függvényekkel az Application Insights könnyedén integrálható a függvényalkalmazásokba az [Azure Portal].

1. Az [Azure Portalon][Azure Portal] keresse meg és válassza a **függvényalkalmazás** lehetőséget, majd válassza ki a kívánt függvényalkalmazást. 

1. Válassza ki az ablak tetején található **Az Application Insights nincs konfigurálva** szalagot. Ha nem jelenik meg ez a szalag, akkor az alkalmazáshoz már engedélyezve van az Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Az Application Insights engedélyezése a portálon":::

1. Hozzon létre egy Application Insights-erőforrást a kép alatti táblázatban megadott beállításokkal.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights-erőforrás létrehozása":::

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | Egyedi alkalmazásnév | A legegyszerűbb, ha a függvényalkalmazás nevét használja, amelynek egyedinek kell lennie az előfizetésben. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használja a függvényalkalmazás [régióját](https://azure.microsoft.com/regions/) vagy egy ahhoz közeli régiót. |

1. Kattintson az **Alkalmaz** gombra. Az Application Insights-erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, ahol a függvényalkalmazás található. Az erőforrás létrehozása utána zárja be az Application Insights ablakát.

1. A függvényalkalmazásban válassza a **Beállítások** > **Konfiguráció**, majd az **Alkalmazásbeállítások** lehetőséget. Ha az `APPINSIGHTS_INSTRUMENTATIONKEY` nevű beállítás megjelenik, akkor engedélyezve van az Application Insights-integráció az Azure-ban futó függvényalkalmazáshoz.

[Azure Portal]: https://portal.azure.com
