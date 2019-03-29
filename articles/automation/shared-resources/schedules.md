---
title: Az Azure Automationben ütemezések
description: Az automatizálási ütemezések runbookok automatikus indításra az Azure automationben ütemezésére szolgálnak. Ismerteti, hogyan hozhat létre és kezelhet az ütemezés szerint, így képes automatikusan elindít egy runbookot egy adott időpontban vagy ismétlődő ütemezés szerint.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d99c6b419ee201be50e74849cd95a332845f5b73
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623168"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Runbook ütemezése az Azure Automationben

Az Azure Automation és a egy megadott időpontban el egy runbook ütemezése, kapcsolja össze egy vagy több ütemezés. Ütemezés beállítható úgy, hogy futtassa egyszer vagy egy került az óránkénti vagy napi ütemezés runbookok az Azure Portalon. Is ütemezheti őket hetente, havonta, a hét meghatározott napjain vagy a hónap napjait, vagy egy adott nap a hónapban. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Ütemezések jelenleg nem támogatja az Azure Automation DSC-konfigurációk.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok

A következő táblázatban található parancsmagokkal hozhat létre és kezelhet az Azure Automation PowerShell-lel ütemezések szolgálnak. Részét képezi a [Azure PowerShell-modul](/powershell/azure/overview).

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Ütemezés lekérése. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Létrehoz egy új ütemezést. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Eltávolítja az ütemezés szerint. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Meglévő ütemezés tulajdonságainak beállítása. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Lekéri az ütemezett runbookok. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Egy runbook hozzárendeli egy ütemezés. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Az ütemezett runbookok dissociates. |

## <a name="creating-a-schedule"></a>Ütemezés létrehozása

Létrehozhat egy új ütemezést a runbookok az Azure Portalon vagy a PowerShell használatával.

> [!NOTE]
> Az Azure Automation használja a legújabb modulokat az Automation-fiók egy új ütemezett feladatot a futása során.  A runbookok és a folyamatok automatizálása azok érintő elkerüléséhez először tesztelje az Automation-fiók tesztelési dedikált csatolt ütemezéssel rendelkező runbookokat.  Ez igazolja az ütemezett runbookok továbbra is megfelelően működjenek, és ha nem, akkor további, és hibaelhárítást a frissített runbook-verzió éles-ba való migrálás előtt meg kell adni a módosítások alkalmazása.
> Az Automation-fiók nem automatikusan kap új verzióit modulok, kivéve, ha frissítette azokat manuálisan kiválasztásával a [frissítés az Azure-modulok](../automation-update-azure-modules.md) parancsát a **modulok**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure Portalon

