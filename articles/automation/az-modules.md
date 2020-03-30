---
title: Az modulok használata az Azure Automationben
description: Ez a cikk az Az modulok használatával nyújt tájékoztatást az Azure Automationben
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986104"
---
# <a name="az-module-support-in-azure-automation"></a>Az Az modul támogatása az Azure Automationben

Az Azure automation támogatja az [Azure Powershell Az modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) használatát a runbookokban. Az Az modul nem importálja automatikusan az új vagy meglévő Automation-fiókok. Ez a cikk ismerteti, hogyan használhatja az Az modulokat az Azure Automation használatával.

## <a name="considerations"></a>Megfontolandó szempontok

Az Azure Automation ben az Az modul használatakor számos szempontot figyelembe kell venni. A Runbookokat és a modulokat az Automation-fiók magasabb szintű megoldásai is használhatják. A runbookok szerkesztése vagy a modulok frissítése problémákat okozhat a runbookokkal. Az új `Az` modulok importálása előtt gondosan tesztelje az összes runbookot és megoldást egy külön Automation-fiókban. A modulok bármilyen módosítása negatívan befolyásolhatja a [Start/Stop](automation-solution-vm-management.md) megoldást. Nem javasoljuk a modulok és runbookok módosítását az Automation-fiókokban, amelyek bármilyen megoldást tartalmaznak. Ez a viselkedés nem jellemző az Az modulokra. Ezt a viselkedést figyelembe kell venni az Automation-fiók bármilyen módosításának bevezetésekor.

Egy `Az` modul importálása az Automation-fiókban nem automatikusan importálja a modult a PowerShell-munkamenetben, amelyet a runbookok használnak. A modulok a következő esetekben lesznek importálva a PowerShell-munkamenetbe:

* Ha egy modul parancsmagját runbookból hívja meg a parancsmag
* Amikor egy runbook kifejezetten `Import-Module` importálja azt a parancsmaggal
* Ha a modultól függően egy másik modult importál egy PowerShell-munkamenetbe

> [!IMPORTANT]
> Fontos, hogy győződjön meg arról, hogy `Az` a `AzureRM` runbookok egy Automation-fiókban vagy csak importálni vagy modulokat a PowerShell-munkamenetek által használt runbookok, és nem mindkettő. Ha `Az` a `AzureRM` runbookban korábban importálja, a runbook befejeződik, de [a get_SerializationSettings metódusra hivatkozó hiba](troubleshoot/runbooks.md#get-serializationsettings) megjelenik a feladatfolyamokban, és előfordulhat, hogy a parancsmagok végrehajtása nem megfelelően lett végrehajtva. Ha `AzureRM` importálja, `Az` majd a runbook továbbra is befejeződik, de megjelenik `Az` egy `AzureRM` hiba a feladat streamek, amely szerint mindkettő, és nem importálható ugyanabban a munkamenetben, vagy ugyanabban a runbookban használható.

## <a name="migrating-to-az-modules"></a>Áttelepítés Az modulokra

Javasoljuk, hogy tesztelje az akta modulok használatára az Az modulok használatára az AzureRM-modulok helyett egy tesztautomatizálási fiókban. Az Automation-fiók létrehozása után a következő lépésekkel biztosíthatja az áttelepítés zökkenőmentes lebonyolítását:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Az AzureRM-modulokat használó összes runbook leállítása és ütemezésének leállítása

Annak érdekében, hogy ne futtasson `AzureRM` olyan meglévő runbookokat, amelyek parancsmagokat `AzureRM` használnak, állítsa le és ne ütemezze le a modulokat használó összes runbookot. A következő példa futtatásával láthatja, hogy milyen ütemezések léteznek, és mely ütemezéseket kell eltávolítani:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Fontos, hogy az egyes ütemezéseket külön-külön tekintse át, hogy szükség esetén a runbookok hoz a jövőben átütemezheti.

### <a name="import-the-az-modules"></a>Az Az modulok importálása

Csak importálja az Az modulokat, amelyek szükségesek a runbookok. Ne importálja az `Az` összesítő modult, mivel `Az.*` tartalmazza az összes importálandó modult. Ez az útmutató minden modul esetében azonos.

Az [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modul a többi `Az.*` modul függősége. Ezért ezt a modult importálni kell az Automation-fiókba, mielőtt bármilyen más modult importálna.

Az Automation-fiókban válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** Kattintson a **Galéria tallózása** gombra a **Galéria tallózása** lap megnyitásához.  A keresősávban adja meg a `Az.Accounts`modul nevét (például ). A PowerShell-modul lapon kattintson az **Importálás gombra** a modul automatizálási fiókba importálásához.

![Modulok importálása az Automation-fiókból](media/az-modules/import-module.png)

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com) keresztül is elvégezhető a modul keresésével. Miután megtalálta a modult, jelölje ki, és az **Azure Automation** lapon kattintson az Üzembe helyezés az Azure **Automation behelyezése gombra.**

![Modulok importálása közvetlenül a galériából](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>A runbookok tesztelése

Miután `Az` a modulok importálása az Automation-fiókba, most már elkezdheti a runbookok szerkesztését az Az modul használatához. A parancsmagok többsége ugyanaz a neve, `AzureRM` kivéve, `Az`hogy a . Az okat nem követő modulok listáját [a kivételek listájában tetszés szerint.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

Az egyik módja a runbookok tesztelése a runbook oka `Enable-AzureRMAlias -Scope Process` az új parancsmagok használata előtt a runbookok használata. Ha hozzáadja ezt a runbookhoz, a runbook módosítások nélkül futtatható.

## <a name="after-migration-details"></a>Áttelepítés után részletek

Az áttelepítés befejezése után ne indítsa el `AzureRM` a runbookok a fiók moduljainak használatát. Azt is javasoljuk, hogy ne `AzureRM` importáljon vagy frissítsen modulokat ezen a fiókon. Ettől a pillanattól kezdve vegye `Az`figyelembe, `Az` hogy ez a fiók átkerült a programba, és csak modulokkal működik. Amikor egy új Automation-fiók `AzureRM` jön létre, a meglévő modulok továbbra is telepítve `AzureRM` lesznek, és az oktatóanyag runbookok továbbra is parancsmagokkal lesznek létrehozva. Ezeket a runbookokat nem szabad futtatni.

## <a name="next-steps"></a>További lépések

Az Az modulok használatáról az [Az modul használatának első lépései](/powershell/azure/get-started-azureps?view=azps-1.1.0).
