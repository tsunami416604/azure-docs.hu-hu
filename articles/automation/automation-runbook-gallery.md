---
title: Runbook- és modulkatalógusok az Azure Automationhöz
description: Runbookok és modulok a Microsoft és a közösség állnak rendelkezésre, hogy telepítse és használja az Azure Automation-környezetben.  Ez a cikk ismerteti, hogyan érheti el ezeket az erőforrásokat, és hozzájárul a runbookok a katalógusba.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421490"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- és modulkatalógusok az Azure Automationhöz

Ahelyett, hogy saját runbookokat és modulokat hozna létre az Azure Automationben, hozzáférhet a Microsoft és a közösség által már létrehozott forgatókönyvekhez.

PowerShell-runbookok és modulok a PowerShell-galériából és a [Python runbookok](#python-runbooks) a Script Center-galériából. [modules](#modules-in-powershell-gallery) A közösséghez a kifejlesztett forgatókönyvek megosztásával is hozzájárulhat, lásd: Runbook hozzáadása a gyűjteményhez

## <a name="runbooks-in-powershell-gallery"></a>Runbookok a PowerShell-galériában

A [PowerShell-galériában](https://www.powershellgallery.com/packages) a Microsoft és a közösség számos runbookot biztosít, amelyeket importálhat az Azure Automationbe. A használathoz töltsön le egy runbookot a katalógusból, vagy közvetlenül importálhatja a runbookokat a katalógusból vagy az Automation-fiókból az Azure Portalon.

Csak az Azure Portalon keresztül importálhat közvetlenül a PowerShell-galériából. Ezt a funkciót nem hajthatja végre a PowerShell használatával.

> [!NOTE]
> Ellenőrizze a PowerShell-galériából kapott runbookok tartalmát, és rendkívül óvatosan telepítse és futtassa őket éles környezetben.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálása a Runbook-galériából az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **Folyamatautomatizálás**csoportban kattintson a **Runbooks gyűjteményre**
3. Válassza a **Forrás: PowerShell-galéria lehetőséget.**
4. Keresse meg a kívánt gyűjteményelemet, és jelölje ki a kívánt gyűjteményelemet a részletek megtekintéséhez. A bal oldalon további keresési paramétereket adhat meg a közzétevőhez és a típushoz.

   ![Tallózási katalógus](media/automation-runbook-gallery/browse-gallery.png)

5. Kattintson a **Forrásprojekt megtekintése** elemre az elem megtekintéséhez a [TechNet Parancsfájlközpontban](https://gallery.technet.microsoft.com/).
6. Elem importálásához kattintson rá a részletek megtekintéséhez, majd kattintson az **Importálás** gombra.

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

7. Szükség esetén módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
8. A runbook az Automation-fiók **Runbookok** lapján jelenik meg.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook hozzáadása a katalógushoz

A Microsoft azt javasoljuk, hogy adjon hozzá runbookokat a PowerShell-galériához, amely ről úgy gondolja, hogy hasznos lehet más ügyfelek számára. A PowerShell-galériában fogadja a PowerShell-modulokat és a PowerShell-parancsfájlokat. Runbookot úgy adhat [hozzá, hogy feltölti a PowerShell-galériába.](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)

> [!NOTE]
> A grafikus runbookok nem támogatottak a PowerShell-galériában.

## <a name="modules-in-powershell-gallery"></a>Modulok a PowerShell-galériában

A PowerShell-modulok a runbookokban használható parancsmagokat tartalmaznak, és az Azure Automationben telepíthető meglévő modulok a [PowerShell-galériában](https://www.powershellgallery.com)érhetők el. Ezt a galériát az Azure Portalon elindíthatja, és közvetlenül az Azure Automationbe telepítheti. Letöltheti és manuálisan is telepítheti őket.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Modul importálása az Automation Modultárból az Azure Portalon

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A modulok listájának megnyitásához válassza a **Modulok lehetőséget** a **Megosztott erőforrások** csoportban.
3. Kattintson a lap tetején található **Galéria tallózása** elemre.

   ![Modulgaléria](media/automation-runbook-gallery/modules-blade.png)

4. A **Tallózás gyűjteménylapon** a következő mezők szerint kereshet:

   * Modulnév
   * Címkék
   * Szerző
   * Parancsmag/DSC-erőforrás neve

5. Keresse meg az Önt érdeklő modult, és válassza ki a részletek megtekintéséhez.

   Ha egy adott modulba részletez, további információkat tekinthet meg. Ez az információ tartalmaz egy hivatkozást vissza a PowerShell-galériában, a szükséges függőségek és az összes parancsmag vagy DSC-erőforrások, amelyek a modul tartalmazza.

   ![A PowerShell-modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Ha a modult közvetlenül az Azure Automationbe szeretné telepíteni, kattintson az **Importálás** gombra.
7. Amikor az Importálás gombra kattint, az **Importálás** ablaktáblán megjelenik az importálni kívánt modulnév. Ha az összes függőség telepítve van, az **OK** gomb aktiválódik. Ha hiányoznak a függőségek, a modul importálása előtt importálnia kell ezeket a függőségeket.
8. Az **Importálás** lapon kattintson az **OK** gombra a modul importálásához. Míg az Azure Automation importál egy modult a fiókjába, kibontja a modul és a parancsmagok metaadatait. Ez a művelet eltarthat egy pár percig, mivel minden tevékenységet ki kell vonni.
9. Első értesítést kap a modul üzembe helyezéséről, és egy másik értesítést, ha befejeződött.
10. A modul importálása után láthatja a rendelkezésre álló tevékenységeket. Használhatja az erőforrásokat a runbookok és a kívánt állapot konfigurációja.

> [!NOTE]
> A csak PowerShell-maggal támogatott modulok nem támogatottak az Azure Automationben, és nem importálhatók az Azure Portalon, vagy közvetlenül a PowerShell-galériából telepíthetők.

## <a name="python-runbooks"></a>Python Runbookok

A Python Runbookok a [Parancsfájlközpont-gyűjteményben](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)érhetők el. A Python runbookok a Script Center gyűjteményhez a **Hozzájárulás feltöltése**gombra kattintva járulhatnak hozzá a Python runbookokhoz. Ha ezt teszed, győződjön **Python** meg arról, hogy adja hozzá a python-címkét a hozzájárulás feltöltésekénél.

> [!NOTE]
> Ahhoz, hogy tartalmat tölthessenek fel a [Script Centerbe,](https://gallery.technet.microsoft.com/scriptcenter) legalább 100 pont szükséges.

## <a name="requesting-a-runbook-or-module"></a>Runbook vagy modul kérése

Kéréseket küldhet a [User Voice -nak.](https://feedback.azure.com/forums/246290-azure-automation/)  Ha segítségre van szüksége egy runbook írásával kapcsolatban, vagy kérdése van a PowerShell-lel kapcsolatban, tegyen fel egy kérdést [a fórumunkban.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="common-solutions-available-in-the-runbook-gallery"></a>A runbook-gyűjteményben elérhető gyakori megoldások

Az alábbi lista néhány runbookot tartalmaz, amelyek megoldásokat kínálnak a gyakori forgatókönyvekre. Az Azure Automation csapata által létrehozott runbookok teljes listáját az [AzureAutomationTeam profil ban láthatja.](https://www.powershellgallery.com/profiles/AzureAutomationTeam)

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importálja a legújabb verziót a PowerShell-galériában az összes modul egy Automation-fiókban.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – Ez a parancsfájl konfigurálja az Azure Diagnostics and Log Analytics-et a feladatállapotot és feladatadatfolyamokat tartalmazó Azure Automation-naplók fogadására.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – Ez a runbook egy távoli fájlt másol egy Windows Azure virtuális gépről.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – Ez a runbook egy helyi fájlt másol egy Azure virtuális gépre.

## <a name="next-steps"></a>További lépések

* A runbookok kezeléséről az [Azure Automationben című témakörben ismerkedhet meg a Runbook kezelésével című témakörben.](manage-runbooks.md)
* A PowerShell és a Runbookokkal rendelkező PowerShell-munkafolyamat közötti különbségeket a [PowerShell-munkafolyamat tanulásával](automation-powershell-workflow.md) kapcsolatos munkafolyamatban
* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, olvassa el a [PowerShell-dokumentumok című dokumentumban.](https://docs.microsoft.com/powershell/scripting/overview)
