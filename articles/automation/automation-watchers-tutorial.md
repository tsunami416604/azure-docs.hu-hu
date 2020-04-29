---
title: Figyelő feladat létrehozása a Azure Automation fiókban
description: Megtudhatja, hogyan hozhat létre figyelő feladatot a Azure Automation-fiókban egy mappában létrehozott új fájlok megtekintéséhez.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617348"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Azure Automation figyelőkkel kapcsolatos feladatok létrehozása a fájlok változásainak követéséhez egy helyi gépen

A Azure Automation egy megfigyelői feladattal keres eseményeket, és elindítja a műveleteket a PowerShell-runbookok használatával. A figyelő feladat két részből áll: a figyelőkből és a műveletből. A figyelők runbook a figyelő feladatban meghatározott időközönként futnak, és adatokat küld egy műveleti runbook. 

Ez az oktatóanyag végigvezeti egy figyelő feladat létrehozásán, amely figyeli, hogy új fájlt adnak-e hozzá egy címtárhoz. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Figyelők runbook importálása
> * Automation-változó létrehozása
> * Művelet runbook létrehozása
> * Figyelőfeladat létrehozása
> * Figyelő indítása
> * A kimenet vizsgálata

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és a műveleti runbookok és a figyelő feladat tárolására.
* Egy [hibrid runbook](automation-hybrid-runbook-worker.md) -feldolgozó, amelyben a figyelő tevékenység fut.
* PowerShell-runbookok. A figyelők feladatai nem támogatják a PowerShell-munkafolyamatok runbookok.

> [!NOTE]
> Az Azure China nem támogatja a figyelők feladatait.

## <a name="import-a-watcher-runbook"></a>Figyelők runbook importálása

Ez az oktatóanyag egy **Watch-NewFile** nevű figyelő runbook használ a címtárban lévő új fájlok kereséséhez. A figyelő runbook lekéri az utolsó ismert írási időt a mappában található fájlokra, és a vízjelnél újabb fájlokat keres.

