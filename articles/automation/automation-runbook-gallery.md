---
title: Runbook és modul-galériák Azure Automation
description: A Microsoft és a Közösség runbookok és moduljai a Azure Automation-környezetben telepíthetők és használhatók.  Ez a cikk bemutatja, hogyan érheti el ezeket az erőforrásokat, és Hogyan járulhat hozzá a runbookok a katalógushoz.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94910d0f42ad6b208cac54dd2826cbd2d917504b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850720"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook és modul-galériák Azure Automation

Ahelyett, hogy az Azure Automation saját runbookok és moduljait hozza létre, a Microsoft és a Közösség által már létrehozott forgatókönyvekhez is hozzáférhet.

A PowerShell-runbookok és- [modulokat](#modules-in-powershell-gallery) a PowerShell-Galéria és a [Python runbookok](#python-runbooks) szerezheti be a Script Center-galériából. Az Ön által fejlesztett forgatókönyvek megosztásával is hozzájárulhat a közösséghez: runbook hozzáadása a gyűjteményhez

## <a name="runbooks-in-powershell-gallery"></a>Runbookok PowerShell-galéria

A [PowerShell-Galéria](https://www.powershellgallery.com/packages) különböző runbookok kínál a Microsofttól és a Közösségtől, amelyet importálhat a Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

A Azure Portal használatával csak közvetlenül a PowerShell-galéria importálható. Ez a függvény nem hajtható végre a PowerShell használatával.

> [!NOTE]
> Ellenőriznie kell a PowerShell-galéria kapott runbookok tartalmát, és rendkívül körültekintően kell eljárnia, ha éles környezetben telepíti és futtatja őket.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálása a Runbook-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **folyamat automatizálása**területen kattintson a **runbookok-gyűjtemény** elemre.
3. **Forrás kiválasztása: PowerShell-Galéria**.
4. Keresse meg a kívánt katalógus-elemet, és válassza ki azt a részletek megtekintéséhez. A bal oldalon további keresési paramétereket adhat meg a közzétevőhöz és a típushoz.

   ![Tallózási katalógus](media/automation-runbook-gallery/browse-gallery.png)

5. Kattintson a **Source (forrás) projekt megtekintése** lehetőségre az elem a [TechNet script Centerben](https://gallery.technet.microsoft.com/)való megtekintéséhez.
6. Egy elem importálásához kattintson rá a részletek megtekintéséhez, majd kattintson az **Importálás** gombra.

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

7. Igény szerint módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
8. A runbook megjelenik az Automation-fiók **runbookok** lapján.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook hozzáadása a gyűjteményhez

A Microsoft azt javasolja, hogy runbookok adjon hozzá a PowerShell-galériahoz, amelyet úgy gondol, hogy más ügyfelek számára hasznos lenne. A PowerShell-galéria PowerShell-modulokat és PowerShell-parancsfájlokat fogad el. Runbook hozzáadásához [töltse fel azt a PowerShell-galériaba](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> PowerShell-galéria nem támogatja a grafikus runbookok.

## <a name="modules-in-powershell-gallery"></a>A PowerShell-galéria moduljai

A PowerShell-modulok olyan parancsmagokat tartalmaznak, amelyeket használhat a runbookok, és a Azure Automation telepíthető meglévő modulok a [PowerShell-Galéria](https://www.powershellgallery.com)érhetők el. Ezt a katalógust a Azure Portal is elindíthatja, és közvetlenül a Azure Automation telepítheti őket. Letöltheti őket, és manuálisan is telepítheti őket.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Modul importálása az Automation modul-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A modulok listájának megnyitásához válassza ki a **megosztott erőforrások** területen lévő **modulok** elemet.
3. Kattintson a lap tetején található **Tallózás** katalógus lehetőségre.

   ![Modul gyűjteménye](media/automation-runbook-gallery/modules-blade.png)

4. A **Tallózás** a katalógusban lapon a következő mezőkben kereshet:

   * Modulnév
   * Címkék
   * Szerző
   * Parancsmag/DSC-erőforrás neve

5. Keresse meg a kívánt modult, és válassza ki, hogy megtekintse a részleteit.

   Egy adott modul részletezésével további információkat jeleníthet meg. Ez az információ tartalmaz egy hivatkozást a PowerShell-galériara, a szükséges függőségekre, valamint az összes olyan parancsmagra vagy DSC-erőforrásra, amelyet a modul tartalmaz.

   ![PowerShell-modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Ha közvetlenül a Azure Automation szeretné telepíteni a modult, kattintson az **Importálás** gombra.
7. Amikor az Importálás gombra kattint, az **Importálás** panelen megjelenik az importálni kívánt modul neve. Ha az összes függőség telepítve van, az **OK** gomb aktiválva lesz. Ha a függőségek hiányoznak, ezeket a függőségeket importálnia kell, mielőtt importálni tudja a modult.
8. Az **Importálás** lapon kattintson az **OK** gombra a modul importálásához. Míg Azure Automation importál egy modult a fiókjába, kibontja a modul és a parancsmagok metaadatait. Ez a művelet eltarthat néhány percig, amíg az egyes tevékenységeket ki kell nyerni.
9. A rendszer első értesítést kap arról, hogy a modul üzembe helyezése folyamatban van, valamint egy másik értesítés, ha befejeződött.
10. A modul importálása után láthatja a rendelkezésre álló tevékenységeket. Használhatja erőforrásait a runbookok és a kívánt állapot konfigurációjában.

> [!NOTE]
> A csak a PowerShell Core-t támogató modulok nem támogatottak Azure Automationban, és nem importálhatók a Azure Portal, vagy közvetlenül a PowerShell-galéria központilag telepíthetők.

## <a name="python-runbooks"></a>Python-Runbookok

A Python-Runbookok a [Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=)-katalógusban érhetők el. A Python runbookok a szkriptek **feltöltése**lehetőségre kattintva járulhat hozzá a Script Center Galleryhez. Ha ezt teszi, ügyeljen arra, hogy hozzáadja a **Python** címkét a hozzájárulás feltöltésekor.

> [!NOTE]
> Ahhoz, hogy tartalmat töltsön fel a [script Centerbe](https://gallery.technet.microsoft.com/scriptcenter) , legalább 100 pontot kell megadnia.

## <a name="requesting-a-runbook-or-module"></a>Runbook vagy modul igénylése

A [felhasználói hangra](https://feedback.azure.com/forums/246290-azure-automation/)vonatkozó kéréseket küldhet.  Ha segítségre van szüksége egy runbook írásához vagy a PowerShell-lel kapcsolatos kérdésben, tegye fel kérdéseit a [fórumba](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>A runbook-katalógusban elérhető gyakori megoldások

Az alábbi lista néhány olyan runbookok tartalmaz, amely a gyakori forgatókönyvekhez nyújt megoldásokat. Az Azure Automation csapat által létrehozott runbookok teljes listáját lásd: [AzureAutomationTeam-profil](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – az Automation-fiók összes moduljának PowerShell-Galéria legújabb verzióját importálja.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – ez a parancsfájl Azure Diagnostics és log Analyticst konfigurál a feladatok állapotát és a feladatokat tartalmazó naplók Azure Automation fogadásához.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – ez a runbook egy távoli fájlt másol egy Windows Azure-beli virtuális gépről.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – a runbook egy helyi fájlt másol egy Azure-beli virtuális gépre.

## <a name="next-steps"></a>Következő lépések

* A runbookok megkezdéséhez lásd: [Runbook kezelése a Azure Automationban](manage-runbooks.md)
* A PowerShell és a PowerShell-munkafolyamatok közötti különbségek megismeréséhez a runbookok használatával lásd: a [PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md)
* A PowerShell-lel kapcsolatos további információkért, beleértve a nyelvi referenciákat és a tanulási modulokat, tekintse át a [PowerShell-dokumentumokat](https://docs.microsoft.com/powershell/scripting/overview).
