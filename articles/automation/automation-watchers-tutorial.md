---
title: Figyelőfeladat létrehozása az Azure Automation-fiókban
description: Ismerje meg, hogyan hozhat létre figyelőfeladatot az Azure Automation-fiókban a mappában létrehozott új fájlok figyeléséhez.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617348"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Hozzon létre egy Azure Automation-figyelő feladatokat a fájlváltozások helyi gépen történő nyomon követéséhez

Az Azure Automation egy figyelő feladatot használ az események megkereséséhez és műveletek aktiválásához a PowerShell runbookokkal. A figyelő feladat két részből áll, a figyelő ből és az akcióból. A figyelő runbook fut a figyelő feladatban meghatározott időközönként, és az adatok kimenete egy művelet Runbook. 

Ez az oktatóanyag végigvezeti egy figyelőfeladat létrehozásán, amely figyeli, ha új fájlt ad hozzá egy könyvtárhoz. Az alábbiak végrehajtásának módját ismerheti meg:

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
* PowerShell-runbookok. A PowerShell-munkafolyamat runbookjait a figyelőfeladatok nem támogatják.

> [!NOTE]
> Figyelő feladatok nem támogatottak az Azure China.

## <a name="import-a-watcher-runbook"></a>Figyelő runbookjának importálása

Ez az oktatóanyag a **Watch-NewFile** nevű figyelő runbookot használja a könyvtárban lévő új fájlok megkereséséhez. A figyelő runbook lekéri az utolsó ismert írási idő a fájlokat egy mappába, és megnézi a fájlokat újabb, mint a vízjel.

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com)keresztül hajtható el.

1. Keresse meg a [Watch-NewFile.ps1 gyűjteménylapját.](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)
2. Az **Azure Automation** lapon kattintson a **Telepítés az Azure Automationbe gombra.**

Ezt a runbookot a portálról is importálhatja az automation-fiókba a következő lépésekkel.

1. Nyissa meg az Automation-fiókot, és kattintson a Runbooks lapra.
2. Kattintson **a Galéria tallózása gombra.**
3. Keresse meg a **Watcher runbookot,** válassza a **Figyelő runbookját, amely új fájlokat keres egy könyvtárban,** és kattintson **az Importálás gombra.**
  ![Automatizálási runbook importálása a felhasználói felületről](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Adjon nevet és leírást a runbooknak, majd kattintson az **OK** gombra a runbook automation-fiókba való importálásához.
5. Kattintson a **Szerkesztés,** majd **a Közzététel gombra.** Amikor a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-an-automation-variable"></a>Automatizálási változó létrehozása

Az [automatizálási változó](automation-variables.md) az előző runbook által beolvasást és az egyes fájlokból tárolt időbélyegek tárolására szolgál.

1. Válassza **a Változók lehetőséget a** Megosztott erőforrások **csoportban,** és kattintson a + Változó hozzáadása **gombra**.
1. Írja be a névhez a Watch-NewFileTimestamp értéket.
1. Válassza a DateTime lehetőséget a típushoz.
1. Kattintson a **Létrehozás** gombra az Automation változó létrehozásához.

## <a name="create-an-action-runbook"></a>Műveletrunbook létrehozása

Egy művelet runbook egy figyelő feladat, hogy járjon el a figyelő runbook átadott adatokat. A [PowerShell-tárból](https://www.powershellgallery.com)importálnia kell egy **folyamat-újfájl** nevű, előre definiált műveletrunbookot. 

Műveletrunbook létrehozása:

1. Keresse meg a [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)gyűjteménylapját.
2. Az **Azure Automation** lapon kattintson a **Telepítés az Azure Automationbe gombra.**

Ezt a runbookot az Automation-fiókba is importálhatja az Azure Portalon:

1. Nyissa meg az Automation-fiókot, és válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás**csoportban.
1. Kattintson **a Galéria tallózása gombra.**
1. Keresse meg a **Figyelő műveletet,** válassza **a Figyelő műveletet, amely feldolgozza a figyelő runbook által kiváltott eseményeket,** majd kattintson az Importálás **gombra.**
  ![Művelet-runbook importálása a felhasználói felületről](media/automation-watchers-tutorial/importsourceaction.png)
1. Adjon nevet és leírást a runbooknak, majd kattintson az **OK** gombra a runbook automation-fiókba való importálásához.
1. Kattintson a **Szerkesztés,** majd **a Közzététel gombra.** Amikor a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-a-watcher-task"></a>Figyelőfeladat létrehozása

Ebben a lépésben konfigurálja a figyelő feladat hivatkozva a figyelő és a művelet runbookok az előző szakaszokban meghatározott.

1. Nyissa meg az Automation-fiókot, és válassza **a Figyelő feladatait** a **Folyamatautomatizálás csoportban.**
1. Jelölje ki a Figyelő feladatait tartalmazó lapot, és kattintson **a + Figyelő feladat hozzáadása gombra**.
1. Írja be **a WatchMyFolder mappát** névként.

1. Válassza **a Figyelő konfigurálása** lehetőséget, és válassza a **Watch-NewFile** runbook lehetőséget.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** - A hibrid runbook-feldolgozó egy mappája, ahol új fájlok jönnek létre, például **d:\examplefiles**.
   * **EXTENSION** - A konfiguráció kiterjesztése. Hagyja üresen az összes fájlkiterjesztés feldolgozásához.
   * **RECURSE** - Rekurzív művelet. Hagyja ezt az értéket alapértelmezettként.
   * **RUN SETTINGS** - A runbook futtatásának beállítása. Válassza ki a hibrid dolgozót.

1. Kattintson **az OK**gombra, majd a **Kijelölés gombra** a Figyelő lapra való visszatéréshez.
1. Válassza **a Művelet konfigurálása** lehetőséget, és válassza a **Folyamat-NewFile** runbook lehetőséget.
1. Adja meg a következő értékeket a paraméterekhez:

   * **EVENTDATA** - Eseményadatok. Hagyja üresen. Az adatok a figyelő runbookból származnak.
   * **Beállítások futtatása** – A runbook futtatásának beállítása. Hagyja azure-ként, mivel ez a runbook fut az Azure Automationben.

1. Kattintson **az OK**gombra, majd a **Kijelölés gombra** a Figyelő lapra való visszatéréshez.
1. A figyelőfeladat létrehozásához kattintson az **OK** gombra.

![Figyelőművelet konfigurálása a felhasználói felületről](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Figyelő beindíthatása

Az alábbiakban ismertetett tesztet kell futtatnia annak érdekében, hogy a figyelő feladat a várt módon működjön. 

1. Távoli a hibrid Runbook-feldolgozó. 
2. Nyissa meg **a PowerShellt,** és hozzon létre egy tesztfájlt a mappában.

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

1. Nyissa meg az Automation-fiókot, és válassza **a Figyelő feladatait** a **Folyamatautomatizálás csoportban.**
1. Jelölje ki a **WatchMyFolder**figyelő feladatot.
1. Kattintson a **Figyelő streamek megtekintése** a **Streams** alatt, hogy lássa, hogy a figyelő megtalálta-e az új fájlt, és elindította a művelet runbookját.
1. A művelet runbook-feladatok megtekintéséhez kattintson a **Figyelők műveletfeladatai megtekintése**elemre. Minden feladat kiválasztható a feladat részleteinek megtekintéséhez.

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

