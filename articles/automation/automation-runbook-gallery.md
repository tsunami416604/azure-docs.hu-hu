---
title: Azure Automation forgatókönyv- és katalógusok
description: Runbookjai és moduljai a Microsoft és a Közösség érhetők el, hogy telepítheti és használhatja az Azure Automation-környezetben.  Ez a cikk bemutatja, hogyan férhet hozzá ezekhez az erőforrásokhoz és működhet közre a runbookok a katalógusba.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 58f666d7ebf8ac02d393a42f55e00f08d82b8cae
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337087"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation forgatókönyv- és katalógusok

Ahelyett, hogy a saját forgatókönyveit és moduljait létrehozása az Azure Automationben, forgatókönyvek, amelyek már a Microsoft és a Közösség által készített érheti el.

Megjelenik a [PowerShell-forgatókönyvek](#runbooks-in-runbook-gallery) és [modulok](#modules-in-powerShell-gallery) a PowerShell-galériából és [Python runbookok](#python-runbooks) a Script Center-katalógusból. Is hozzájárulhat a Közösség által megosztási forgatókönyveket, amelyek fejleszt, lásd: [runbook felvétele a katalógusban](#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-powershell-gallery"></a>Runbookok a PowerShell-galériában

A [PowerShell-galériából](https://www.powershellgallery.com/packages) runbookok különféle biztosít a Microsoft és a Közösség, amelyet importálhat az Azure Automationbe. Az egyik használatához egy runbookot letöltését a katalógusban, vagy közvetlenül runbookok importálhatók az Automation-fiókját az Azure Portalon vagy a katalógusból.

Csak importálhatja közvetlenül a PowerShell-galériából az Azure portal használatával. Ez a funkció PowerShell használatával nem hajtható végre.

> [!NOTE]
> Ellenőriznie kell a runbookokat, hogy a PowerShell-galériából lekérése, és rendkívül körültekintően járjon el a telepítése és az éles környezetben futtatná őket a tartalmát.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálásához a forgatókönyv-katalógus az Azure portal használatával

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Folyamatautomatizálás**, kattintson a **runbookkatalógus**
3. Válassza ki **forrás: PowerShell-galériából**.
4. Keresse meg a gyűjteményelem szeretne, és válassza ki azt a részletek megtekintéséhez. A bal oldali további keresési paraméterek megadása a közzétevő és típusát.

   ![Tallózás a katalógusban](media/automation-runbook-gallery/browse-gallery.png)

5. Kattintson a **forrásprojekt megtekintése** elem megtekintéséhez a [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Egy elem importálásához kattintson a részletek megtekintéséhez, majd kattintson rá a **importálása** gombra.

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

7. Szükség esetén módosítsa a runbook nevét, és kattintson a **OK** a runbook importálása.
8. A runbook megjelenik-e a **Runbookok** az Automation-fiókhoz tartozó lapon.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook felvétele a katalógusban

A Microsoft javasolja, hogy a runbookok hozzáadása a PowerShell-galériából, amely úgy gondolja, hogy más felhasználók számára hasznos lehet. A PowerShell-galériából a PowerShell-modulok és a PowerShell-parancsfájlok fogad el. Hozzáadhat egy runbook által [feltölteni a PowerShell-galériából a](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafikus runbookok nem támogatottak a PowerShell-galériában.

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
9. Befejezése után egy, hogy a modul parancsfájlműveletekkel értesítése és a egy másik értesítést kap.
10. A modul importálása után megjelenik az elérhető tevékenységeket. A runbookok és a Desired State Configuration erőforrásaihoz is használhatja.

> [!NOTE]
> A PowerShell core támogató modulok nem támogatottak az Azure Automationben, és nem lehet importálni az Azure Portalon, vagy közvetlenül a PowerShell-galériából helyezik üzembe.

## <a name="python-runbooks"></a>Python-Runbookok

Python-Runbookok érhetők el a [Script Center-galéria](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). A Script Center-galéria Python runbookok közreműködhet kattintva **hozzájárulás feltöltése**. Ha Ön, győződjön meg arról, hogy a címke hozzáadása **Python** főágra feltöltésekor.

> [!NOTE]
> Annak érdekében, hogy a tartalom feltöltése [Script Center](https://gallery.technet.microsoft.com/scriptcenter) legalább 100 pontok megadása kötelező. 

## <a name="requesting-a-runbook-or-module"></a>Egy runbook vagy a modul kérése

Kérelmek küldése [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Runbookok írása segítségre van szüksége, vagy PowerShell kérdése van, ha új kérdést tenne fel, hogy [fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>További lépések

* Forgatókönyvekkel való ismerkedéshez tekintse meg [kezelése az Azure Automation forgatókönyv](manage-runbooks.md)
* A forgatókönyvek PowerShell és a PowerShell-munkafolyamat közötti különbségek ismertetése: [Learning PowerShell-munkafolyamat](automation-powershell-workflow.md)