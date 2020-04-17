---
title: Runbook- és modulkatalógusok az Azure Automationhöz
description: Runbookok és modulok a Microsoft és a közösség állnak rendelkezésre, hogy telepítse és használja az Azure Automation-környezetben.  Ez a cikk bemutatja, hogyan érheti el ezeket az erőforrásokat, és hogyan járulhat hozzá a runbookok a katalógusba.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535553"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- és modulkatalógusok az Azure Automationhöz

Ahelyett, hogy saját runbookokat és modulokat hozna létre az Azure Automationben, hozzáférhet a Microsoft és a közösség által már létrehozott forgatókönyvekhez. PowerShell-runbookok és modulok a PowerShell-galériából és a [Python runbookok](#use-python-runbooks) a Script Center-galériából. [modules](#modules-in-powershell-gallery) A közösséghez a [kifejlesztett forgatókönyvek](#add-a-powershell-runbook-to-the-gallery)megosztásával is hozzájárulhat a közösséghez. 

## <a name="runbooks-in-powershell-gallery"></a>Runbookok a PowerShell-galériában

A [PowerShell-galériában](https://www.powershellgallery.com/packages) a Microsoft és a közösség számos runbookot biztosít, amelyeket importálhat az Azure Automationbe. A használathoz töltsön le egy runbookot a katalógusból, vagy közvetlenül importálhatja a runbookokat a katalógusból vagy az Automation-fiókból az Azure Portalon.

> [!NOTE]
> A grafikus runbookok nem támogatottak a PowerShell-galériában.

Csak az Azure Portalon keresztül importálhat közvetlenül a PowerShell-galériából. Ezt a funkciót nem hajthatja végre a PowerShell használatával.

> [!NOTE]
> Ellenőrizze a PowerShell-galériából kapott runbookok tartalmát, és rendkívül óvatosan telepítse és futtassa őket éles környezetben.

## <a name="modules-in-powershell-gallery"></a>Modulok a PowerShell-galériában

A PowerShell-modulok a runbookokban használható parancsmagokat tartalmaznak, és az Azure Automationben telepíthető meglévő modulok a [PowerShell-galériában](https://www.powershellgallery.com)érhetők el. Ezt a galériát az Azure Portalon elindíthatja, és közvetlenül az Azure Automationbe telepítheti. Letöltheti és manuálisan is telepítheti őket.

## <a name="common-solutions-available-in-powershell-gallery"></a>A PowerShell-galériában elérhető gyakori megoldások

Az alábbi lista néhány runbookot tartalmaz, amelyek megoldásokat kínálnak a gyakori forgatókönyvekre. Az Azure Automation csapata által létrehozott runbookok teljes listáját az [AzureAutomationTeam profil ban láthatja.](https://www.powershellgallery.com/profiles/AzureAutomationTeam)

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importálja az összes modul legújabb verzióját egy Automation-fiókban a PowerShell-galériából.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – Az Azure Diagnostics and Log Analytics konfigurálása a feladatállapotot és feladatadatfolyamokat tartalmazó Azure Automation-naplók fogadására.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – Távoli fájlt másol egy Windows Azure virtuális gépről.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – egy helyi fájlt másol egy Azure virtuális gépre.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálása a runbook-galériából az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza a **Runbook-gyűjtemény** lehetőséget a **Folyamatautomatizálás csoportban.**
3. Válassza a **Forrás: PowerShell-galéria lehetőséget.**
4. Keresse meg a kívánt gyűjteményelemet, és jelölje ki a kívánt gyűjteményelemet a részletek megtekintéséhez. A bal oldalon további keresési paramétereket adhat meg a közzétevőhez és a típushoz.

   ![Tallózási katalógus](media/automation-runbook-gallery/browse-gallery.png)

5. Kattintson a **Forrásprojekt megtekintése** elemre az elem megtekintéséhez a [TechNet Parancsfájlközpontban](https://gallery.technet.microsoft.com/).
6. Elem importálásához kattintson rá a részletek megtekintéséhez, majd kattintson az **Importálás gombra.**

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

7. Szükség esetén módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
8. A runbook az Automation-fiók **Runbookok** lapján jelenik meg.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook hozzáadása a gyűjteményhez

A Microsoft azt javasoljuk, hogy adjon hozzá runbookokat a PowerShell-galériához, amely ről úgy gondolja, hogy hasznos lehet más ügyfelek számára. A PowerShell-galériában fogadja a PowerShell-modulokat és a PowerShell-parancsfájlokat. Runbookot úgy adhat [hozzá, hogy feltölti a PowerShell-galériába.](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Modul importálása a modulgalériából az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A modulok listájának megnyitásához válassza a **Modulok lehetőséget** a **Megosztott erőforrások** csoportban.
3. Kattintson a lap tetején található **Galéria tallózása** elemre.

   ![Modulgaléria](media/automation-runbook-gallery/modules-blade.png)

4. A Tallózás gyűjteménylapon a következő mezők szerint kereshet:

   * Modulnév
   * Címkék
   * Szerző
   * Parancsmag/DSC-erőforrás neve

5. Keresse meg az Önt érdeklő modult, és válassza ki a részletek megtekintéséhez.

   Ha egy adott modulba részletez, további információkat tekinthet meg. Ez az információ tartalmaz egy hivatkozást vissza a PowerShell-galériában, a szükséges függőségek és az összes parancsmag vagy DSC-erőforrások, amelyek a modul tartalmazza.

   ![A PowerShell-modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Ha a modult közvetlenül az Azure Automationprogramba szeretné telepíteni, kattintson az **Importálás gombra.**
7. Az Importálás ablaktáblán láthatja az importálandó modul nevét. Ha az összes függőség telepítve van, az **OK** gomb aktiválódik. Ha hiányoznak a függőségek, a modul importálása előtt importálnia kell ezeket a függőségeket.
8. Az Importálás ablaktáblán kattintson az **OK** gombra a modul importálásához. Míg az Azure Automation importál egy modult a fiókjába, kibontja a modul és a parancsmagok metaadatait. Ez a művelet eltarthat egy pár percet, mivel minden tevékenység et ki kell bontani.
9. Első értesítést kap a modul üzembe helyezéséről, és egy másik értesítést, ha befejeződött.
10. A modul importálása után láthatja a rendelkezésre álló tevékenységeket. Modul-erőforrásokat használhat a runbookokban és a DSC-erőforrásokban.

> [!NOTE]
> A csak PowerShell-maggal támogatott modulok nem támogatottak az Azure Automationben, és nem importálhatók az Azure Portalon, vagy közvetlenül a PowerShell-galériából telepíthetők.

## <a name="use-python-runbooks"></a>Python runbookok használata

A Python Runbookok a [Parancsfájlközpont-gyűjteményben](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)érhetők el. A Python runbookok a Script Center gyűjteményhez a **Hozzájárulás feltöltése**gombra kattintva járulhatnak hozzá a Python runbookokhoz. Ha ezt teszed, győződj `Python` meg arról, hogy hozzáadod a címkét a hozzájárulásfeltöltéssorán.

> [!NOTE]
> A tartalom [Script Center](https://gallery.technet.microsoft.com/scriptcenter)bevaló feltöltéséhez legalább 100 pontra van szükség.

## <a name="request-a-runbook-or-module"></a>Runbook vagy modul kérése

Kéréseket küldhet a [User Voice -nak.](https://feedback.azure.com/forums/246290-azure-automation/)  Ha segítségre van szüksége egy runbook írásával kapcsolatban, vagy kérdése van a PowerShell-lel kapcsolatban, tegyen fel egy kérdést [a fórumunkban.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="next-steps"></a>További lépések

* A runbookok első [lépései: Runbook kezelése az Azure Automationben című témakörben.](manage-runbooks.md)
* A PowerShell és a Runbookokkal rendelkező PowerShell-munkafolyamat közötti különbségeket a [PowerShell-munkafolyamat tanulásával](automation-powershell-workflow.md)kapcsolatos munkafolyamat című témakörben ismeri el.
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
