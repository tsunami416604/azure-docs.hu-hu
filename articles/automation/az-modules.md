---
title: Az Az modul támogatása az Azure Automationben
description: Ez a cikk az Az modulok Azure Automation ben használatával kapcsolatos információkat tartalmaz.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548356"
---
# <a name="az-module-support-in-azure-automation"></a>Az Az modul támogatása az Azure Automationben

Az Azure Automation támogatja az [Azure PowerShell Az modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) használatát a runbookokban. Az Az modul nem importálja automatikusan az új vagy meglévő Automation-fiókokba. 

## <a name="considerations"></a>Megfontolandó szempontok

Az Azure Automation ben az Az modul használatakor számos szempontot figyelembe kell venni. A Runbookokat és a modulokat magasabb szintű megoldások használhatják az Automation-fiókban. A runbookok szerkesztése vagy a modulok frissítése problémákat okozhat a runbookokkal. Az új `Az` modulok importálása előtt gondosan tesztelje az összes runbookot és megoldást egy külön Automation-fiókban. A modulok bármilyen módosítása negatívan befolyásolhatja a [Start/Stop](automation-solution-vm-management.md) megoldást. Nem javasoljuk a modulok és runbookok módosítását az Automation-fiókokban, amelyek bármilyen megoldást tartalmaznak. Ez a viselkedés nem jellemző az Az modulokra. Ezt a viselkedést figyelembe kell venni az Automation-fiók bármilyen módosításának bevezetésekor.

Egy `Az` modul importálása az Automation-fiókban nem automatikusan importálja a modult a PowerShell-munkamenetben, amelyet a runbookok használnak. A modulok a következő esetekben lesznek importálva a PowerShell-munkamenetbe:

* Ha egy modul parancsmagját runbookból hívja meg a parancsmag
* Amikor egy runbook kifejezetten `Import-Module` importálja azt a parancsmaggal
* Ha a modultól függően egy másik modult importál egy PowerShell-munkamenetbe

> [!IMPORTANT]
> Fontos, hogy győződjön meg arról, hogy `Az` a `AzureRM` runbookok egy Automation-fiókban vagy csak importálni vagy modulokat a PowerShell-munkamenetek által használt runbookok, és nem mindkettő. Ha `Az` a `AzureRM` runbookban korábban importálja, a runbook befejeződik, de a [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) parancsmagra hivatkozó hiba megjelenik a feladatfolyamokban, és előfordulhat, hogy a parancsmagok végrehajtása nem megfelelően történik. Ha `AzureRM` importálja, `Az`majd a runbook továbbra is befejeződik, de hibaüzenet jelenik meg a feladatstreamekben, amely szerint mindkettő, `Az` és `AzureRM` nem importálható ugyanabban a munkamenetben, vagy ugyanabban a runbookban használható.

## <a name="migrating-to-az-modules"></a>Áttelepítés Az modulokra

Javasoljuk, hogy tesztelje az az modulokra való áttelepítést egy tesztautomatizálási fiókban. Az Automation-fiók létrehozása után az ebben a szakaszban található utasításokat használhatja a modulok használatához.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Az AzureRM-parancsmagokat használó összes runbook leállítása és ütemezése nem ütemezése

Annak érdekében, hogy ne futtasson `AzureRM` olyan meglévő runbookokat, amelyek parancsmagokat `AzureRM` használnak, állítsa le és ne ütemezze le a modulokat használó összes runbookot. A példához hasonló kód futtatásával megtekintheti, hogy milyen ütemezések léteznek, és mely ütemezéseket kell eltávolítani.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Fontos, hogy az egyes ütemezéseket külön-külön tekintse át, hogy szükség esetén a jövőben átütemezhesse a runbookok hoz.

### <a name="import-the-az-modules"></a>Az Az modulok importálása

Csak importálja az Az modulokat, amelyek szükségesek a runbookok. Ne importálja az `Az` összesítő modult, `Az.*` mivel az összes modult tartalmazza. Ez az útmutató minden modul esetében azonos.

Az [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modul a többi `Az.*` modul függősége. Ezért ezt a modult importálni kell az Automation-fiókba, mielőtt más modulokat importálna.

Az Automation-fiókban válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** Kattintson a **Galéria tallózása** gombra a **Galéria tallózása** lap megnyitásához.  A keresősávban adja meg a `Az.Accounts`modul nevét (például ). A PowerShell-modul lapon kattintson az **Importálás gombra** a modul automatizálási fiókba importálásához.

![Modulok importálása az Automation-fiókból](media/az-modules/import-module.png)

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com) keresztül is elvégezhető, ha megkeresi az importálási modult. Miután megtalálta a modult, jelölje ki, és az **Azure Automation** lapon kattintson az Üzembe helyezés az Azure **Automation behelyezése gombra.**

![Modulok importálása közvetlenül a galériából](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>A runbookok tesztelése

Miután `Az` a modulok importálása az Automation-fiókba, elkezdheti a runbookok szerkesztését az Az modulok használatához. A parancsmagok többsége azonos névvel rendelkezik, kivéve, hogy `AzureRM` a névre `Az`változott. Az elnevezési konvenciót nem követő modulok listáját [a kivételek listája](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)tartalmazza.

A runbook új parancsmagok használatára történő módosításának tesztelésének egyik módja a runbook elején történő használat. `Enable-AzureRMAlias -Scope Process` Ha hozzáadja ezt a parancsot a runbookhoz, a parancsfájl módosítások nélkül futtatható.

## <a name="after-migration-details"></a>Áttelepítés utáni részletek

Az áttelepítés befejezése után ne próbálja meg elindítani a runbookok az Automation-fiók moduljainak használatát. `AzureRM` Azt is javasoljuk, hogy ne `AzureRM` importáljon vagy frissítsen modulokat a fiókban. Vegye figyelembe a `Az`számlára áttelepített , és csak modulokkal `Az` működik. 

Új Automation-fiók létrehozásakor a `AzureRM` meglévő modulok továbbra is telepítve vannak. Továbbra is frissítheti az `AzureRM` oktatóanyag runbookok parancsmagokkal. Ne futtassa ezeket a runbookokat.

## <a name="next-steps"></a>További lépések

Az Az modulok használatáról az [Az modul használatának első lépései](/powershell/azure/get-started-azureps?view=azps-1.1.0).
