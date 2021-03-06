---
title: A PowerShell-galéria Azure Automation runbookok és moduljainak használata
description: Ez a cikk azt ismerteti, hogyan használhatók a Microsoft és a Közösség runbookok és moduljai a PowerShell-galéria.
services: automation
ms.subservice: process-automation
ms.date: 12/17/2020
ms.topic: conceptual
ms.openlocfilehash: 26dfedf1961cf39645c3bcf3ce5fa2db6c14119e
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682763"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Runbookok és modulok használata a PowerShell-galériában

Ahelyett, hogy az Azure Automation saját runbookok és moduljait hozza létre, a Microsoft és a Közösség által már létrehozott forgatókönyvekhez is hozzáférhet. PowerShell-runbookok és- [modulokat](#modules-in-powershell-gallery) a PowerShell-Galéria és a [Python Runbookok](#use-python-runbooks) a Azure Automation GitHub-szervezetből szerezhet be. A közösséghez a [fejlesztés alatt álló forgatókönyvek](#add-a-powershell-runbook-to-the-gallery)megosztásával is hozzájárulhat.

> [!NOTE]
> A TechNet Script Center kivonásra kerül. A Runbook-katalógusban található runbookok származó összes művelet át lett helyezve az [Automation GitHub-szervezetbe](https://github.com/azureautomation).

## <a name="runbooks-in-powershell-gallery"></a>Runbookok PowerShell-galéria

A [PowerShell-Galéria](https://www.powershellgallery.com/packages) különböző runbookok kínál a Microsofttól és a Közösségtől, amelyet importálhat a Azure Automationba. Ha szeretné használni, töltsön le egy runbook a katalógusból, vagy közvetlenül importálhatja a runbookok a katalógusból vagy az Automation-fiókjából a Azure Portal.

> [!NOTE]
> PowerShell-galéria nem támogatja a grafikus runbookok.

A Azure Portal használatával csak közvetlenül a PowerShell-galéria importálható. Ez a függvény nem hajtható végre a PowerShell használatával.

> [!NOTE]
> Ellenőriznie kell a PowerShell-galéria kapott runbookok tartalmát, és rendkívül körültekintően kell eljárnia, ha éles környezetben telepíti és futtatja őket.

## <a name="modules-in-powershell-gallery"></a>A PowerShell-galéria moduljai

A PowerShell-modulok olyan parancsmagokat tartalmaznak, amelyeket használhat a runbookok, és a Azure Automation telepíthető meglévő modulok a [PowerShell-Galéria](https://www.powershellgallery.com)érhetők el. Ezt a katalógust a Azure Portal is elindíthatja, és közvetlenül a Azure Automation telepítheti őket. Letöltheti őket, és manuálisan is telepítheti őket.

## <a name="common-scenarios-available-in-powershell-gallery"></a>A PowerShell-galériaben elérhető gyakori forgatókönyvek

Az alábbi lista néhány olyan runbookok tartalmaz, amelyek támogatják a gyakori forgatókönyveket. Az Azure Automation csapat által létrehozott runbookok teljes listáját lásd: [AzureAutomationTeam-profil](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – egy Automation-fiókban lévő összes modul legújabb verziójának importálása PowerShell-galériaból.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – Azure Diagnostics és log Analytics konfigurálása a feladatok állapotát és a feladatok folyamait tartalmazó Azure Automation naplók fogadásához.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – távoli fájl másolása egy Windows Azure-beli virtuális gépről.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – helyi fájl másolása egy Azure-beli virtuális gépre.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>PowerShell-runbook importálása a runbook-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza a **runbookok** katalógus lehetőséget a **folyamat automatizálása** alatt.
3. **Forrás kiválasztása: PowerShell-Galéria**.
4. Keresse meg a kívánt katalógus-elemet, és válassza ki azt a részletek megtekintéséhez. A bal oldalon további keresési paramétereket adhat meg a közzétevőhöz és a típushoz.

   ![Tallózási katalógus](media/automation-runbook-gallery/browse-gallery.png)

5. A [Azure Automation GitHub-szervezet](https://github.com/azureautomation)elemének megtekintéséhez kattintson a **Source Project megtekintése** lehetőségre.
6. Egy elem importálásához kattintson rá a részletek megtekintéséhez, majd kattintson az **Importálás** elemre.

   ![Importálás gomb](media/automation-runbook-gallery/gallery-item-detail.png)

7. Igény szerint módosítsa a runbook nevét, majd kattintson az **OK** gombra a runbook importálásához.
8. A runbook megjelenik az Automation-fiók **runbookok** lapján.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>PowerShell-runbook hozzáadása a gyűjteményhez

A Microsoft azt javasolja, hogy runbookok adjon hozzá a PowerShell-galériahoz, amelyet úgy gondol, hogy más ügyfelek számára hasznos lenne. A PowerShell-galéria PowerShell-modulokat és PowerShell-parancsfájlokat fogad el. Runbook hozzáadásához [töltse fel azt a PowerShell-galériaba](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Modul importálása a modul-galériából a Azure Portal

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A modulok listájának megnyitásához válassza ki a **megosztott erőforrások** területen lévő **modulok** elemet.
3. Kattintson a lap tetején található **Tallózás** katalógus lehetőségre.

   ![Modul gyűjteménye](media/automation-runbook-gallery/modules-blade.png)

4. A Tallózás a katalógusban lapon a következő mezőkben kereshet:

   * Modulnév
   * Címkék
   * Szerző
   * Parancsmag/DSC-erőforrás neve

5. Keresse meg a kívánt modult, és válassza ki, hogy megtekintse a részleteit.

   Egy adott modul részletezésével további információkat jeleníthet meg. Ez az információ tartalmaz egy hivatkozást a PowerShell-galériara, a szükséges függőségekre, valamint az összes olyan parancsmagra vagy DSC-erőforrásra, amelyet a modul tartalmaz.

   ![PowerShell-modul részletei](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Ha közvetlenül a Azure Automation szeretné telepíteni a modult, kattintson az **Importálás** gombra.
7. Az importálás ablaktáblán láthatja az importálandó modul nevét. Ha az összes függőség telepítve van, az **OK** gomb aktiválva lesz. Ha a függőségek hiányoznak, ezeket a függőségeket importálnia kell, mielőtt importálni tudja a modult.
8. Az importálás ablaktáblán kattintson az **OK** gombra a modul importálásához. Míg Azure Automation importál egy modult a fiókjába, kibontja a modul és a parancsmagok metaadatait. Ez a művelet eltarthat néhány percig, amíg az egyes tevékenységeket ki kell nyerni.
9. A rendszer első értesítést kap arról, hogy a modul üzembe helyezése folyamatban van, valamint egy másik értesítés, ha befejeződött.
10. A modul importálása után láthatja a rendelkezésre álló tevékenységeket. A runbookok és a DSC-erőforrásokhoz is használhat modul-erőforrásokat.

> [!NOTE]
> A csak a PowerShell Core-t támogató modulok nem támogatottak Azure Automationban, és nem importálhatók a Azure Portal, vagy közvetlenül a PowerShell-galéria központilag telepíthetők.

## <a name="use-python-runbooks"></a>Python-runbookok használata

A Python-Runbookok a [Azure Automation GitHub-szervezetben](https://github.com/azureautomation)érhetők el. Ha hozzájárul a GitHub-tárházhoz, adja hozzá a címkét **(GitHub-témakör): Python3** , amikor feltölti a hozzájárulását.

## <a name="request-a-runbook-or-module"></a>Runbook vagy modul igénylése

A [felhasználói hangra](https://feedback.azure.com/forums/246290-azure-automation/)vonatkozó kéréseket küldhet.  Ha segítségre van szüksége a runbook írásához vagy a PowerShell-lel kapcsolatos kérdésben, tegye fel kérdéseit a [Microsoft Q&egy kérdés oldalára](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>További lépések

* A PowerShell-runbook megkezdéséhez tekintse meg az [oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md)című témakört.
* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