Ez az importálási folyamat a [PowerShell-galériaon](https://www.powershellgallery.com)keresztül végezhető el.

1. Navigáljon a [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)katalógus lapjára.
2. A **Azure Automation** lapon kattintson a telepítés elemre **Azure Automation**lehetőségre.

Ezt a runbook a portál Automation-fiókjába is importálhatja a következő lépések végrehajtásával.

1. Nyissa meg az Automation-fiókját, és kattintson a Runbookok lapra.
2. Kattintson a **Tallózás**katalógus lehetőségre.
3. Keresse meg a **figyelők runbook**, válassza ki a **figyelők runbook, amely az új fájlokat keresi egy könyvtárban**, és kattintson az **Importálás**elemre.
  ![Automatizálási runbook importálása felhasználói felületről](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Adja meg a runbook nevét és leírását, majd az **OK** gombra kattintva importálja a Runbook az Automation-fiókjába.
5. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel**elemre. Ha a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-an-automation-variable"></a>Automation-változó létrehozása

Az [Automation változó](automation-variables.md) az előző runbook által beolvasott és az egyes fájlokban tárolt időbélyegek tárolására szolgál.

1. Válasszon **változókat** a **megosztott erőforrások** területen, majd kattintson **a + változó hozzáadása**lehetőségre.
1. Adja meg a NewFileTimestamp nevet a név mezőben.
1. A típushoz válassza a DateTime értéket.
1. Az Automation-változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="create-an-action-runbook"></a>Művelet runbook létrehozása

A figyelő feladatban egy runbook, amely az átadott adatoknak a megfigyelő runbook való működéséhez használatos. A [PowerShell-Galéria](https://www.powershellgallery.com)a **Process-NewFile** nevű előre definiált műveleti runbook kell importálnia. 

Művelet runbook létrehozása:

1. Navigáljon a [Process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)katalógus lapjára.
2. A **Azure Automation** lapon kattintson a telepítés elemre **Azure Automation**lehetőségre.

Ezt a runbook az Automation-fiókjába is importálhatja a Azure Portal:

1. Navigáljon az Automation-fiókjához, és válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
1. Kattintson a **Tallózás**katalógus lehetőségre.
1. Keresse meg a **figyelők műveletet**, válassza a **figyelők művelet lehetőséget, amely feldolgozza a megfigyelő runbook által aktivált eseményeket**, majd kattintson az **Importálás**gombra.
  ![Művelet runbook importálása a felhasználói felületen](media/automation-watchers-tutorial/importsourceaction.png)
1. Adja meg a runbook nevét és leírását, majd az **OK** gombra kattintva importálja a Runbook az Automation-fiókjába.
1. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel**elemre. Ha a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-a-watcher-task"></a>Figyelőfeladat létrehozása

Ebben a lépésben az előző szakaszokban meghatározott figyelőre és műveleti runbookok hivatkozó figyelő feladatot konfigurálja.

1. Navigáljon az Automation-fiókjához, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása**alatt.
1. Válassza ki a megfigyelő tevékenységek lapot, és kattintson a **+ figyelők hozzáadása feladat**elemre.
1. Adja meg a **WatchMyFolder** nevet.

1. Válassza a **figyelő konfigurálása** lehetőséget, és válassza ki a **Watch-NewFile** runbook.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** – a hibrid Runbook-feldolgozó azon mappája, ahol új fájlok jönnek létre (például **d:\examplefiles**).
   * **Bővítmény** – bővítmény a konfigurációhoz. Hagyja üresen az összes fájlkiterjesztés feldolgozását.
   * **Reátok** – rekurzív művelet. Hagyja meg az alapértelmezett értéket.
   * **Futtatási beállítások** – a runbook futtatásának beállítása. Válassza ki a hibrid feldolgozót.

1. Kattintson az **OK gombra**, majd **válassza a lehetőséget** , ha vissza szeretne térni a figyelők oldalára.
1. Válassza a **művelet beállítása** elemet, és válassza a **Process-NewFile** runbook.
1. Adja meg a következő értékeket a paraméterekhez:

   * **EVENTDATA** -események. Hagyja üresen. Az adatok bekerülnek a figyelő runbook.
   * **Futtatási beállítások** – a runbook futtatásának beállítása. Hagyja meg az Azure-t, mivel ez a runbook Azure Automation fut.

1. Kattintson az **OK gombra**, majd **válassza a lehetőséget** , ha vissza szeretne térni a figyelők oldalára.
1. A figyelő feladat létrehozásához kattintson **az OK** gombra.

![Figyelő művelet konfigurálása a felhasználói felületen](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Figyelő indítása

Az alább leírtak szerint futtatnia kell egy tesztet annak biztosításához, hogy a figyelő tevékenység a várt módon működjön. 

1. Távoli a hibrid Runbook-feldolgozóval. 
2. Nyissa meg a **PowerShellt** , és hozzon létre egy tesztoldalt a mappában.

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

1. Navigáljon az Automation-fiókjához, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása**alatt.
1. Válassza ki a figyelő tevékenység **WatchMyFolder**.
1. Kattintson a **figyelő streamek megtekintése** **streamek** alatt lehetőségre, hogy a figyelő megtalálta az új fájlt, és elindította a művelet runbook.
1. A művelet runbook feladatainak megtekintéséhez kattintson a **figyelő műveleti feladatok megtekintése**elemre. Mindegyik feladatot kiválaszthatja a feladatok részleteinek megtekintéséhez.

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
> * Figyelőfeladat létrehozása
> * Figyelő indítása
> * A kimenet vizsgálata

Kövesse ezt a hivatkozást, ha többet szeretne megtudni a saját runbook létrehozásáról.

> [!div class="nextstepaction"]
> [Az első PowerShell-runbook](automation-first-runbook-textual-powershell.md).

