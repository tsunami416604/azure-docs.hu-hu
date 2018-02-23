---
title: "Hajtsa végre a lekérdezéseket Azure Naplóelemzés munkaterületek között |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan kérdezheti több munkaterületekkel és az adott App Insights alkalmazást az előfizetésben."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>Lekérdezések végrehajtása több Naplóelemzési munkaterület között

Korábban az Azure Naplóelemzés, hogy sikerült csak adatok elemzését az aktuális munkaterület, és azt a lekérdezés képessége csak korlátozott az előfizetés definiált több munkaterületek között.  

Lekérheti most nem csak több Naplóelemzési munkaterület, de is ugyanazt az erőforráscsoportot, egy másik erőforráscsoportban vagy egy másik előfizetést az adott Application Insights alkalmazásból adatok között. Ez lehetővé teszi az adatok rendszerszintű nézetet.  Csak ilyen típusú lekérdezést hajthatja végre a [speciális portal](log-analytics-log-search-portals.md#advanced-analytics-portal), és nem az Azure-portálon.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>A Naplóelemzési munkaterület és az Application Insights lekérdezése
A lekérdezés egy másik munkaterület hivatkozik, használja a [ *munkaterület* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) azonosítóját, és az Application Insights egy alkalmazás esetén a [ *app* ](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())azonosítója.  

Például az első lekérdezés visszaadja-e az összesített számát az aktuális munkaterület, és egy másik munkaterület nevű frissítés táblából besorolásuk által igényelt frissítések is *contosoretail-it*.  A második lekérdezés példa felé irányuló nevű alkalmazás kérelmek összesített számát adja vissza *fabrikamapp* az Application insights szolgáltatással. 

### <a name="identifying-workspace-resources"></a>Munkaterület erőforrások azonosítása
A munkaterület azonosító végrehajtható számos módon:

* Erőforrásnév - emberek számára olvasható alhálózatnév a munkaterület, más néven *összetevőnév*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A munkaterület neve azonosító azt feltételezi, hogy egyedi az összes elérhető előfizetések között. Ha a megadott névvel több alkalmazást, a lekérdezés nem sikerült a kétértelműség miatt. Ebben az esetben az egyéb azonosítókhoz egyikét kell használnia.

* Minősített - értéke a "teljes neve" a munkaterületen, az előfizetés neve, az erőforráscsoport és az összetevő neve, a következő formátumban álló: *subscriptionName/resourceGroup/most letölthető a KomponensNév*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Mivel az Azure-előfizetés neve nem egyedi, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* munkaterület-azonosító: A munkaterület-Azonosítót az egyes munkaterületeken jelenik meg a globálisan egyedi azonosítóját (GUID) rendelt egyedi, nem módosítható, azonosító.

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Az Azure erőforrás-azonosító – az Azure által meghatározott egyedi azonosító a munkaterületen. Ha az erőforrás neve nem egyértelmű használhatja az erőforrás-azonosítója.  A munkaterületek, formátuma: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/munkaterületek/most letölthető a KomponensNév*.  

    Példa:
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Egy alkalmazás azonosítása

Az Application Insightsban alkalmazás azonosításának hajtható végre a *app(Identifier)* kifejezés.  A *azonosító* argumentum meghatározza az alkalmazás segítségével a következők egyikét:

* Erőforrásnév - alhálózatnév emberi olvasható az alkalmazás, más néven a *összetevőnév*.  

    `app("fabrikamapp")`

* Minősített - értéke a "teljes neve" az alkalmazást, és az előfizetés neve, az erőforráscsoport és az összetevő neve, a következő formátumban álló: *subscriptionName/resourceGroup/most letölthető a KomponensNév*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetés neve nem egyedi, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Azonosító: az alkalmazás az alkalmazás GUID azonosítója.

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* Az Azure erőforrás-azonosító – az Azure által definiált egyedi azonosító az alkalmazás. Ha az erőforrás neve nem egyértelmű használhatja az erőforrás-azonosítója. A formátum: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/összetevőinek/most letölthető a KomponensNév*.  

    Példa:
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>További lépések

Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](https://docs.loganalytics.io/docs/Language-Reference) összes a lekérdezés szintaxisa található lehetőségeit Naplóelemzési megtekintéséhez.    