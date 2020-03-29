---
title: Figyelőfeladat létrehozása az Azure Automation-fiókban
description: Ismerje meg, hogyan hozhat létre figyelőfeladatot az Azure Automation-fiókban a mappában létrehozott új fájlok figyeléséhez.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75419753"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Hozzon létre egy Azure Automation-figyelő feladatokat a fájlváltozások helyi gépen történő nyomon követéséhez

Az Azure Automation figyelőfeladatok at használ az események figyelésére és műveletek indítására a PowerShell runbookokkal. Ez az oktatóanyag végigvezeti egy figyelőfeladat létrehozásán, amely figyeli, ha új fájlt ad hozzá egy könyvtárhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Figyelő runbookjának importálása
> * Automatizálási változó létrehozása
> * Műveletrunbook létrehozása
> * Figyelőfeladat létrehozása
> * Figyelő beindíthatása
> * Ellenőrizze a kimenetet

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a figyelő és a művelet runbookok és a Figyelő feladat tárolására.
* Egy [hibrid runbook-feldolgozó,](automation-hybrid-runbook-worker.md) ahol a figyelő feladat fut.

> [!NOTE]
> Figyelő feladatok nem támogatottak az Azure China.

## <a name="import-a-watcher-runbook"></a>Figyelő runbookjának importálása

Ez az oktatóanyag a **Watch-NewFile** nevű figyelő runbookot használja a könyvtárban lévő új fájlok megkereséséhez. A figyelő runbook lekéri az utolsó ismert írási idő a fájlokat egy mappába, és megnézi a fájlokat újabb, mint a vízjel.

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com)keresztül hajtható el.

1. Keresse meg a [Watch-NewFile.ps1 gyűjteménylapját.](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)
2. Az **Azure Automation** lapon kattintson a **Telepítés az Azure Automationbe gombra.**

Ezt a runbookot a portálról is importálhatja az automation-fiókba a következő lépésekkel.

1. Nyissa meg az Automation-fiókot, és kattintson a **Runbooks** lapra.
2. Kattintson a **Galéria böngészésgombjára.**
3. Keressen rá a "Watcher runbook" kifejezésre, válassza a **Figyelő runbookját, amely új fájlokat keres egy könyvtárban,** és válassza **az Importálás**lehetőséget.
  ![Automatizálási runbook importálása a felhasználói felületről](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Adjon nevet és leírást a runbooknak, és válassza az **OK gombot** a runbook automation-fiókba való importálásához.
1. Kattintson a **Szerkesztés,** majd **a Közzététel gombra.** Amikor a rendszer kéri, válassza az **Igen** lehetőséget a runbook közzétételéhez.

## <a name="create-an-automation-variable"></a>Automatizálási változó létrehozása

Az [automatizálási változó](automation-variables.md) az előző runbook által beolvasást és az egyes fájlokból tárolt időbélyegek tárolására szolgál.

1. Válassza **a Változók lehetőséget a** MEGOSZTOTT ERŐFORRÁSOK **csoportban,** majd a + Változó **hozzáadása**lehetőséget.
1. Írja be a "Watch-NewFileTimestamp" nevet a névhez
1. Válassza a DateTime lehetőséget a Típushoz.
1. Kattintson a **Létrehozás** gombra. Ez létrehozza az automatizálási változót.

## <a name="create-an-action-runbook"></a>Műveletrunbook létrehozása

Egy művelet runbook egy figyelő feladat, hogy járjon el a figyelő runbook átadott adatokat. A PowerShell-munkafolyamat runbookjait nem támogatják a figyelőfeladatok, a PowerShell-runbookokat kell használnia. Importálnia kell egy előre definiált műveletrunbookot, **a Process-NewFile programot.**

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com)keresztül hajtható el.

1. Keresse meg a [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)gyűjteménylapját.
2. Az **Azure Automation** lapon kattintson a **Telepítés az Azure Automationbe gombra.**

Ezt a runbookot a portálról is importálhatja az automation-fiókba a következő lépésekkel.

1. Nyissa meg az automatizálási fiókot, és válassza a **Runbooks** lehetőséget a **FOLYAMATAUTOMATIZÁLÁS** kategóriában.
1. Kattintson a **Galéria böngészésgombjára.**
1. Keressen rá a "Figyelőművelet" kifejezésre, és válassza **a Figyelő műveletet, amely feldolgozza a figyelő runbookja által kiváltott eseményeket,** és válassza **az Importálás**lehetőséget.
  ![Művelet-runbook importálása a felhasználói felületről](media/automation-watchers-tutorial/importsourceaction.png)
