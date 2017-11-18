---
title: "Megfigyelő feladat létrehozása az Azure Automation-fiókban |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy megfigyelő feladatot az Azure Automation-fiók egy mappában létrehozott új fájlokat beállításjegyzékek."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 7cd6bebcaa1ed263b9854f7307cf22fba006748e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Azure Automation megfigyelő feladatok lehetővé teszik, hogy a helyi adatközpontban történik események válaszolni

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy új megfigyelő feladat:

> [!div class="checklist"]
> * Megkeresi az új fájlok a könyvtárban található megfigyelő runbook létrehozása.
> * A figyelő által feldolgozott fájl legutóbbi tartsa egy automatizálási változó létrehozása.
> * Hozzon létre egy művelet runbookot is nevezett, ha a megfigyelő runbook egy új fájlt talál.
> * Kiválasztja a megfigyelő runbook és a művelet a runbook egy megfigyelő-feladat létrehozása.
> * Egy figyelő vált egy új fájlt hozzá egy könyvtárat.
> * Vizsgálja meg a kimeneti adatokat jeleníti meg az új fájl művelet runbookból.  

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre szükség.
+ Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Automation-fiók](automation-offering-get-started.md) a felügyeletiszolgáltatás-megfigyelő és a művelet runbookokat, és a figyelő a feladat tárolásához.
+ A [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md) a megfigyelő feladat futtató.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Megkeresi az új fájlok megfigyelő runbook létrehozása
1.  Nyissa meg az Automation-fiókot, majd kattintson a Runbookok oldalon.
2.  Kattintson a "Tallózás gyűjtemény" gombra.
![A felhasználói Felületről Runbook listája](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Keresse meg a "Figyelés-NewFile", és a runbook importálása az Automation-fiók.
![A felhasználói Felületről runbook közzététele](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Kattintson az "Edit" tekintse meg a Runbook forrást, és kattintson a "Közzététele" gombra.

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>A figyelő által feldolgozott fájl legutóbbi tartsa egy automatizálási változó létrehozása
1.  Nyissa meg a megosztott erőforrások a változók lapot, majd kattintson a "Hozzáadás változó" ![a felhasználói Felületről változók felsorolása](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Adja meg a "Figyelés-NewFileTimestamp" a neve
3.  Válassza ki a típusát, DateTime, és kattintson a "Létrehozás" gombra.
![Vízjel változó létrehozása a felhasználói Felületről](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Egy művelet runbook is nevezett, amikor egy új fájlt talál a megfigyelő runbook létrehozása
1.  Kattintson a "Folyamatok AUTOMATIZÁLÁSA" kategóriához tartozó Runbookok oldalon.
2.  Kattintson a "Tallózás gyűjtemény" gombra.
3.  Keresse meg a "Folyamat-NewFile", és a runbook importálása az Automation-fiók.
4.  Kattintson az "Edit" tekintse meg a Runbook forrást, és kattintson a "Közzététele" gombra.
![A felhasználói Felületről folyamat figyelője](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Kiválasztja a megfigyelő runbook és a művelet a runbook egy megfigyelő-feladat létrehozása
1.  Nyissa meg a figyelő feladatok lapját, és kattintson a "Megfigyelő feladat hozzáadása" gombra.
![A felhasználói Felületről megfigyelő listája](media/automation-watchers-tutorial/WatchersList.png)
2.  Adja meg az "Új fájlokat beállításjegyzékek" néven.
3.  Válassza ki a "Figyelő beállítása", és válassza ki a "Figyelés-NewFile" runbookot.
![A felhasználói Felületről megfigyelő konfigurálása](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Adja meg a következő értékeket paraméterek:
    *   FOLDERPATH. A hibrid feldolgozó egy mappát, ahol új fájlok létrehozása
    *   A BŐVÍTMÉNY. Hagyja üresen az összes fájl kiterjesztéseinek feldolgozása.
    *   RECURSE. Hagyja változatlanul.
    *   FUTTATÁSI BEÁLLÍTÁSOK. Válassza ki a hibrid feldolgozó.
5.  Kattintson az OK gombra, majd válassza ki a figyelő lapra való visszatéréshez.
6.  Válassza ki a "Konfigurálása action", és válassza a "Folyamat-NewFile" runbook.
![A felhasználói Felületről megfigyelő művelet konfigurálása](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Adja meg a következő értékeket paraméterek:
    *   EVENTDATA. Hagyja üresen. Adatok kerülnek a megfigyelő runbook.
    *   Futtatási beállítások. Azure hagyja, a runbook fut az Automation szolgáltatás.
8.  Kattintson az OK gombra, majd válassza ki a figyelő lapra való visszatéréshez.
9.  Kattintson az OK gombra a megfigyelő feladat létrehozásához.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Egy új fájlt hozzá egy könyvtárat egy megfigyelő eseményindító
1.  A hibrid feldolgozó történő távoli
2.  A megfigyelő tevékenység által figyelt mappához történő hozzáadása egy új szövegfájlt.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Tekintse meg a kimenetet információkat jeleníti meg az új fájl művelet runbookból
1.  Kattintson a "Figyelés új fájlok" a figyelő feladat
2.  Kattintson a "nézet megfigyelő adatfolyamok" az, hogy a figyelő az új fájl található, és a művelet a runbook indítása.
3.  Kattintson a "megfigyelő művelet feladatok megtekintése" a művelet a runbook-feladat megtekintéséhez.
![Megfigyelő műveleti feladatok a felhasználói Felületről](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Következő lépések:

További információkért lásd: [az első PowerShell runbook](automation-first-runbook-textual-powershell.md).








