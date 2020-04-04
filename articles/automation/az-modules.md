---
title: Az Az modul támogatása az Azure Automationben
description: Ez a cikk az Az modulok Azure Automation ben használatával kapcsolatos információkat tartalmaz.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637995"
---
# <a name="az-module-support-in-azure-automation"></a>Az Az modul támogatása az Azure Automationben

Az Azure Automation támogatja az [Azure PowerShell Az modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) használatát a runbookokban. Az összesítő Az modul nem importálja automatikusan az új vagy meglévő Automation-fiókokba. 

## <a name="considerations"></a>Megfontolandó szempontok

Az Azure Automation Ben az Az modulok használatakor számos szempontot figyelembe kell venni:

* Az Automation-fiók magasabb szintű megoldásai runbookokat és modulokat használhatnak. Ezért a runbookok szerkesztése vagy a modulok frissítése problémákat okozhat a megoldásokkal. Az új Az modulok importálása előtt gondosan tesztelje az összes runbookot és megoldást egy külön Automation-fiókban. 

* A modulok bármilyen módosítása negatívan befolyásolhatja a [Start/Stop](automation-solution-vm-management.md) megoldást. 

* Az modul importálása az Automation-fiókban nem automatikusan importálja a modult a Runbookok által használt PowerShell-munkamenetben. A modulok a következő esetekben lesznek importálva a PowerShell-munkamenetbe:

    * Amikor egy runbook meghívja a modul parancsmagját
    * Amikor egy runbook kifejezetten importálja a modult a `Import-Module` parancsmaggal
    * Ha egy runbook importál egy másik modult a modultól függően

> [!NOTE]
> Nem javasoljuk a modulok és runbookok módosítását az Automation-fiókokban, amelyek bármilyen megoldást tartalmaznak. Ez a rendelkezés nem az Az modulokra vonatkozik. Ezt figyelembe kell venni az Automation-fiók bármilyen módosításának bevezetésekor.

> [!IMPORTANT]
> Győződjön meg arról, hogy az Automation-fiókban lévő runbookok az Az-modulokat vagy [az AzureRM-modulokat](https://www.powershellgallery.com/packages/AzureRM/6.13.1) importálják, de mindkettőt nem egy PowerShell-munkamenetbe. Ha egy runbook importálja az Az modulokat az AzureRM-modulok előtt, a runbook befejeződik. Azonban egy hiba hivatkozva a [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) parancsmag jelenik meg a feladat streamek és parancsmagok előfordulhat, hogy nem megfelelően hajtják végre. Ha a runbook importálja az AzureRM-modulokat az Az-modulok előtt, a runbook is befejeződik. Ebben az esetben azonban hibaüzenet jelenik meg a feladatstreamekben, amely szerint az Az és az AzureRM nem importálható ugyanabban a munkamenetben, és nem használható ugyanabban a runbookban.

## <a name="migrating-to-az-modules"></a>Áttelepítés Az modulokra

Azt javasoljuk, hogy tesztelje az az modulokra való áttelepítést egy tesztautomation-fiókban. Miután létrehozta a fiókot, az ebben a szakaszban található utasításokat használhatja a modulok használatához.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Az AzureRM-modulokat használó összes runbook leállítása és ütemezésének leállítása

Annak érdekében, hogy ne futtasson azureRM-modulokat használó meglévő runbookokat, állítsa le és ne ütemezze az összes érintett runbookot. A példához hasonló kód futtatásával megtekintheti, hogy milyen ütemezések léteznek, és mely ütemezéseket kell eltávolítani:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Fontos, hogy az egyes ütemezéseket külön-külön tekintse át, hogy szükség esetén a runbookok hoz a jövőben átütemezheti.

### <a name="import-the-az-modules"></a>Az Az modulok importálása

>[!NOTE]
>A runbookok importálása csak a szükséges Az modulokat importálja. Ne importálja az Az összesítő modult, mivel az összes Az modult tartalmazza. Ez az útmutató minden modul esetében azonos.

Az [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modul a többi Az modul függősége. Emiatt a runbookok importálnia kell ezt a modult az Automation-fiókba, mielőtt más modulokat importálna.

A modulok importálása az Azure Portalon:

1. Az Automation-fiókban válassza a **Modulok lehetőséget a** Megosztott erőforrások **csoportban.** 
2. Kattintson a **Galéria tallózása** gombra a Galéria tallózása lap megnyitásához.  
3. A keresősávban adja meg a modul `Az.Accounts`nevét, például . 
4. A PowerShell-modul lapon kattintson az **Importálás gombra** a modul automatizálási fiókba importálásához.

![Modulok importálása az Automation-fiókból](media/az-modules/import-module.png)

Ez az importálási folyamat a [PowerShell-galérián](https://www.powershellgallery.com) keresztül is elvégezhető, ha megkeresi az importálási modult. Miután megtalálta a modult, válassza ki, válassza az **Azure Automation** fület, és kattintson az Üzembe helyezés az **Azure Automationben parancsra.**

![Modulok importálása közvetlenül a galériából](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>A runbookok tesztelése

Miután importálta az Az modulokat az Automation-fiókba, elkezdheti a runbookok szerkesztését a modulok használatához. A parancsmagok többsége ugyanazokkal a nevekkel rendelkezik, mint az AzureRM-modul, azzal a különbséggel, hogy az AzureRM (vagy AzureRm) előtag az Az értékre módosult. Az elnevezési konvenciót nem követő modulok listáját [a kivételek listája](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)tartalmazza.

A runbook új parancsmagok használatára történő módosításának tesztelésének egyik módja a runbook elején történő használat. `Enable-AzureRmAlias -Scope Process` Ha hozzáadja ezt a parancsot a runbookhoz, a parancsfájl módosítások nélkül futtatható.

## <a name="after-migration-details"></a>Áttelepítés utáni részletek

Az áttelepítés befejezése után ne próbálja meg elindítani a runbookok azureRM-modulok használatával az Automation-fiók. Azt is javasoljuk, hogy ne importálja vagy frissítse az AzureRM-modulokat a fiókban. Vegye figyelembe a fiókot áttelepített Az, és működnek az Az modulok csak. 

Amikor új Automation-fiókot hoz létre, a meglévő AzureRM-modulok továbbra is telepítve vannak. Továbbra is frissítheti az oktatóanyag runbookok AzureRM-parancsmagokkal. Azonban nem kell futtatni ezeket a runbookokat.

## <a name="next-steps"></a>További lépések

Az Az modulok használatáról az [Az modul használatának első lépései](/powershell/azure/get-started-azureps?view=azps-1.1.0).
