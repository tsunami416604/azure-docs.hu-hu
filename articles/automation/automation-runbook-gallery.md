---
title: Azure Automation forgatókönyv- és katalógusok
description: Runbookjai és moduljai a Microsoft és a Közösség érhetők el, hogy telepítheti és használhatja az Azure Automation-környezetben.  Ez a cikk bemutatja, hogyan férhet hozzá ezekhez az erőforrásokhoz és működhet közre a runbookok a katalógusba.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca19ce2cca314950adc40bbf065dec80e7fa3e1f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227926"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation forgatókönyv- és katalógusok
Ahelyett, hogy a saját forgatókönyveit és moduljait létrehozása az Azure Automationben, számos forgatókönyv esetében, amely már a Microsoft és a Közösség által készített érheti el.  Ezekben a forgatókönyvekben, módosítás nélkül használhatja, vagy kiindulási pontként használhatja őket, és az adott igények szerint szerkesztheti őket.

Megjelenik a runbookot a [forgatókönyv-katalógusában](#runbooks-in-runbook-gallery) és a modulok a [PowerShell-galériából](#modules-in-powerShell-gallery).  Is hozzájárulhat a Közösség által megosztási forgatókönyveket, amelyek fejleszt, lásd: [runbook felvétele a katalógusban](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Runbookok a Runbook-katalógus
A [forgatókönyv-katalógusában](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) runbookok különféle biztosít a Microsoft és a Közösség, amelyet importálhat az Azure Automationbe. Letöltheti a runbook a katalógusból, amelyet üzemeltet a [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload), vagy a runbookok a katalógusból az Azure Portalon közvetlenül importálhatók.

Csak importálhatja közvetlenül a forgatókönyv-katalógus az Azure portal használatával. Ez a Windows PowerShell-lel függvény nem hajtható végre.

> [!NOTE]
> Ellenőriznie kell a runbookokat, hogy a forgatókönyv-katalógus kérhet, és rendkívül körültekintően járjon el a telepítése és az éles környezetben futtatná őket a tartalmát.
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Runbook importálása a Runbook-galériából, az Azure portal használatával
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Folyamatautomatizálás**, kattintson a **runbookkatalógus**
3. Keresse meg a gyűjteményelem szeretne, és válassza ki azt a részletek megtekintéséhez. A bal oldali további keresési paraméterek megadása a közzétevő és típusát.
   
    ![Tallózás a katalógusban](media/automation-runbook-gallery/browse-gallery.png)
5. Kattintson a **forrásprojekt megtekintése** elem megtekintéséhez a [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Egy elem importálásához kattintson a részletek megtekintéséhez, majd kattintson rá a **importálása** gombra.
   
    ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)
7. Szükség esetén módosítsa a runbook nevét, és kattintson a **OK** a runbook importálása.
8. A runbook megjelenik-e a **Runbookok** az Automation-fiókhoz tartozó lapon.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Egy runbook a runbook-katalógus hozzáadása
A Microsoft javasolja, hogy a runbookok a Runbook-katalógus, amely úgy gondolja, hogy más felhasználók számára hasznos lehet.  Hozzáadhat egy runbook által [feltölteni a Parancsfájlközpontban a](https://gallery.technet.microsoft.com/site/upload) figyelembe véve a következő részleteket.

* Meg kell adnia *Windows Azure* számára a **kategória** és *Automation* számára a **alkategória** megjeleníteni a runbook a a varázsló.  
* A feltöltés egyetlen .ps1 vagy .graphrunbook fájlnak kell lennie.  Ha a runbook igényel minden olyan modulok, a gyermek runbookok vagy az eszközök, majd sorolja fel azokat a Küldés leírását és a runbook a Megjegyzések szakaszban.  Ha ez a forgatókönyv több runbook igénylő, töltse fel az egyes külön-külön, és az felsorolja a kapcsolódó runbookok minden ezek leírását. Győződjön meg arról, hogy ugyanazt a címkét használja, így azok megjelennek a kategóriába. Felhasználó fog rendelkezni, olvassa el a leírást, hogy tudja, hogy más runbookokat szükségesek a forgatókönyv működjön.
* Adja hozzá a címke "GraphicalPS", ha közzéteszi a **grafikus forgatókönyv** (nem egy grafikus munkafolyamat). 
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
PowerShell-modulok a runbookokban használható parancsmagokat tartalmaznak, és a meglévő telepítheti az Azure Automation-modulok elérhetők a [PowerShell-galériából](http://www.powershellgallery.com).  Indítsa el a katalógus az Azure Portalról, és telepítse őket közvetlenül az Azure Automationbe, vagy letöltheti a fájlokat, és telepítse őket manuálisan.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Modul importálása az Automation-modul katalógusból az Azure portal használatával
1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza ki **modulok** alatt **megosztott erőforrások** modulok listájának megnyitásához.
4. Kattintson a **Tallózás a katalógusban** az oldal tetején.
   
    ![A modul gyűjteménye](media/automation-runbook-gallery/modules-blade.png) <br>
5. Az a **Tallózás a katalógusban** oldalon kereshet a következő mezőket:
   
   * Modulnév
   * Címkék
   * Szerző
   * A parancsmag/DSC-erőforrás neve
6. Keresse meg az Önt érdeklő modul, és válassza ki azt a részletek megtekintéséhez.  
   További információk megtekintheti egy adott modul részletesen, a modult, és a telepítőre mutató vissza a PowerShell-galériából kapcsolatos előírt függőségeket, és az összes, a parancsmagok és/vagy DSC-erőforrások, amely a modul tartalmazza.
   
    ![PowerShell modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. A modul telepítése közvetlenül az Azure Automationbe, kattintson a **importálás** gombra.
8. Ha az Importálás gombra kattint, a a **importálása** panelen láthatja a modul neve, amely az importálni kívánt. Ha telepítve van a függőségeket, a **OK** gomb aktívvá válik. Ha függőségei hiányoznak, meg kell importálhatja azokat, ez a modul importálása előtt.
9. Az a **importálása** kattintson **OK** a modul importálásához. Bár az Azure Automation-fiókjába importálja a modult, kinyeri a metaadatokat, a modul és a parancsmagok. Ez eltarthat néhány percig, óta kinyerni kell minden egyes tevékenységhez.
10. Befejezése után egy, hogy a modul parancsfájlműveletekkel értesítése és a egy másik értesítést kap.
11. A modul importálása után megtekintheti az elérhető tevékenységeket, és használhatja az erőforrások a runbookok és a Desired State Configuration.

> [!NOTE]
> A PowerShell core támogató modulok nem támogatottak az Azure Automationben, és nem lehet importálni az Azure Portalon, vagy közvetlenül a PowerShell-galériából helyezik üzembe.

## <a name="python-runbooks"></a>Python-Runbookok

Python-Runbookok érhetők el a [Script Center-galéria](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). A Script Center-galéria Python runbookok közreműködhet. Ha Ön, győződjön meg arról, hogy a címke hozzáadása **Python** főágra feltöltésekor.

## <a name="requesting-a-runbook-or-module"></a>Egy runbook vagy a modul kérése
Kérelmek küldése [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Súgó írása egy runbook van szüksége, vagy PowerShell kérdése van, ha új kérdést tenne fel, hogy [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>További lépések
* Forgatókönyvekkel való ismerkedéshez tekintse meg [létrehozása vagy importálása az Azure Automation-runbook](automation-creating-importing-runbook.md)
* A forgatókönyvek PowerShell és a PowerShell-munkafolyamat közötti különbségek ismertetése: [Learning PowerShell-munkafolyamat](automation-powershell-workflow.md)

