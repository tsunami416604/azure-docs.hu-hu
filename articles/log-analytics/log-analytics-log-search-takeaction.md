---
title: "Azure Automation Runbook műveletet a Naplóelemzési felhasználó által kezdeményezett |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan egy Naplóelemzési keresési eredmény igény egy Automation-runbook futtatását."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ff938697add98f3d21b4971175432335ee2e39ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>A Naplóelemzési napló keresési eredmény egy Automation-Runbook a művelet végrehajtása

Az Azure Naplóelemzés napló keresési eredményt, most kiválaszthatja **hajtsa végre a műveletet** egy Automation-runbook futtatásához.  A forgatókönyv segítségével kijavítani a problémát, vagy más műveletet például hibaelhárítási adatokat gyűjthet, egy e-mailek küldése, vagy hozzon létre egy szolgáltatási kérelem igénybe vehet. 

## <a name="components-and-features-used"></a>Használt összetevők és szolgáltatások
* [Azure Automation-fiók](../automation/automation-offering-get-started.md)
* [A Naplóelemzési munkaterület](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Naplófájl-keresési runbook indítására

Hajtsa végre a műveletet egy olyan eseményre, és a napló a keresési eredmények runbook kezdeményezni, akkor először hozzon létre egy naplófájl-keresési, és az eredmények hívhat meg a runbook igény.  Ez a napló keresési funkció az Azure-ban érhető el vagy [OMS-portálon](../log-analytics/log-analytics-log-searches.md).  Ebben a példában a naplófájl-keresési funkció alapszintű bemutató az Azure-portálról végezzük.

1. Az Azure portálon, a központ menüben kattintson a **további szolgáltatások** válassza **Naplóelemzési**.  
2. A Naplóelemzési panelen válassza ki a Naplóelemzési munkaterület, majd a munkaterületen panelen **naplófájl-keresési**.  
3. A naplófájl-keresési panelen napló keresés végrehajtása.  
4. A napló a keresési eredmények, kattintson a három pont a mezők és az előugró ablakban válassza ki az egyik balra **művelet végrehajtása a**.<br><br> ![Válassza a keresési eredmény érvénybe műveletet](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
5. Hajtsa végre a megfelelő művelet paneljén válassza **egy runbook futtatására**, majd a a **egy runbook futtatására** panelen kiválaszthatja a runbook futtatását.  Az Automation-fiók, amely csatolva van a Naplóelemzési munkaterület minden olyan forgatókönyvben is választhat.  Vegye figyelembe a következőket:

    * Runbookok címkék alapján vannak rendezve.
    * Közvetlenül a mezőket a keresési eredmények a forgatókönyv bemeneti paraméter értékek választhatók ki.  A legördülő lista megjelenik az eredmény, válassza ki az összes elérhető mezők.  
    * Ha szeretné a runbook futtatásához egy [hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md) , hogy a számítógépen, amelyen a probléma, ha a megfelelő hibrid forgatókönyv-feldolgozó csoport tagja a gép csak tartalmazó telepítette.  Ha a hibrid feldolgozócsoport megegyezik-e a számítógép, amely a naplófájl-keresési eredmény nevét, majd a csoport automatikusan ki van választva.    

6. Miután rákattintott **futtatása**, a runbook-feladat panelen nyílik meg, hogy tekintse át a feladat állapotának meg.   

Választja, ha egy runbookot, amely úgy lett konfigurálva, hogy [Log Analytics-riasztás alapján nevű](../automation/automation-invoke-runbook-from-omsla-alert.md), van egy bemeneti paraméter, **WebhookData** , amely **objektum** típusa.  Ha a bemeneti paraméter megadása kötelező, akkor adhatók át a runbook a keresési eredmények között, akkor átalakíthatja a JSON-formátumú karakterláncot egy objektumtípust, hogy lehetővé teszi a szűrést végezni, amelyre hivatkozhat a runbook-tevékenységek elemeket.  Ehhez kiválasztásával **keresési eredmények (objektum)** a legördülő listából.<br><br> ![Válassza ki a Webhook-objektumot a runbook paraméter](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Következő lépések

* Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](log-analytics-search-reference.md) megtekintéséhez az összes keresési mezők és facets Naplóelemzési érhető el.
* Útmutató Automation-runbook meghívása automatikusan, tekintse át [egy Azure Automation-runbook hívja az OMS-Log Analytics-riasztás alapján](../automation/automation-invoke-runbook-from-omsla-alert.md).  