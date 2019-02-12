---
title: Az modulok használata az Azure Automationben
description: Ez a cikk ismerteti Az modulok használatával az Azure Automationben
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e9240949c589717303fe00205c5374b5e3a6a791
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008257"
---
# <a name="az-module-support-in-azure-automation"></a>Házirendmodul-támogatás az Azure automationben

Az Azure automation használata támogatja a [Az Azure Powershell modul](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) a runbookokban. A Az modul nem importál automatikusan az új vagy meglévő Automation-fiókokat. Ez a cikk ismerteti Az modulok használata az Azure Automation szolgáltatással.

## <a name="considerations"></a>Megfontolandó szempontok

Számos szempontot figyelembe kell venni a Az modul használata az Azure Automationben. Az Automation-fiókban a magasabb szintű megoldások által használható Runbookjai és moduljai. Runbookok szerkesztése vagy modulok frissítése potenciálisan problémákat okozhat a forgatókönyvekkel. Tesztelje az összes forgatókönyvek és megoldások gondosan az önálló Automation-fiókhoz mielőtt importálná az új `Az` modulok. Modulok módosításait negatív hatással lehet a magasabb szintű megoldások, például az Update Management és a gépek indítása/leállítása közben csúcsidőn kívüli órákra. Azt javasoljuk, hogy nem módosítják a modulok és a runbook az Automation-fiókok, amelyek tartalmazzák a hozzáadott megoldások. Ez a viselkedés jellemző a Az modulok nem. Ez a viselkedés figyelembe kell venni, amikor módosításokat bemutatná az Automation-fiók.

Importálása egy `Az` modulban, az Automation-fiókját a PowerShell-munkamenetben, a runbookokat használó automatikusan nem importálja a modult. Rendszer importálja a parancsmagmodulokat a PowerShell-munkamenetet a következő esetekben:

* Ha a modulból a parancsmag meghívása egy runbookból
* Amikor egy runbook importálja azokat az explicit módon a `Import-Module` parancsmag
* Egy másik modul attól függően, a modul PowerShell-munkamenetbe való importálás után

> [!IMPORTANT]
> Fontos győződjön meg arról, hogy a runbookok az Automation-fiók csak importálása `Az` vagy `AzureRM` modulok azokat a runbookokat és nem is által használt PowerShell-munkameneteket. Ha `Az` importálása előtt `AzureRM` egy runbook a runbook fog befejeződni, de egy [get_SerializationSettings mód hiba](/troubleshoot/runbooks.md#get-serializationsettings) fogja megjeleníteni a feladatstreamek és parancsmagok előfordulhat, hogy nem lettek megfelelően hajtott végre. Importálásakor `AzureRM` , majd `Az` a runbook végez, továbbra is teljes körű, látni fogja a feladatstreamek arról, hogy a hiba, de mindkét `Az` és `AzureRM` importálhatók ugyanabban a munkamenetben vagy ugyanazon a runbookon belül használt.

## <a name="migrating-to-az-modules"></a>Áttelepítés Az modulok

Tesztelje az áttelepítést az AzureRM-modulok egy Automation-fiók teszt helyett Az modulok használata ajánlott. Hogy az Automation-fiók létrehozása után a következő lépések segítségével győződjön meg arról, a migrálás zavartalan kerül:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Állítsa le és AzureRM-modulok használó összes runbookot ütemezés törlése

Győződjön meg arról, hogy minden meglévő runbookok, amelyek nem működnek a `AzureRM` parancsmagok, állítsa le és ütemezés törlése az összes runbook használó `AzureRM` modulok. Láthatja, hogy milyen ütemezések létezik, és mely ütemezések el kell távolítani a következő példa futtatásával:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Tekintse át az egyes ütemezések külön-külön győződjön meg arról, hogy átütemezheti, a jövőben a runbookok szükség esetén fontos.

### <a name="import-the-az-modules"></a>Az Az-modulok importálása

Az importálás kizárólag a Az modulok, a runbookok számára szükséges. Ne importálja az összegző `Az` , mert a modul tartalmazza az összes a `Az.*` modulok importálni. Ez az útmutató a modulok esetében megegyezik.

A [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) modul az a más függőség `Az.*` modulok. Ezért ez a modul az Automation-fiókba más modulok importálása előtt importálni kell.

Válassza ki az Automation-fiók **modulok** alatt **megosztott erőforrások**. Kattintson a **Tallózás a katalógusban** megnyitásához a **Tallózás a katalógusban** lapot.  A keresősávba írja be a modul neve (például `Az.Accounts`). A PowerShell-modul lapon kattintson a **importálása** a modul importálásához az Automation-fiókba.

![Importálja a modulokat az Automation-fiók](media/az-modules/import-module.png)

Ez az importálási folyamat is végezhető el a [PowerShell-galériából](https://www.powershellgallery.com) modul való kereséssel. Miután megtalálta a modult, válassza ki, majd a a **Azure Automation** lapra, majd **üzembe helyezés az Azure Automation**.

![Modulok importálása közvetlenül a katalógusból](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>A runbook tesztelése

Miután a `Az` rendszer importálja az Automation-fiókban, most már megkezdheti a modult szeretné használni Az inkább a runbookok szerkesztéséhez. A parancsmagok többsége kivételével azonos névvel rendelkeznek `AzureRM` értékre változott `Az`. Ne kövesse ezt a folyamatot modulok listáját lásd: [kivétellistáján](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names).

A runbookok tesztelésére, mielőtt módosítja a runbookot, hogy az új parancsmagok használatával a egyirányú `Enable-AzureRMAlias -Scope Process` runbook elején. Ez a runbook ad, a runbook módosítás nélkül futtathatja.

## <a name="after-migration-details"></a>Miután a migrálás részletei

Az áttelepítés befejezése után ne indítsa el a runbookok használatával `AzureRM` modulok hosszabb ideig a fiókban. Ajánlott továbbá nem importálhatja, vagy frissíteni `AzureRM` modulok ehhez a fiókhoz. A pillanattól kezdve, fontolja meg ezt a fiókot át `Az`, és működtetésének megpróbálásakor `Az` csak modulok. Egy új Automation-fiók létrehozásakor a meglévő `AzureRM` modulok továbbra is telepítve lesz, és a runbook még mindig lehet készült, `AzureRM` parancsmagok. Ezek a runbookok nem kell futtatni.

## <a name="next-steps"></a>További lépések

Az modulok használatával kapcsolatos további tudnivalókért lásd: [– első lépések Az modul](/powershell/azure/get-started-azureps?view=azps-1.1.0).