1. Válassza ki az Azure Portalon, az automation-fiók **ütemezések** a szakaszában **megosztott erőforrások** a bal oldalon.
2. Kattintson a **ütemezés hozzáadása** az oldal tetején.
3. Az a **új ütemezés** panelen adjon meg egy **neve** és opcionálisan egy **leírás** az új ütemezés számára.
4. Válassza ki, hogy az ütemezés egy alkalommal fut le, vagy ismétlődő ütemezés szerint kiválasztásával **egyszer** vagy **ismétlődő**. Ha **egyszer** adjon meg egy **kezdési idő**, és kattintson a **létrehozás**. Ha **ismétlődő**, adjon meg egy **kezdési idő** és a **Ismétlődés minden**, válassza ki a gyakoriságot, hogy milyen gyakran szeretné a runbook ismételje meg – a **óra**, **nap**, **hét**, vagy **hónap**.
    1. Ha **hét**, már a megadott közül választhat a hét azon napjai, listáját. Válassza ki a kívánt számú nappal. Az első futtatásakor az ütemezés a kiválasztott a kezdő időpont utáni első napján fog történni. Válassza ki az ütemezést hétvégi, például válassza ki a **szombat** és **vasárnap**.

       ![A beállítás hétvégi ismétlődő ütemezés szerint](../media/schedules/week-end-weekly-recurrence.png)

    2. Ha **hónap**, felhőszolgáltatására, különböző lehetőségeket. Az a **havi előfordulások** lehetőséget, válassza ki vagy **hónap napjai** vagy **napok**. Ha úgy dönt, **hónap napjai**, naptár jelenik meg, amely lehetővé teszi, hogy válassza ki a kívánt számú nappal. Ha például nem következik be, az aktuális hónap 31 dátumot, az ütemezés futtatásának megakadályozása. Ha azt szeretné, hogy az utolsó nappal futtatni az ütemezést, válassza a **Igen** alatt **Futtatás a hónap utolsó napján**. Ha úgy dönt, **napok**, a **Ismétlődés minden** lehetőség jelenik meg. Válasszon **első**, **második**, **harmadik**, **negyedik**, vagy **utolsó**. Végül válassza ki a naponta, vagy ismételje meg a.

       ![Havi ütemezés a hónap első tizenötödik és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

5. Ezzel a módszerrel kattintson **létrehozás**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Új ütemezés létrehozása a PowerShell használatával

Használja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag ütemezés létrehozásához. Megadhatja a kezdési időpont az ütemezés és a gyakoriság kell futnia. Az alábbi példák bemutatják, hogyan hozhat létre a számos különböző frissítésütemezési forgatókönyv.

#### <a name="create-a-one-time-schedule"></a>Hozzon létre egy ütemezéssel

Az alábbi Példaparancsok hozzon létre egy ütemezéssel.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Hozzon létre egy ismétlődő ütemezés szerint

A következő mintaparancsok bemutatják, hogyan hozhat létre, amely a minden nap 1:00 Órakor egy évig ismétlődő ütemezés szerint.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Hetente ismétlődő ütemezés létrehozása

A következő mintaparancsok bemutatják, hogyan hozhat létre, amely csak a munkanapokon futtat heti ütemezés.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Hozzon létre a hétvégékre hetente ismétlődő ütemezés szerint

Az alábbi mintaparancsok bemutatják, hogyan hozhat létre a futó hétvégén csak heti ütemezés.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Hozzon létre egy ismétlődő ütemezés szerint az első, a hónap 15., és az utolsó nap

A következő mintaparancsok bemutatják, hogyan hozhat létre, amely a hónap 1., 15 és utolsó napján futtat ismétlődő ütemezés szerint.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása egy runbook

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha a runbook paraméterekkel rendelkezik, majd megadhat értékeket a számukra. Értékeket kell megadnia minden kötelező paraméterhez, és előfordulhat, hogy adjon meg értékeket a kötelező paramétereket. Ezekkel az értékekkel minden alkalommal, amikor a runbook az ütemezés szerint elindult. Csatolja ugyanazon a runbookon belül egy másik ütemezést, és adja meg a különböző paraméterértékekkel.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Összekapcsolhat egy ütemezést egy runbookhoz, az Azure portal használatával

1. Válassza ki az Azure Portalon, az automation-fiók **Runbookok** a szakaszában **Folyamatautomatizálás** a bal oldalon.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nincs csatolva egy ütemezést, akkor létrehozhat egy új ütemezést vagy összekapcsolás meglévő ütemezéssel van kínáljuk.
4. Ha a runbook paraméterekkel rendelkezik, válassza a beállítást **futtatási beállítások (alapértelmezett: Azure) módosítása** és a **paraméterek** be az adatokat ablaktáblán jelennek meg.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Ütemezés összekapcsolása egy runbookot, a PowerShell-lel

Használhatja a [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) parancsmag összekapcsolhat egy ütemezést. Megadhatja a runbook-paraméterek értékei a paraméterek paraméterrel. További információk a paraméterértékek meghatározásáról: [Runbook elindítása az Azure Automationben](../automation-starting-a-runbook.md).
Az alábbi mintaparancsok bemutatják, hogyan összekapcsolhat egy ütemezést egy runbook egy Azure Resource Manager-parancsmaggal paraméterekkel.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Runbookok ütemezése gyakrabban

A leggyakoribb állítható be egy Azure Automation ütemezési időköz egy óra. Ha az ütemezéseket, hajtsa végre, mint a gyakran van szükség, két lehetőség van:

* Hozzon létre egy [webhook](../automation-webhooks.md) runbook és használatára vonatkozó [Azure Scheduler](../../scheduler/scheduler-get-started-portal.md) a webhook hívása. Az Azure Scheduler biztosít a több részletes granularitási ütemezés meghatározásakor.

* Hozzon létre négy ütemezések eltérés körülbelül óránként futó 15 percen belül minden indítása. Ebben a forgatókönyvben lehetővé teszi, hogy a runbook futtatása 15 percenként az eltérő ütemezésekkel való.

## <a name="disabling-a-schedule"></a>Ütemezés letiltása

Ha letilt egy ütemezést, bármely olyan runbookhoz, hozzá kell kapcsolni már nem adott ütemezés szerint futtatja. Manuálisan ütemezés letilthatja vagy gyakorisággal ütemezések esetében lejárati idő beállítása, azok létrehozásakor. A lejárati idő elérésekor az ütemterv le lett tiltva.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Az Azure Portalról ütemezésének letiltása

1. Válassza ki az Azure Portalon, az Automation-fiók **ütemezések** a szakaszában **megosztott erőforrások** a bal oldalon.
2. Kattintson a jobb oldali ablaktáblában nyissa meg az ütemezés nevét.
3. Változás **engedélyezve** való **nem**.

### <a name="to-disable-a-schedule-with-powershell"></a>A PowerShell-lel ütemezésének letiltása

Használhatja a [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) parancsmag meglévő ütemezés tulajdonságainak módosításához. Tiltsa le az ütemezést, adja meg a **hamis** a a **IsEnabled** paraméter.

Az alábbi Példaparancsok szemléltetik egy runbook egy Azure Resource Manager-parancsmaggal ütemezésének letiltása.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* Ismerkedés az Azure Automation runbookjai, lásd: [Runbook elindítása az Azure Automationben](../automation-starting-a-runbook.md)

