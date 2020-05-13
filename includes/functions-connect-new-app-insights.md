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
A függvények megkönnyítik Application Insights integráció hozzáadását egy Function-alkalmazáshoz a [Azure Portalból].

1. Az [Azure Portal][Azure Portalon]keresse meg és válassza ki a **Function app**elemet, majd válassza ki a Function alkalmazást. 

1. Válassza ki az ablak tetején a **Application Insights nem konfigurált** szalagcímet. Ha nem látja ezt a szalagcímet, az alkalmazás már Application Insights engedélyezve van.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Application Insights engedélyezése a portálról":::

1. Hozzon létre egy Application Insights erőforrást a rendszerkép alatti táblázatban megadott beállítások használatával.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights erőforrás létrehozása":::

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Egyedi alkalmazás neve | A legegyszerűbb, ha ugyanazt a nevet használja, mint a Function alkalmazás, amelynek egyedinek kell lennie az előfizetésében. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használja ugyanazt a [régiót](https://azure.microsoft.com/regions/) , mint a Function alkalmazás, vagy pedig az adott régióhoz közeledik. |

1. Kattintson az **Alkalmaz** gombra. A Application Insights erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a Function alkalmazás. Az erőforrás létrehozása után zárjuk be a Application Insights ablakot.

1. Vissza a Function alkalmazásban válassza a **Beállítások**  >  **konfigurálása**lehetőséget, majd válassza az **Alkalmazásbeállítások**lehetőséget. Ha megjelenik egy nevű beállítás `APPINSIGHTS_INSTRUMENTATIONKEY` , Application Insights integráció engedélyezve van az Azure-ban futó Function-alkalmazáshoz.

[Azure Portal]: https://portal.azure.com
