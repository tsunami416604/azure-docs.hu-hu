---
title: Az Azure Naplóelemzés erőforrások közötti keresési |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan kérdezheti több munkaterületekkel és App Insights app erőforrásokon az előfizetésben.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: a8d5465a2a9aaf9cf686a8e135a1f537cc60c6b5
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129251"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Kereszt-erőforrás napló keresést a Naplóelemzési  

Korábban az Azure Naplóelemzés, hogy sikerült csak adatok elemzését az aktuális munkaterület, és azt a lekérdezés képessége csak korlátozott az előfizetés definiált több munkaterületek között.  Emellett a web-alapú alkalmazásokhoz az Application insights szolgáltatással közvetlenül az Application Insights vagy a Visual Studio gyűjtött telemetriai elemek csak keressen.  Ez is tette a natív módon elemezheti a működési probléma és alkalmazásadatok együtt.   

Lekérheti most nem csak több Naplóelemzési munkaterület, de is ugyanazt az erőforráscsoportot, egy másik erőforráscsoportban vagy egy másik előfizetést az adott Application Insights alkalmazásból adatok között. Ez lehetővé teszi az adatok rendszerszintű nézetet.  Csak ezek a lekérdezéstípusok a hajthatja végre a [speciális portal](log-analytics-log-search-portals.md#advanced-analytics-portal), és nem az Azure-portálon. Az erőforrások száma (a Naplóelemzési munkaterület és az Application Insights-alkalmazás) is felvehet egyetlen lekérdezést 100 korlátozódik. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>A Naplóelemzési munkaterület és az Application Insights lekérdezése
A lekérdezés egy másik munkaterület hivatkozik, használja a [ *munkaterület* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) azonosítóját, és az Application Insights egy alkalmazás esetén a [ *app* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())azonosítója.  

### <a name="identifying-workspace-resources"></a>Munkaterület erőforrások azonosítása
Az alábbi példák bemutatják, lekérdezések Naplóelemzési munkaterület visszaadandó frissítések összesített száma a frissítés táblából nevű munkaterület különböző *contosoretail-it*. 

Munkaterület azonosító is lehet befejezett egy számos módon:

* Erőforrásnév - emberek számára olvasható alhálózatnév a munkaterület, más néven *összetevőnév*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A munkaterület neve azonosítja azt feltételezi, hogy egyediségi összes elérhető előfizetések között. Ha a megadott névvel több alkalmazást, a lekérdezés nem sikerült a kétértelműség miatt. Ebben az esetben az egyéb azonosítókhoz egyikét kell használnia.

* Minősített - értéke a "teljes neve" a munkaterületen, az előfizetés neve, az erőforráscsoport és az összetevő neve, a következő formátumban álló: *subscriptionName/resourceGroup/most letölthető a KomponensNév*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Mivel az Azure-előfizetés neve nem egyedi, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Munkaterület-azonosító: A munkaterület-Azonosítót az egyes munkaterületeken jelenik meg a globálisan egyedi azonosítóját (GUID) rendelt egyedi, nem módosítható, azonosító.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Az Azure erőforrás-azonosító – az Azure által meghatározott egyedi azonosító a munkaterületen. Ha az erőforrás neve nem egyértelmű használhatja az erőforrás-azonosítója.  A munkaterületek, formátuma: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/munkaterületek/most letölthető a KomponensNév*.  

    Példa:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Egy alkalmazás azonosítása
Az alábbi példák felé irányuló nevű alkalmazás kérelmek összesített számát adja vissza *fabrikamapp* az Application insights szolgáltatással. 

Az Application Insightsban alkalmazás azonosító segítségével végezhető a *app(Identifier)* kifejezés.  A *azonosító* argumentum meghatározza az alkalmazás segítségével a következők egyikét:

* Erőforrásnév - alhálózatnév emberi olvasható az alkalmazás, más néven a *összetevőnév*.  

    `app("fabrikamapp")`

* Minősített - értéke a "teljes neve" az alkalmazást, és az előfizetés neve, az erőforráscsoport és az összetevő neve, a következő formátumban álló: *subscriptionName/resourceGroup/most letölthető a KomponensNév*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetés neve nem egyedi, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Azonosító: az alkalmazás az alkalmazás GUID azonosítója.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Az Azure erőforrás-azonosító – az Azure által definiált egyedi azonosító az alkalmazás. Ha az erőforrás neve nem egyértelmű használhatja az erőforrás-azonosítója. A formátum: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/összetevőinek/most letölthető a KomponensNév*.  

    Példa:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>További lépések

Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](https://docs.loganalytics.io/docs/Language-Reference) összes a lekérdezés szintaxisa található lehetőségeit Naplóelemzési megtekintéséhez.    
