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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132590"
---
A függvények megkönnyítik Application Insights integráció hozzáadását egy Function-alkalmazáshoz a [Azure Portalból].

1. A [portál][Azure Portalon]írja be `Function Apps` a lap tetején található keresési sávot, válassza ki a Function alkalmazást, majd válassza ki a **Application Insights nincs** megjelölve szalagcímet az ablak tetején. Ha nem látja ezt a szalagcímet, az alkalmazás már Application Insights engedélyezve van.

    ![Application Insights engedélyezése a portálról](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Hozzon létre egy Application Insights erőforrást a rendszerkép alatti táblázatban megadott beállítások használatával.

   ![Application Insights-erőforrás létrehozása](media/functions-connect-new-app-insights/ai-general.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Egyedi alkalmazás neve | A legegyszerűbb, ha ugyanazt a nevet használja, mint a Function alkalmazás, amelynek egyedinek kell lennie az előfizetésében. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használja ugyanazt a [régiót](https://azure.microsoft.com/regions/) , mint a Function alkalmazás, vagy pedig az adott régióhoz közeledik. |

1. Kattintson az **OK** gombra. A Application Insights erőforrás ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a Function alkalmazás. Az erőforrás létrehozása után zárjuk be a Application Insights ablakot.

1. Lépjen vissza a Function alkalmazásba, válassza az **Alkalmazásbeállítások**lehetőséget, majd görgessen le az **Alkalmazásbeállítások menüponthoz**. Ha megjelenik egy nevű `APPINSIGHTS_INSTRUMENTATIONKEY`beállítás, Application Insights integráció engedélyezve van az Azure-ban futó Function-alkalmazáshoz.

[Azure Portal]: https://portal.azure.com
