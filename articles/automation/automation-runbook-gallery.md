---
title: Azure Automation forgatókönyv- és katalógusok
description: Runbookjai és moduljai a Microsoft és a Közösség érhetők el, hogy telepítheti és használhatja az Azure Automation-környezetben.  Ez a cikk bemutatja, hogyan férhet hozzá ezekhez az erőforrásokhoz és működhet közre a runbookok a katalógusba.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 68eb3f3e5c568bb518251aca2a4e76932ce2eee5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416207"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation forgatókönyv- és katalógusok

Ahelyett, hogy a saját forgatókönyveit és moduljait létrehozása az Azure Automationben, forgatókönyvek, amelyek már a Microsoft és a Közösség által készített érheti el.

Megjelenik a runbookot a [forgatókönyv-katalógusában](#runbooks-in-runbook-gallery) és a modulok a [PowerShell-galériából](#modules-in-powerShell-gallery).  Is hozzájárulhat a Közösség által megosztási forgatókönyveket, amelyek fejleszt, lásd: [runbook felvétele a katalógusban](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Runbookok a Runbook-katalógus

A [forgatókönyv-katalógusában](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) runbookok különféle biztosít a Microsoft és a Közösség, amelyet importálhat az Azure Automationbe. Valamelyik használja, töltse le egy runbook a katalógusból, amelyet üzemeltet a [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload), vagy a runbookok a katalógusból az Azure Portalon közvetlenül importálhatók.

Csak importálhatja közvetlenül a forgatókönyv-katalógus az Azure portal használatával. Ez a Windows PowerShell-lel függvény nem hajtható végre.

> [!NOTE]
> Ellenőriznie kell a runbookokat, hogy a forgatókönyv-katalógus kérhet, és rendkívül körültekintően járjon el a telepítése és az éles környezetben futtatná őket a tartalmát.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Runbook importálása a Runbook-galériából, az Azure portal használatával

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Folyamatautomatizálás**, kattintson a **runbookkatalógus**
3. Keresse meg a gyűjteményelem szeretne, és válassza ki azt a részletek megtekintéséhez. A bal oldali további keresési paraméterek megadása a közzétevő és típusát.

   ![Tallózás a katalógusban](media/automation-runbook-gallery/browse-gallery.png)

4. Kattintson a **forrásprojekt megtekintése** elem megtekintéséhez a [TechNet Script Center](https://gallery.technet.microsoft.com/).
5. Egy elem importálásához kattintson a részletek megtekintéséhez, majd kattintson rá a **importálása** gombra.

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

6. Szükség esetén módosítsa a runbook nevét, és kattintson a **OK** a runbook importálása.
7. A runbook megjelenik-e a **Runbookok** az Automation-fiókhoz tartozó lapon.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Egy runbook a runbook-katalógus hozzáadása

A Microsoft javasolja, hogy a runbookok a Runbook-katalógus, amely úgy gondolja, hogy más felhasználók számára hasznos lehet.  Hozzáadhat egy runbook által [feltölteni a Parancsfájlközpontban a](https://gallery.technet.microsoft.com/site/upload) figyelembe véve a következő részleteket.

* Meg kell adnia *Windows Azure* számára a **kategória** és *Automation* számára a **alkategória** megjeleníteni a runbook a a varázsló.  
* A feltöltés kell lennie egy `.ps1` vagy `.graphrunbook` fájlt.  Ha a runbook igényel minden olyan modulok, a gyermek runbookok vagy az eszközök, majd sorolja fel azok az elemek leírását, a Küldés és a runbook a Megjegyzések szakaszban.  Ha ez a forgatókönyv több runbook igénylő, töltse fel az egyes külön-külön, és az felsorolja a kapcsolódó runbookok minden ezek leírását. Győződjön meg arról, hogy ugyanazt a címkét használja, így azok megjelennek a kategóriába. Felhasználó fog rendelkezni, olvassa el a leírást, hogy tudja, hogy más runbookokat szükségesek a forgatókönyv működjön.
* Adja hozzá a "GraphicalPS" címkét, ha Ön közzétételi egy **grafikus forgatókönyv** (nem egy grafikus munkafolyamat).
* A leírás segítségével a PowerShell vagy egy PowerShell-munkafolyamat a kódrészlet beszúrása **kód szakaszban beszúrása** ikonra.
* A feltöltés összegzés a Runbook-katalógus eredmény jelenik meg így meg kell adni, amely segít azonosítani az a funkciók a runbook felhasználó részletes információkat.
* A feltöltés kell egy vagy három, a következő címkék hozzárendelése.  A runbook szerepel a varázslóban a kategóriákat, amelyek megfelelnek a címkék területen.  A listában nem szereplő bármely címkék figyelmen kívül hagyja a varázslót. Bármely címke nem ad meg, ha a runbook szerepel, a többi kategóriában.
  
  * Backup
  * Kapacitáskezelés
  * A változások
  * Megfelelőség
  * Fejlesztési és tesztelési környezetek
  * Vészhelyreállítás
  * Figyelés
  * Javítás
  * Kiépítés
  * Szervizelés
  * Virtuális gép életciklus-felügyelete

* Automation frissíti a katalógus óránként egyszer, így azonnal hozzájárulások nem jelenik meg.

## <a name="modules-in-powershell-gallery"></a>A PowerShell-galériában modulok

PowerShell-modulok a runbookokban használható parancsmagokat tartalmaznak, és a meglévő telepítheti az Azure Automation-modulok elérhetők a [PowerShell-galériából](https://www.powershellgallery.com). Indítsa el a katalógus az Azure Portalról, és közvetlenül az Azure Automationbe telepítse őket. Is letöltheti a fájlokat, és telepítse őket manuálisan.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Modul importálása az Automation-modul katalógusból az Azure portal használatával

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki **modulok** alatt **megosztott erőforrások** modulok listájának megnyitásához.
3. Kattintson a **Tallózás a katalógusban** az oldal tetején.

   ![A modul gyűjteménye](media/automation-runbook-gallery/modules-blade.png)

4. Az a **Tallózás a katalógusban** oldalon kereshet a következő mezőket:

   * Modulnév
   * Címkék
   * Szerző
   * A parancsmag/DSC-erőforrás neve

5. Keresse meg az Önt érdeklő modul, és válassza ki azt a részletek megtekintéséhez.  

   Eddig ugyan adott modulok részletesen, megtekintheti további információt. Ezen információk közé tartozik a hivatkozások a PowerShell-galériából, a szükséges függőségeket és a parancsmagok vagy DSC-erőforrások, amely a modul tartalmazza.

   ![PowerShell modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. A modul telepítése közvetlenül az Azure Automationbe, kattintson a **importálás** gombra.
7. Ha az Importálás gombra kattint, a a **importálása** panelen láthatja, hogy Ön importálni kívánt modul nevét. Ha telepítve van a függőségeket, a **OK** gomb aktívvá válik. Ha Ön hiányzik a függőségeket, kell importálja ezeket a függőségeket, ez a modul importálása előtt.
8. Az a **importálása** kattintson **OK** a modul importálásához. Bár az Azure Automation-fiókjába importálja a modult, kinyeri a metaadatokat, a modul és a parancsmagok. Ez a művelet eltarthat néhány perc alatt, mivel minden egyes tevékenységhez szükséges kinyerni.
9.  Befejezése után egy, hogy a modul parancsfájlműveletekkel értesítése és a egy másik értesítést kap.
10. A modul importálása után megjelenik az elérhető tevékenységeket. A runbookok és a Desired State Configuration erőforrásaihoz is használhatja.

> [!NOTE]
> A PowerShell core támogató modulok nem támogatottak az Azure Automationben, és nem lehet importálni az Azure Portalon, vagy közvetlenül a PowerShell-galériából helyezik üzembe.

## <a name="python-runbooks"></a>Python-Runbookok

Python-Runbookok érhetők el a [Script Center-galéria](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). A Script Center-galéria Python runbookok közreműködhet. Ha Ön, győződjön meg arról, hogy a címke hozzáadása **Python** főágra feltöltésekor.

## <a name="requesting-a-runbook-or-module"></a>Egy runbook vagy a modul kérése

Kérelmek küldése [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Runbookok írása segítségre van szüksége, vagy PowerShell kérdése van, ha új kérdést tenne fel, hogy [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>További lépések

* Forgatókönyvekkel való ismerkedéshez tekintse meg [kezelése az Azure Automation forgatókönyv](manage-runbooks.md)
* A forgatókönyvek PowerShell és a PowerShell-munkafolyamat közötti különbségek ismertetése: [Learning PowerShell-munkafolyamat](automation-powershell-workflow.md)