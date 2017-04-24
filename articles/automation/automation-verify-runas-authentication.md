---
title: "Azure Automation-fiók konfigurációjának ellenőrzése | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan lehet ellenőrizni, hogy az Automation-fiók konfigurációja megfelelő-e."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation futtató fiók hitelesítésének tesztelése
Az Automation-fiók sikeres létrehozását követően végrehajthat egy egyszerű tesztet annak megállapítására, hogy az újonnan létrehozott vagy frissített Automation futtató fiók alkalmas-e a sikeres hitelesítésre az Azure Resource Managerben vagy a klasszikus Azure üzembe helyezési modellben.    

## <a name="automation-run-as-authentication"></a>Hitelesítés Automation futtató fiókkal

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az erőforráscsoportban elérhető összes erőforrás listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureAutomationTutorialScript** forgatókönyv paneljét.

## <a name="classic-run-as-authentication"></a>Hitelesítés klasszikus futtató fiókkal
Ha a klasszikus üzemi modellben szeretné kezelni az erőforrásokat, a következő lépések végrehajtásával megállapíthatja, hogy az új klasszikus futtató fiók alkalmas-e a hitelesítésre.     

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureClassicAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br><br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben szereplő összes klasszikus virtuális gép listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureClassicAutomationTutorialScript** forgatókönyv paneljét.

## <a name="next-steps"></a>Következő lépések
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* További információk a grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).

