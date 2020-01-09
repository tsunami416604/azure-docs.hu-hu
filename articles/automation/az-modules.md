---
title: Az modulok használata az Azure Automationben
description: Ez a cikk az az modulok Azure Automation használatával történő használatát ismerteti
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 8c832982a5525b0296155197d89684932cebaa95
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418827"
---
# <a name="az-module-support-in-azure-automation"></a>Az modul támogatása Azure Automation

Az Azure Automation lehetővé teszi az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) használatát a runbookok. Az az modul nincs automatikusan importálva az új vagy meglévő Automation-fiókokban. Ez a cikk azt ismerteti, hogyan használható az az modulok Azure Automation használatával.

## <a name="considerations"></a>Megfontolandó szempontok

Számos dolgot figyelembe kell venni, ha az az modult használja Azure Automationban. A runbookok és modulokat az Automation-fiók magasabb szintű megoldásai is használhatják. A runbookok szerkesztése vagy a modulok frissítése potenciálisan problémákat okozhat a runbookok. Az új `Az` modulok importálása előtt gondosan tesztelje az összes runbookok és-megoldást egy külön Automation-fiókban. A modulok módosításai negatívan megadhatják a [Start/Stop](automation-solution-vm-management.md) megoldást. Javasoljuk, hogy ne változtassa meg a modulokat és a runbookok olyan Automation-fiókokban, amelyek bármilyen megoldást tartalmaznak. Ez a viselkedés nem jellemző az az modulokra. Ezt a viselkedést figyelembe kell venni az Automation-fiók módosításainak bevezetésekor.

`Az` modulnak az Automation-fiókba való importálása nem importálja automatikusan a modult a runbookok által használt PowerShell-munkamenetbe. A modulok a következő helyzetekben importálhatók a PowerShell-munkamenetbe:

* Ha egy modulból parancsmagot hív meg egy runbook
* Ha egy runbook explicit módon importálja az `Import-Module` parancsmaggal
* Ha a modultól függően egy másik modult importál egy PowerShell-munkamenetbe

> [!IMPORTANT]
> Fontos, hogy az Automation-fiókban lévő runbookok csak `Az` vagy `AzureRM` modulokat importáljon a runbookok által használt PowerShell-munkamenetbe, és ne mindkettőt. Ha a `Az` a runbook `AzureRM` előtt lett importálva, a runbook befejeződik, de [a get_SerializationSettings metódusra hivatkozó hiba](troubleshoot/runbooks.md#get-serializationsettings) a feladatokhoz tartozó adatfolyamokban fog megjelenni, és előfordulhat, hogy a parancsmagok nem lettek megfelelően végrehajtva. Ha `AzureRM` importál, majd `Az` a runbook továbbra is befejeződik, de hibaüzenet jelenik meg, amely szerint a `Az` és a `AzureRM` nem importálhatók ugyanabban a munkamenetben, vagy nem használhatók ugyanabban a runbook.

## <a name="migrating-to-az-modules"></a>Áttelepítés az az modulokba

Javasoljuk, hogy tesztelje az áttelepítést a AzureRM-modulok helyett az az modulok használatára a test Automation-fiókban. Az Automation-fiók létrehozása után az alábbi lépéseket követve gondoskodhat arról, hogy az áttelepítés zökkenőmentesen haladjon át:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Az összes AzureRM-modult használó runbook leállítása és visszaírása

Annak biztosítása érdekében, hogy ne futtasson `AzureRM`-parancsmagokat használó meglévő runbookok, állítsa le és ütemezze be a `AzureRM` modulokat használó összes runbookok. A következő példa futtatásával megtekintheti, hogy mely ütemtervek léteznek, és mely ütemterveket kell eltávolítani:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Fontos áttekinteni az egyes ütemterveket, hogy a későbbiekben újra lehessen ütemezni a runbookok, ha szükséges.

### <a name="import-the-az-modules"></a>Az az modulok importálása

Csak az az runbookok szükséges modulok importálása. Ne importálja a kumulatív `Az` modult, mert az tartalmazza az összes importálandó `Az.*` modult. Ez az útmutató minden modul esetében azonos.

Az az [. accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modul a többi `Az.*` modultól függ. Emiatt ezt a modult az Automation-fiókjába kell importálni az egyéb modulok importálása előtt.

Az Automation-fiókból válassza a **modulok** elemet a **megosztott erőforrások**területen. Kattintson a **Tallózás** a katalógusban elemre a **tallózási** katalógus lap megnyitásához.  A keresősáv mezőben adja meg a modul nevét (például `Az.Accounts`). A PowerShell-modul lapon kattintson az **Importálás** elemre a modul Automation-fiókba való importálásához.

![Modulok importálása az Automation-fiókból](media/az-modules/import-module.png)

Ezt az importálási folyamatot a [PowerShell-Galéria](https://www.powershellgallery.com) is elvégezheti, ha megkeresi a modult. Miután megtalálta a modult, jelölje ki, és a **Azure Automation** lapon kattintson a telepítés elemre **Azure Automation**.

![Modulok importálása közvetlenül a katalógusból](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>A runbookok tesztelése

Miután importálta a `Az` modulokat az Automation-fiókjába, most már megkezdheti a runbookok szerkesztését az az modul használatára. A parancsmagok többsége ugyanazzal a névvel rendelkezik, kivéve a `AzureRM` `Az`re módosult. Az ezt a folyamatot nem követő modulok listáját a [kivételek listájában](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)tekintheti meg.

A runbookok tesztelésének egyik módja, mielőtt módosítja a runbook az új parancsmagok használatára, a runbook elején a `Enable-AzureRMAlias -Scope Process` használatával. Ha hozzáadja ezt a runbook, a runbook módosítása nélkül is futtatható.

## <a name="after-migration-details"></a>Áttelepítési részletek

Az áttelepítés befejezése után ne indítsa el a runbookok a fiók `AzureRM` moduljainak használatával. Azt is javasoljuk, hogy ne importáljon vagy frissítsen `AzureRM` modulokat ebben a fiókban. Ettől a pillanattól kezdve vegye figyelembe, hogy ez a fiók át lett telepítve a `Az`ba, és csak `Az` modulokkal működik. Új Automation-fiók létrehozásakor a rendszer továbbra is telepíti a meglévő `AzureRM` modulokat, és az oktatóanyag runbookok továbbra is `AzureRM` parancsmagokkal lesz létrehozva. Ezek a runbookok nem futtathatók.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az az modulok használatáról, tekintse meg az [első lépések az modullal](/powershell/azure/get-started-azureps?view=azps-1.1.0)című témakört.
