---
title: Figyelő feladat létrehozása a Azure Automation fiókban
description: Megtudhatja, hogyan hozhat létre figyelő feladatot a Azure Automation-fiókban egy mappában létrehozott új fájlok megtekintéséhez.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 75341fa2df6972dbf05542577d56ab35315919e6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989234"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Azure Automation figyelőkkel kapcsolatos feladatok létrehozása a fájlok változásainak követéséhez egy helyi gépen

A Azure Automation megfigyelői feladatokkal figyeli az eseményeket, és elindítja a műveleteket a PowerShell-runbookok. Ez az oktatóanyag végigvezeti egy figyelő feladat létrehozásán, amely figyeli, hogy új fájlt adnak-e hozzá egy címtárhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Figyelők runbook importálása
> * Automation-változó létrehozása
> * Művelet runbook létrehozása
> * Figyelő feladat létrehozása
> * Figyelő indítása
> * A kimenet vizsgálata

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és a műveleti runbookok és a figyelő feladat tárolására.
* Egy [hibrid runbook](automation-hybrid-runbook-worker.md) -feldolgozó, amelyben a figyelő tevékenység fut.

> [!NOTE]
> Az Azure China nem támogatja a figyelők feladatait.

## <a name="import-a-watcher-runbook"></a>Figyelők runbook importálása

Ez az oktatóanyag egy **Watch-NewFile** nevű figyelő runbook használ a címtárban lévő új fájlok kereséséhez. A figyelő runbook lekéri az utolsó ismert írási időt a mappában található fájlokra, és a vízjelnél újabb fájlokat keres.

