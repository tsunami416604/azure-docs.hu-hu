---
title: Figyelőfeladat létrehozása az Azure Automation-fiókban
description: Ismerje meg, hogyan figyelőfeladat létrehozása az Azure Automation-fiókban, mappában létrehozott új fájlok megtekintéshez.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 2786de150307b21b06b624914d5fea55ded6e3c7
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249790"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Az Azure Automation figyelőjét fájl változásainak követése a helyi számítógép-feladatok létrehozása

Az Azure Automation figyelőfeladatok események és műveletek PowerShell-forgatókönyvekkel való indításához használja. Ez az oktatóanyag végigvezeti a figyelő észleli, ha egy új fájlt adnak hozzá egy könyvtárat a figyelőfeladat létrehozása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megfigyelő runbook importálása
> * Az automatizálási változó létrehozása
> * Egy művelet runbook létrehozása
> * Figyelőfeladat létrehozása
> * Eseményindító-figyelő
> * Tekintse meg a kimenetet

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és műveleti runbookok, valamint a Figyelőfeladat tárolásához.
* A [hibrid runbook-feldolgozó](automation-hybrid-runbook-worker.md) ahol a figyelőfeladat fut-e.

## <a name="import-a-watcher-runbook"></a>Megfigyelő runbook importálása

Ebben az oktatóanyagban egy figyelői runbookból nevű **Watch-NewFile** új fájlokat egy könyvtárban. A megfigyelő runbook egy mappában található fájlokat a legutóbbi ismert írási kérdezi le, és megvizsgálja a fájlokat a küszöbértéket tartalmazó támogatottnál. Ebben a lépésben a runbook az automation-fiókba importálásához.

1. Nyissa meg az Automation-fiók, és kattintson a **Runbookok** lapot.
2. Kattintson a **Tallózás a katalógusban** gombra.
3. Keressen a "Megfigyelő runbook", válassza ki **keres. új fájlok egy figyelői runbookból** válassza **importálás**.
  ![Automation-runbook importálása a felhasználói felületen](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Adja a forgatókönyvnek, egy nevet és leírást, majd válassza **OK** importálása a runbook az Automation-fiókba.
1. Válassza ki **szerkesztése** majd **közzététel**. Amikor a rendszer kéri, válassza ki **Igen** közzétenni a runbookot.

## <a name="create-an-automation-variable"></a>Az automatizálási változó létrehozása

Egy [automation változó](automation-variables.md) tárolja az időbélyegeket az előző runbook olvasó, és minden egyes fájl tárolja.

1. Válassza ki **változók** alatt **megosztott erőforrások** válassza **+ változó hozzáadása**.
1. Adja meg a "Watch-NewFileTimestamp" nevet
1. Válassza ki a dátum/idő típus.
1. Kattintson a **létrehozás** gombra. Ezzel létrehozza az automation változó.

## <a name="create-an-action-runbook"></a>Egy művelet runbook létrehozása

Egy művelet runbookot szolgál a figyelőfeladat reagálhat rájuk, egy figyelői runbookból átadott adatok. Figyelőfeladatok nem támogatja a PowerShell-munkafolyamati runbookok, a PowerShell-runbookok kell használnia. Ebben a lépésben importálás frissítése "Folyamat-NewFile" nevű előre meghatározott művelet runbookot.

1. Az automation-fiókjában keresse meg és válassza **Runbookok** alatt a **FOLYAMATAUTOMATIZÁLÁS** kategória.
1. Kattintson a **Tallózás a katalógusban** gombra.
1. "Figyelőművelet" keresése és kiválasztása **figyelőművelet, egy figyelői runbookból által indított események feldolgozó** válassza **importálása**.
  ![A művelet runbook importálása a felhasználói felületen](media/automation-watchers-tutorial/importsourceaction.png)
1. Adja a forgatókönyvnek, egy nevet és leírást, majd válassza **OK** importálása a runbook az Automation-fiókba.
1. Válassza ki **szerkesztése** majd **közzététel**. Amikor a rendszer kéri, válassza ki **Igen** közzétenni a runbookot.

## <a name="create-a-watcher-task"></a>Figyelőfeladat létrehozása

A figyelőfeladat két részt tartalmaz. A megfigyelő és a műveletet. A figyelő a figyelőfeladat meghatározott időközönként futtatja. A megfigyelő runbook adatainak rendszer továbbította a művelet runbookot. Ebben a lépésben konfigurálja a figyelőfeladat az előző lépések során meghatározott megfigyelő és műveleti runbookok hivatkozik.

1. Az automation-fiókjában keresse meg és válassza **figyelőfeladatok** alatt a **FOLYAMATAUTOMATIZÁLÁS** kategória.
1. A figyelő feladatok lapon válassza ki, majd kattintson a **+ figyelőfeladat hozzáadása** gombra.
1. Adja meg a "WatchMyFolder" nevet.

1. Válassza ki **konfigurálása megfigyelő** , és válassza ki a **Watch-NewFile** runbook.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** – Ha új fájl jön létre, a hibrid feldolgozón mappa. d:\examplefiles
   * **BŐVÍTMÉNY** – hagyja üresen az összes fájlkiterjesztések feldolgozni.
   * **RECURSE** – hagyja üresen ezt az alapértelmezett értéket.
   * **BEÁLLÍTÁSAI** – válassza ki a hibrid feldolgozó.

1. Kattintson az OK gombra, és válassza ki a figyelő lapra való visszatéréshez.
1. Válassza ki **művelet beállítása** , és válassza ki a "Folyamat-NewFile" runbook.
1. Adja meg a következő értékeket a Paraméterek:

   ***EVENTDATA** – hagyja üresen. Adatok a megfigyelő runbook átadva a.  
   ***Futtatási beállítások** -hagyja meg az Azure, az Automation szolgáltatásban futtatja a runbookot.

1. Kattintson a **OK**, majd válassza ki a figyelő lapra való visszatéréshez.
1. Kattintson a **OK** a figyelőfeladat létrehozása.

![A felhasználói felület figyelőművelet konfigurálása](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Eseményindító-figyelő

A megfigyelő teszteléséhez a várt módon működik, egy tesztfájlt létrehozásához szükséges.

Távolról csatlakozzon a hibrid feldolgozó. Nyissa meg **PowerShell** , és hozzon létre egy tesztfájlt a mappában.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

Az alábbi példa bemutatja a várt kimenetet.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Tekintse meg a kimenetet

1. Az automation-fiókjában keresse meg és válassza **figyelőfeladatok** alatt a **FOLYAMATAUTOMATIZÁLÁS** kategória.
1. Válassza ki a figyelőfeladat "WatchMyFolder".
1. Kattintson a **figyelő-adatfolyamok megtekintése** alatt **adatfolyamok** , hogy a figyelő az új fájl található, és a művelet runbookot elindítani.
1. A művelet a runbook-feladatok megtekintéséhez kattintson a a **figyelő műveleti feladatainak megtekintése**. Minden egyes feladat lehet kijelölve a nézet a feladat részleteit.

   ![Figyelő műveleti feladatainak felhasználói felületről](media/automation-watchers-tutorial/WatcherActionJobs.png)

A várt kimenet, amikor az új fájl található a következő példában látható:

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
> * Figyelőfeladat létrehozása
> * Eseményindító-figyelő
> * Tekintse meg a kimenetet

Erre a hivatkozásra a további információk a létrehozásról saját forgatókönyv.

> [!div class="nextstepaction"]
> [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md).
