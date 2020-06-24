---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84731010"
---
A Azure Functions megkönnyíti Application Insights integráció hozzáadását egy Function-alkalmazáshoz a [Azure Portalból].

1. Az [Azure Portalon][Azure Portal] keresse meg és válassza a **függvényalkalmazás** lehetőséget, majd válassza ki a kívánt függvényalkalmazást. 

1. Válassza ki az ablak tetején található **Az Application Insights nincs konfigurálva** szalagot. Ha nem látja ezt a szalagcímet, előfordulhat, hogy az alkalmazás már Application Insights engedélyezve van.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Az Application Insights engedélyezése a portálon":::

1. Bontsa ki az **erőforrás módosítása** elemet, és hozzon létre egy Application Insights-erőforrást az alábbi táblázatban megadott beállítások használatával.  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Új erőforrásnév** | Egyedi alkalmazásnév | A legegyszerűbb, ha a függvényalkalmazás nevét használja, amelynek egyedinek kell lennie az előfizetésben. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használja a függvényalkalmazás [régióját](https://azure.microsoft.com/regions/) vagy egy ahhoz közeli régiót. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights-erőforrás létrehozása":::

1. Kattintson az **Alkalmaz** gombra. 

   Az Application Insights-erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, ahol a függvényalkalmazás található. Az erőforrás létrehozása utána zárja be az Application Insights ablakát.

1. A Function alkalmazásban válassza a **Konfigurálás** lehetőséget a **Beállítások**területen, majd válassza az **Alkalmazásbeállítások**lehetőséget. Ha az `APPINSIGHTS_INSTRUMENTATIONKEY` nevű beállítás megjelenik, akkor engedélyezve van az Application Insights-integráció az Azure-ban futó függvényalkalmazáshoz.

[Azure Portal]: https://portal.azure.com
