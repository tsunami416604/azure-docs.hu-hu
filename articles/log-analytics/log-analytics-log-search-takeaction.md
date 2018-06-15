---
title: Azure Automation Runbook műveletet a Naplóelemzési felhasználó által kezdeményezett |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan egy Naplóelemzési keresési eredmény igény egy Automation-runbook futtatását.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: c59a32e1b2d460e04c4c6f5d1be2dd655abbef27
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394615"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>A Naplóelemzési napló keresési eredmény egy Automation-Runbook a művelet végrehajtása

Az Azure Naplóelemzés napló keresési eredményt, most kiválaszthatja **hajtsa végre a műveletet** egy Automation-runbook futtatásához.  A forgatókönyv segítségével kijavítani a problémát, vagy más műveletet például hibaelhárítási adatokat gyűjthet, egy e-mailek küldése, vagy hozzon létre egy szolgáltatási kérelem igénybe vehet. 

## <a name="components-and-features-used"></a>Használt összetevők és szolgáltatások
* [Azure Automation-fiók](../automation/automation-offering-get-started.md)
* [A Naplóelemzési munkaterület](../log-analytics/log-analytics-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Naplófájl-keresési runbook indítására

Hajtsa végre a műveletet egy olyan eseményre, és a napló a keresési eredmények runbook kezdeményezni, akkor először hozzon létre egy naplófájl-keresési, és az eredmények hívhat meg a runbook igény.  Ez a napló keresési funkciója érhető el a [Azure-portálon](../log-analytics/log-analytics-log-search-new.md).  Ebben a példában a naplófájl-keresési funkció alapszintű bemutató az Azure-portálról végezzük.

1. Az Azure portálon kattintson **minden szolgáltatás** válassza **Naplóelemzési**.  
2. A Naplóelemzési munkaterület kiválasztása.
3. A munkaterületen, válassza ki a **naplófájl-keresési**.  
4. A napló lapon a napló keresés végrehajtása.  
5. A napló a keresési eredmények, kattintson a három pont a mezők és az előugró ablakban válassza ki az egyik balra **művelet végrehajtása a**.<br><br> ![Válassza a keresési eredmény érvénybe műveletet](./media/log-analytics-log-search-takeaction/log-search-takeaction-menuoption.png) 
6. Válassza ki **egy runbook futtatására** válassza ki a runbook futtatását.  Az Automation-fiók, amely csatolva van a Naplóelemzési munkaterület minden olyan forgatókönyvben is választhat.  Vegye figyelembe a következőket:

    * Runbookok címkék alapján vannak rendezve.
    * Közvetlenül a mezőket a keresési eredmények a forgatókönyv bemeneti paraméter értékek választhatók ki.  A legördülő lista megjelenik az eredmény, válassza ki az összes elérhető mezők.  
    * Ha szeretné a runbook futtatásához egy [hibrid forgatókönyv-feldolgozó](../automation/automation-hybrid-runbook-worker.md) , hogy a számítógépen, amelyen a probléma, ha a megfelelő hibrid forgatókönyv-feldolgozó csoport tagja a gép csak tartalmazó telepítette.  Ha a hibrid feldolgozócsoport megegyezik-e a számítógép, amely a naplófájl-keresési eredmény nevét, majd a csoport automatikusan ki van választva.    

6. Miután rákattintott **futtatása**, lehetővé teszi, hogy tekintse át a feladat állapotának megnyílik a runbook feladat lap.   

Választja, ha egy runbookot, amely úgy lett konfigurálva, hogy [Log Analytics-riasztás alapján nevű](../automation/automation-invoke-runbook-from-omsla-alert.md), van egy bemeneti paraméter, **WebhookData** , amely **objektum** típusa.  Ha a bemeneti paraméter megadása kötelező, akkor adhatók át a runbook a keresési eredmények között, akkor átalakíthatja a JSON-formátumú karakterláncot egy objektumtípust, hogy lehetővé teszi a szűrést végezni, amelyre hivatkozhat a runbook-tevékenységek elemeket.  Ehhez kiválasztásával **keresési eredmények (objektum)** a legördülő listából.<br><br> ![Válassza ki a Webhook-objektumot a runbook paraméter](media/log-analytics-log-search-takeaction/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>További lépések

* Tekintse át a [Naplóelemzési jelentkezzen keresési hivatkozás](log-analytics-search-reference.md) megtekintéséhez az összes keresési mezők és facets Naplóelemzési érhető el.
* Útmutató Automation-runbook meghívása automatikusan, tekintse át [egy Azure Automation-runbook meghívása Log Analytics-riasztás alapján](../automation/automation-invoke-runbook-from-omsla-alert.md).  