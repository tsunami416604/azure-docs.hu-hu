---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59497352"
---
Funkciók egyszerűvé teszi a függvényalkalmazás hozzáadása az Application Insights-integráció a [Azure Portal].

1. Az a [portál][azure portal], jelölje be **minden szolgáltatás > Függvényalkalmazások**, válassza ki a függvényalkalmazást, és válassza a **Application Insights** az ablak tetején lévő fejléc

    ![Az Application Insights engedélyezése a portálról](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Hozzon létre egy Application Insights-erőforrást az ábra alatti táblázatban megadott beállításokkal:

   ![Application Insights-erőforrás létrehozása](media/functions-connect-new-app-insights/ai-general.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | Egyedi nevet az alkalmazáshoz | A legegyszerűbb használja ugyanazt a nevet a függvényalkalmazás, amelyek az előfizetésében egyedinek kell lennie. | 
    | **Hely** | Nyugat-Európa | Ha lehetséges, használjon azonos [régió](https://azure.microsoft.com/regions/) a függvényalkalmazás, vagy a függvényalkalmazáshoz. |

1. Kattintson az **OK** gombra. Az Application Insights-erőforrást az ugyanabban az erőforráscsoportban és előfizetésben, a függvényalkalmazás jön létre. Létrehozás befejezése után zárja be az Application Insights ablakot.

1. Vissza a függvényalkalmazásban, jelölje be a **Alkalmazásbeállítások**, és görgessen le a **Alkalmazásbeállítások**. Amikor megjelenik egy beállítás nevű `APPINSIGHTS_INSTRUMENTATIONKEY`, az azt jelenti, hogy az Application Insights integrációja az Azure-ban futó függvényalkalmazást a engedélyezve van-e.

[Azure Portal]: https://portal.azure.com
