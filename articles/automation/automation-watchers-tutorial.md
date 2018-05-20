---
title: Megfigyelő feladat létrehozása az Azure Automation-fiókban
description: Megtudhatja, hogyan hozzon létre egy megfigyelő feladatot az Azure Automation-fiók egy mappában létrehozott új fájlokat beállításjegyzékek.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 03/19/2017
ms.openlocfilehash: 0cc215d6643c86460a1d5471aa1eed8fdf18e028
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Hozzon létre egy Azure Automation-megfigyelő feladatok fájl változásainak követése a helyi számítógépen

Azure Automation szolgáltatásbeli megfigyelő feladatok műveletek indul el, tekintse meg az események használja. Ez az oktatóanyag bemutatja, hogyan figyelheti, amikor egy új fájlt ad hozzá egy könyvtárat megfigyelő tevékenység létrehozása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megfigyelő runbook importálása
> * Az automatizálási változó létrehozása
> * Egy művelet runbook létrehozása
> * Megfigyelő feladat létrehozása
> * Egy figyelő eseményindító
> * Tekintse meg a kimenetet

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a felügyeletiszolgáltatás-megfigyelő és a művelet runbookokat, és a figyelő a feladat tárolásához.
* A [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md) a megfigyelő feladat futtató.

## <a name="import-a-watcher-runbook"></a>Megfigyelő runbook importálása

Ez az oktatóanyag használja egy megfigyelő runbook nevű **figyelési-NewFile** amelyet meg kíván keresni az új fájlok a könyvtárban található. A megfigyelő runbook egy mappában található fájlok utolsó ismert írási idő beolvasása, és ellenőrzi, hogy az újabb, mint a vízjel fájlokat. Ebben a lépésben akkor importálja ezt a runbookot az automation-fiók.

1. Nyissa meg az Automation-fiók, és törölje a **Runbookok** lap.
1. Kattintson a **Tallózás gyűjtemény** gombra.
1. Keressen "Megfigyelő runbook", válassza ki a **megfigyelő runbook, új fájlok a könyvtárban található a következőhöz** válassza **importálása**.
  ![Automation-runbook importálása a felhasználói Felületről](media/automation-watchers-tutorial/importsourcewatcher.png)
1. A runbook adjon egy nevet és leírást, és válassza ki **OK** a runbook importálása az Automation-fiók.
1. Válassza ki **szerkesztése** majd **közzététel**. Ha a select kéri **Igen** közzétenni a runbookot.

## <a name="create-an-automation-variable"></a>Az automatizálási változó létrehozása

Egy [automatizálási változó](automation-variables.md) tárolja az időbélyegeket, amely a fenti forgatókönyv olvas, és minden egyes fájl tárolja. 

1. Válassza ki **változók** alatt **megosztott erőforrások** válassza **+ változó hozzáadása**.
1. Adja meg a "Figyelés-NewFileTimestamp" a neve
1. Válassza ki a DateTime típushoz.
1. Kattintson a **létrehozása** gombra. Ezzel létrehozza az automatizálási változó.

## <a name="create-an-action-runbook"></a>Egy művelet runbook létrehozása

Egy művelet runbook megfigyelő feladat hogy intézkedjen a megfigyelő runbook küldött adatok használatos. Ebben a lépésben frissíti import "Folyamat-NewFile" nevű előre definiált művelet runbook.

1. Nyissa meg az automation-fiók, és válassza ki **Runbookok** alatt a **folyamat** kategóriát.
1. Kattintson a **Tallózás gyűjtemény** gombra.
1. "Megfigyelő action" megkereséséhez és kijelöléséhez **megfigyelő művelet, amely feldolgozza a megfigyelő runbook által indított események** válassza **importálási**.
  ![A felhasználói Felületről művelet forgatókönyv importálása](media/automation-watchers-tutorial/importsourceaction.png)
