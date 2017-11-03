---
title: "Hajtsa végre a lekérdezéseket Azure Naplóelemzés munkaterületek között |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan kérdezheti több munkaterületek között szereplő példák, hajtsa végre az előfizetés."
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
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Hajtsa végre a lekérdezések között több Naplóelemzési munkaterület

Korábban az Azure Naplóelemzés, akkor lehetett csak adatok elemzését az aktuális munkaterület és a korlátozott lekérdezés arra a képességére, az előfizetés definiált több munkaterületek között.  

Most lekérheti több munkaterületek keresztül az adatok rendszerszintű nézetének.  Csak ilyen típusú lekérdezést hajthatja végre a [speciális portal](log-analytics-log-search-portals.md#advanced-analytics-portal), és nem az Azure-portálon.  

## <a name="querying-across-log-analytics-workspaces"></a>A Naplóelemzési munkaterület különböző lekérdezése
A lekérdezés egy másik munkaterület hivatkozik, használja a *munkaterület* azonosítója.  Például a következő lekérdezés eredményében összesített számát az aktuális munkaterület, és egy másik munkaterület nevű frissítés táblából besorolásuk által igényelt frissítések is *contosoretail-it*.  


## <a name="identifying-resources"></a>Erőforrások azonosító
A munkaterület azonosító végrehajtható számos módon:

* Erőforrásnév - emberek számára olvasható alhálózatnév a munkaterület, más néven *összetevőnév*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A munkaterület neve azonosító azt feltételezi, hogy egyedi az összes elérhető előfizetések között. Ha a megadott névvel több alkalmazást, a lekérdezés nem sikerült a kétértelműség miatt. Ebben az esetben az egyéb azonosítókhoz egyikét kell használnia.

* Minősített - értéke a "teljes neve" a munkaterület előfizetés neve álló erőforráscsoport és az összetevő neve a következő formátumban: *subscriptionName/resourceGroup/most letölthető a KomponensNév*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Mivel az Azure-előfizetés neve nem egyedi, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* munkaterület-azonosító: A munkaterület-Azonosítót az egyes munkaterületeken jelenik meg a globálisan egyedi azonosítóját (GUID) rendelt egyedi, nem módosítható, azonosító.

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Az Azure erőforrás-azonosító – az Azure által meghatározott egyedi azonosító a munkaterületen. Ön ezt használni, amikor az erőforrás neve nem egyértelmű.  A munkaterületek, formátuma: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/munkaterületek/most letölthető a KomponensNév*.  

    Példa:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Következő lépések

Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](https://docs.loganalytics.io/docs/Language-Reference) összes a lekérdezés szintaxisa található lehetőségeit Naplóelemzési megtekintéséhez.    