1. Adjon nevet és leírást a runbooknak, és válassza az **OK gombot** a runbook automation-fiókba való importálásához.
1. Kattintson a **Szerkesztés,** majd **a Közzététel gombra.** Amikor a rendszer kéri, válassza az **Igen** lehetőséget a runbook közzétételéhez.

## <a name="create-a-watcher-task"></a>Figyelőfeladat létrehozása

A figyelő feladat két részből áll. A figyelő és az akció. A figyelő a figyelőfeladatban meghatározott időközönként fut. A figyelő runbookból származó adatok átkerülnek a művelet runbook. Ebben a lépésben konfigurálja a figyelő feladat hivatkozva a figyelő és a művelet runbookok az előző lépésekben meghatározott.

1. Nyissa meg az automatizálási fiókot, és válassza a **Figyelő feladatait** a **PROCESS AUTOMATION** kategóriában.
1. Válassza ki a Figyelő feladatok lapját, és kattintson a **+ Figyelő feladat** hozzáadása gombra.
1. Írja be a "WatchMyFolder" nevet.

1. Válassza **a Figyelő konfigurálása** lehetőséget, és válassza a **Watch-NewFile** runbook lehetőséget.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** - A hibrid dolgozó azon mappája, ahol új fájlok jönnek létre. d:\példafájlok
   * **EXTENSION** - Hagyja üresen az összes fájlkiterjesztés feldolgozását.
   * **RECURSE** - Hagyja ezt az értéket alapértelmezettként.
   * **BEÁLLÍTÁSOK FUTTATÁSA** – Válassza ki a hibrid dolgozót.

1. Kattintson az OK gombra, majd a Kijelölés gombra a figyelő oldalára való visszatéréshez.
1. Válassza **a Művelet konfigurálása** lehetőséget, és válassza a "Process-NewFile" runbook (Folyamat-Új fájl) runbook lehetőséget.
1. Adja meg a következő értékeket a paraméterekhez:

   * **EVENTDATA** - Hagyja üresen. Az adatok a figyelő runbookból származnak.
   * **Beállítások futtatása** – Hagyja az Azure-ként, mivel ez a runbook fut az Automation szolgáltatásban.

1. Kattintson **az OK**gombra, majd a Kijelölés gombra a figyelő oldalra való visszatéréshez.
1. A figyelőfeladat létrehozásához kattintson az **OK** gombra.

![Figyelőművelet konfigurálása a felhasználói felületről](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Figyelő beindíthatása

A figyelő várt működésének teszteléséhez létre kell hoznia egy tesztfájlt.

A hibrid dolgozó távoli vezérlése. Nyissa meg **a PowerShellt,** és hozzon létre egy tesztfájlt a mappában.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

A következő példa a várt kimenetet mutatja be.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Ellenőrizze a kimenetet

1. Nyissa meg az automatizálási fiókot, és válassza a **Figyelő feladatait** a **PROCESS AUTOMATION** kategóriában.
1. Válassza ki a "WatchMyFolder" figyelő feladatot.
1. Kattintson a **Figyelő streamek megtekintése** a **Streams** alatt, hogy lássa, hogy a figyelő megtalálta az új fájlt, és elindította a művelet runbookot.
1. A műveletrunbook-feladatok megtekintéséhez kattintson a **Figyelők tevékenységi feladatának megtekintése**elemre. Minden feladat kiválasztható a feladat részleteinek megtekintéséhez.

   ![Figyelőakció-feladatok a felhasználói felületről](media/automation-watchers-tutorial/WatcherActionJobs.png)

Az új fájl megjelenésekor várt kimenet a következő példában látható:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Figyelő runbookjának importálása
> * Automatizálási változó létrehozása
> * Műveletrunbook létrehozása
> * Figyelőfeladat létrehozása
> * Figyelő beindíthatása
> * Ellenőrizze a kimenetet

Ezt a linket követve többet is megtudhat a saját runbook okozásáról.

> [!div class="nextstepaction"]
> [Az első PowerShell runbook](automation-first-runbook-textual-powershell.md).