1. A runbook adjon egy nevet és leírást, és válassza ki **OK** a runbook importálása az Automation-fiók.
1. Válassza ki **szerkesztése** majd **közzététel**. Ha a select kéri **Igen** közzétenni a runbookot.

## <a name="create-a-watcher-task"></a>Megfigyelő feladat létrehozása

A megfigyelő feladat tartalmaz két részből áll. A megfigyelő és a műveletet. A megfigyelő fut a figyelő feladat meghatározott időközönként. Megfigyelő runbook adatok kerülnek a művelet a runbook-kiszolgálóra. Ebben a lépésben konfigurálja a megfigyelő feladat az előző lépések során meghatározott felügyeletiszolgáltatás-megfigyelő és a művelet runbookok hivatkozik.

1. Nyissa meg az automation-fiók, és válassza ki **megfigyelő feladatok** alatt a **folyamat** kategóriát.
1. Válassza ki a figyelő feladatok lapján, majd kattintson a **+ adjon hozzá egy megfigyelő feladatot** gombra.
1. Adja meg a "WatchMyFolder" néven.

1. Válassza ki **konfigurálása megfigyelő** válassza ki a **figyelési-NewFile** runbook.

1. Adja meg a következő értékeket a Paraméterek:

   * **FOLDERPATH** -a hibrid feldolgozó egy mappát, ahol új fájlok létrehozása. d:\examplefiles
   * **BŐVÍTMÉNY** -hagyja üresen az összes fájl kiterjesztéseinek feldolgozása.
   * **RECURSE** -hagyja meg az alapértelmezett érték.
   * **Futtatás beállítások** -válassza ki a hibrid feldolgozó.

1. Kattintson az OK gombra, majd válassza ki a figyelő lapra való visszatéréshez.
1. Válassza ki **művelet konfigurálása** válassza ki a "Folyamat-NewFile" runbook.
1. Adja meg a következő értékeket paraméterek:

   *    **EVENTDATA** -hagyja üresen. Adatok kerülnek a megfigyelő runbook.  
   *    **Futtatási beállítások** -Azure hagyja, a runbook fut az Automation szolgáltatás.

1. Kattintson a **OK**, majd válassza ki a figyelő lapra való visszatéréshez.
1. Kattintson a **OK** a megfigyelő feladat létrehozásához.

![A felhasználói Felületről megfigyelő művelet konfigurálása](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Egy figyelő eseményindító

A megfigyelő tesztelése a várt módon működik, egy fájl tesztelése létrehozásához szükséges.

A hibrid feldolgozó történő távoli. Nyissa meg **PowerShell** , és hozzon létre egy teszt fájl a mappában.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

A következő példa bemutatja a várt kimeneti.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Tekintse meg a kimenetet

1. Nyissa meg az automation-fiók, és válassza ki **megfigyelő feladatok** alatt a **folyamat** kategóriát.
1. Válassza ki a figyelő "WatchMyFolder" feladatot.
1. Kattintson a **megfigyelő adatfolyamok megtekintése** alatt **adatfolyamok** , hogy a figyelő az új fájl található, és a művelet a runbook indítása.
1. A művelet runbook-feladatok megtekintéséhez kattintson a a **megfigyelő művelet feladatok megtekintéséhez**. Minden feladat lehet kijelölve a nézet a feladat részleteit.

   ![Megfigyelő műveleti feladatok a felhasználói Felületről](media/automation-watchers-tutorial/WatcherActionJobs.png)

A várt kimenet, amikor az új fájl megtalálható-e az alábbi példában látható:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Megfigyelő runbook importálása
> * Az automatizálási változó létrehozása
> * Egy művelet runbook létrehozása
> * Megfigyelő feladat létrehozása
> * Egy figyelő eseményindító
> * Tekintse meg a kimenetet

Kattintson erre a hivatkozásra a további információk a létrehozásról saját runbook.

> [!div class="nextstepaction"]
> [Az első PowerShell runbook](automation-first-runbook-textual-powershell.md).