Ez az importálási folyamat a PowerShell-galériaon [](https://www.powershellgallery.com)keresztül végezhető el.

1. Navigáljon a [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)katalógus lapjára.
2. A **Azure Automation** lapon kattintson a telepítés elemre **Azure Automation**lehetőségre.

Ezt a runbook a portál Automation-fiókjába is importálhatja a következő lépések végrehajtásával.

1. Nyissa meg az Automation-fiókját, és kattintson a **runbookok** lapra.
2. Kattintson a **Tallózás** a katalógusban gombra.
3. Keressen rá a "Watcher runbook" kifejezésre, válassza ki a **figyelők runbook, amely új fájlokat keres egy könyvtárban** , és válassza az **Importálás**lehetőséget.
  ![Automatizálási runbook importálása felhasználói felületről](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Adja meg a runbook nevét és leírását, majd kattintson az **OK** gombra a Runbook az Automation-fiókba való importálásához.
1. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel**elemre. Ha a rendszer kéri, válassza az **Igen** lehetőséget a runbook közzétételéhez.

## <a name="create-an-automation-variable"></a>Automation-változó létrehozása

Az [Automation változó](automation-variables.md) az előző runbook által beolvasott és az egyes fájlokban tárolt időbélyegek tárolására szolgál.

1. Válasszon **változókat** a **megosztott erőforrások** területen, majd válassza **a + változó hozzáadása**elemet.
1. A név mezőbe írja be a "Watch-NewFileTimestamp" kifejezést
1. Válassza a dátum és idő értéket.
1. Kattintson a **Létrehozás** gombra. Ez létrehozza az Automation változót.

## <a name="create-an-action-runbook"></a>Művelet runbook létrehozása

A figyelő feladatban egy runbook, amely az átadott adatoknak a megfigyelő runbook való működéséhez használatos. A figyelőkkel kapcsolatos feladatok nem támogatják a PowerShell-munkafolyamatok runbookok, a PowerShell-runbookok kell használnia. A **Process-NewFile**nevű előre definiált műveleti runbook kell importálnia.

Ez az importálási folyamat a PowerShell-galériaon [](https://www.powershellgallery.com)keresztül végezhető el.

1. Navigáljon a [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)katalógus lapjára.
2. A **Azure Automation** lapon kattintson a telepítés elemre **Azure Automation**lehetőségre.

Ezt a runbook a portál Automation-fiókjába is importálhatja a következő lépések végrehajtásával.

1. Navigáljon az Automation-fiókjához, és válassza a **runbookok** lehetőséget a **folyamat automatizálása** kategóriában.
1. Kattintson a **Tallózás** a katalógusban gombra.
1. Keressen rá a "figyelő művelet" kifejezésre, és válassza a **figyelők művelet lehetőséget, amely feldolgozza a megfigyelő runbook által aktivált eseményeket** , majd válassza az **Importálás**lehetőséget.
  ![Művelet runbook importálása a felhasználói felületen](media/automation-watchers-tutorial/importsourceaction.png)
1. Adja meg a runbook nevét és leírását, majd kattintson az **OK** gombra a Runbook az Automation-fiókba való importálásához.
1. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel**elemre. Ha a rendszer kéri, válassza az **Igen** lehetőséget a runbook közzétételéhez.

## <a name="create-a-watcher-task"></a>Figyelő feladat létrehozása

A figyelő feladat két részből áll. A figyelő és a művelet. A figyelő a figyelő feladatban meghatározott időközönként fut. A figyelő runbook származó adatok át lettek adva a művelet runbook. Ebben a lépésben az előző lépésekben meghatározott figyelőre és műveleti runbookok hivatkozó figyelő feladatot konfigurálja.

1. Navigáljon az Automation-fiókhoz, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása** kategóriában.
1. Válassza ki a megfigyelő tevékenységek lapot, és kattintson a **+ figyelő feladatának hozzáadása** gombra.
1. A név mezőben adja meg a "WatchMyFolder" értéket.

1. Válassza a **figyelő konfigurálása** lehetőséget, és válassza ki a **Watch-NewFile** runbook.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** – a hibrid feldolgozó azon mappája, ahol új fájlok jönnek létre. d:\examplefiles
   * **Kiterjesztés** – hagyja üresen az összes fájlkiterjesztés feldolgozását.
   * **Recurse** – ezt az értéket hagyja alapértelmezettként.
   * **Futtatási beállítások** – válassza ki a hibrid feldolgozót.

1. Kattintson az OK gombra, majd válassza a lehetőséget, ha vissza szeretne térni a figyelők oldalára.
1. Válassza a **művelet beállítása** elemet, és válassza a "Process-NewFile" runbook.
1. Adja meg a következő értékeket a paraméterekhez:

   * **EVENTDATA** – hagyja üresen. Az adatok bekerülnek a figyelő runbook.
   * **Beállítások futtatása** – az Azure-ba való kilépés az Automation szolgáltatás runbook fut.

1. Kattintson az **OK gombra**, majd válassza a lehetőséget, ha vissza szeretne térni a figyelők oldalára.
1. A figyelő feladat létrehozásához kattintson **az OK** gombra.

![Figyelő művelet konfigurálása a felhasználói felületen](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Figyelő indítása

Ha tesztelni szeretné, hogy a figyelő a várt módon működik-e, létre kell hoznia egy tesztelési fájlt.

Távoli a hibrid feldolgozóba. Nyissa meg a **PowerShellt** , és hozzon létre egy tesztoldalt a mappában.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

A következő példa a várt kimenetet mutatja.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>A kimenet vizsgálata

1. Navigáljon az Automation-fiókhoz, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása** kategóriában.
1. Válassza ki a "WatchMyFolder" figyelő feladatot.
1. Kattintson a **figyelő streamek megtekintése** a **streamek** alatt lehetőségre, hogy megtekintse a figyelőt, és megkezdte a művelet runbook.
1. A művelet runbook kapcsolatos feladatok megtekintéséhez kattintson a **figyelők megtekintése műveletre**. Mindegyik feladatot kiválaszthatja a feladatok részleteinek megtekintéséhez.

   ![Figyelő műveleti feladatai a felhasználói felületen](media/automation-watchers-tutorial/WatcherActionJobs.png)

Az új fájl észlelésekor várható kimenet az alábbi példában látható:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Figyelők runbook importálása
> * Automation-változó létrehozása
> * Művelet runbook létrehozása
> * Figyelő feladat létrehozása
> * Figyelő indítása
> * A kimenet vizsgálata

Kövesse ezt a hivatkozást, ha többet szeretne megtudni a saját runbook létrehozásáról.

> [!div class="nextstepaction"]
> [Az első PowerShell-runbook](automation-first-runbook-textual-powershell.md).

