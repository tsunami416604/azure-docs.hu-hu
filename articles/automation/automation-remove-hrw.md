---
title: "Távolítsa el az Azure Automation hibrid forgatókönyv-feldolgozók |} Microsoft Docs"
description: "Ez a cikk tájékoztatást nyújt kezelése központilag telepített Azure Automation hibrid forgatókönyv-feldolgozóival, amely lehetővé teszi runbookok futtatását a helyi datacenter vagy a felhőalapú környezetben lévő számítógépeken."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: a897a97b9a1e259f5994a27b974eb5183fd1194b
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Távolítsa el az Azure Automation hibrid forgatókönyv-feldolgozók

képes vagy 

## <a name="removing-hybrid-runbook-worker"></a>Hibrid forgatókönyv-feldolgozó eltávolítása

Egy vagy több hibrid forgatókönyv-feldolgozók eltávolítása egy csoportból, vagy távolítsa el a csoport a követelményeitől függően.  A helyi számítógépről távolítsa el a hibrid forgatókönyv-feldolgozók, hajtsa végre az alábbi lépéseket.

1. Az Azure-portálon lépjen az Automation-fiók.  
2. Az a **beállítások** panelen válassza **kulcsok** meg és jegyezze fel a mező értékei **URL-cím** és **elsődleges elérési kulcsot**.  Ezt az információt a következő lépésre van szükség.
3. Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsot: `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Használja a **-Verbose** részletes napló, az eltávolítási folyamat váltani.

> [!NOTE]
> Ez nem távolítja el a Microsoft Monitoring Agent a számítógépről, csak a funkciókat és a hibrid forgatókönyv-feldolgozói szerepkör konfigurációja.  

## <a name="remove-hybrid-worker-groups"></a>Hibridfeldolgozó-csoport törlése

Távolítsa el a csoportot, először távolítsa el a hibrid forgatókönyv-feldolgozó minden számítógépen, a korábban bemutatott eljárás segítségével csoport tagja, és ezután hajtsa végre az alábbi lépések végrehajtásával távolítsa el a csoportot.  

1. Nyissa meg az Automation-fiók az Azure portálon.
1. Válassza ki a **hibrid dolgozó csoportok** csempe és a a **hibrid dolgozó csoportok** panelen válassza ki a törölni kívánt csoportot.  A megadott csoport kijelölése után a **hibrid feldolgozócsoport** tulajdonságok panelen jelenik meg.<br> ![Hibrid forgatókönyv-feldolgozó csoport panel](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. A kijelölt csoporthoz tartozó tulajdonságok paneljén kattintson **törlése**.  Válasszon egy üzenet jelenik meg, ez a művelet megerősítését kéri **Igen** Ha biztos benne, hogy a műveletet.<br> ![Csoport törlése megerősítő párbeszédpanele](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> A folyamat eltarthat néhány másodpercig, az előrehaladását nyomon követheti az **Értesítések** menüpont alatt. 

## <a name="next-steps"></a>Következő lépések

* Felülvizsgálati [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.
* Windows hibrid forgatókönyv-feldolgozók telepítésével kapcsolatos információkért lásd: [Azure Automation Windows hibrid forgatókönyv-feldolgozó](automation-windows-hrw-install.md).
* Linux hibrid forgatókönyv-feldolgozók telepítésével kapcsolatos információkért lásd: [Azure Automation Linux hibrid forgatókönyv-feldolgozó](automation-linux-hrw-install.md).