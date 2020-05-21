---
title: Ütemtervek kezelése Azure Automation
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és dolgozhat ütemtervet Azure Automation.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 848be71764bb503879c34ed6667a43909ef19c13
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713437"
---
# <a name="manage-schedules-in-azure-automation"></a>Ütemtervek kezelése Azure Automation

Ahhoz, hogy egy runbook egy adott időpontban kezdődjön Azure Automation, egy vagy több ütemtervhez kell csatolni. Az ütemterv beállítható úgy, hogy egyszer vagy ismétlődően, óránként vagy napi szinten fusson a Azure Portal runbookok. Azt is megteheti, hogy hetente, havonta vagy a hónap adott napjain, vagy a hónap adott napján van-e beütemezhetve. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Azure Automation támogatja a nyári időszámítást, és megfelelő módon ütemezhet az automatizálási műveletekhez.

> [!NOTE]
> Az ütemtervek jelenleg nincsenek engedélyezve Azure Automation DSC-konfigurációkhoz.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Az ütemtervekhez való hozzáféréshez használt PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok automatizálási ütemterveket hoznak létre és kezelhetnek a PowerShell használatával. Az az [modulok](modules.md#az-modules)részét képezik. 

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Lekéri az ütemtervet. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Ütemezett runbookok beolvasása. |
| [Új – AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Új ütemtervet hoz létre. |
| [Regisztráció – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook társít egy ütemtervhez. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Egy ütemterv eltávolítása. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Egy meglévő ütemterv tulajdonságainak beállítása. |
| [Regisztráció törlése – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Egy runbook leválasztása az ütemtervből. |

## <a name="create-a-schedule"></a>Ütemterv létrehozása

A runbookok új ütemtervet hozhat létre a Azure Portal vagy a PowerShell használatával. Ha el szeretné kerülni a runbookok és az általuk automatizálható folyamatokat, először tesztelje azokat a runbookok, amelyeken a társított ütemtervek egy tesztelésre kijelölt Automation-fiókkal rendelkeznek. Egy teszt ellenőrzi, hogy az ütemezett runbookok továbbra is megfelelően működik-e. Ha problémát tapasztal, a frissített runbook-verzió éles környezetbe történő áttelepítése előtt hárítsa el és alkalmazza a szükséges módosításokat.

> [!NOTE]
> Az Automation-fiókja nem kapja meg automatikusan a modulok új verzióit, hacsak nem frissíti őket manuálisan az [Azure-modulok frissítése](../automation-update-azure-modules.md) lehetőség kiválasztásával a **modulokból**. A Azure Automation az Automation-fiók legújabb moduljait használja új ütemezett feladatok futtatásakor. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Új ütemterv létrehozása a Azure Portal

1. A Azure Portal az Automation-fiókjából válassza a bal oldalon található **megosztott erőforrások** szakaszban az **ütemtervek** lehetőséget.
1. A lap tetején kattintson az **ütemterv hozzáadása** lehetőségre.
1. Az **új ütemterv** panelen adjon meg egy nevet, és szükség esetén adja meg az új ütemterv leírását.
1. Válassza ki, hogy az ütemterv **egyszer** vagy **ismétlődően**fusson-e egy egyszeri vagy ismétlődő kijelöléssel. Ha **egyszer**van kiválasztva, adjon meg egy kezdési időpontot, majd válassza a **Létrehozás**lehetőséget. Ha az **ismétlődő**lehetőséget választja, adjon meg egy kezdési időpontot. Az **ismétlődéshez**válassza ki, hogy milyen gyakran szeretné megismételni a runbook. Válassza az óra, nap, hét vagy hónap lehetőséget.
    1. Ha a **hét**lehetőséget választja, a hét napjai láthatók. Válasszon annyi napot, amennyit csak szeretne. Az ütemterv első futtatása a kezdési időpont után kiválasztott első napon fog történni. Ha például hétvégi menetrendet szeretne választani, válassza a szombat és a vasárnap lehetőséget.
    
       ![Hétvégi ismétlődő ütemterv beállítása](../media/schedules/week-end-weekly-recurrence.png)

    2. Ha a **hónap**lehetőséget választja, különböző beállításokat kap. A **havi előfordulások** beállításnál válassza a **hónap napja** vagy a **hét napja**lehetőséget. Ha a **hónap napja**lehetőséget választja, akkor a naptár úgy jelenik meg, hogy a kívánt számú napot válassza ki. Ha olyan dátumot választ, mint az aktuális hónapban nem előforduló 31, az ütemterv nem fog futni. Ha azt szeretné, hogy az ütemterv az utolsó napon fusson, válassza az **Igen** lehetőséget a **hónap utolsó napján futtatva**. Ha a **hét napja**lehetőséget választja, megjelenik az **Ismétlődés minden** lehetőség. Válassza az **első**, a **második**, a **harmadik**, a **negyedik**vagy a **utolsó**lehetőséget. Végül válasszon ki egy napot a megismétléshez.

       ![Havi ütemterv a hónap első, tizenötödik és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

1. Ha elkészült, válassza a **Létrehozás**lehetőséget.

### <a name="create-a-new-schedule-with-powershell"></a>Új ütemterv létrehozása a PowerShell-lel

Ütemtervek létrehozásához használja a [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) parancsmagot. Megadhatja az ütemterv kezdési idejét és a futtatni kívánt gyakoriságot. Az alábbi példák bemutatják, hogyan hozhat létre számos különböző ütemterv-forgatókönyvet.

#### <a name="create-a-one-time-schedule"></a>Egyszeri ütemterv létrehozása

Az alábbi példa egy egyszeri ütemtervet hoz létre.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Ismétlődő ütemterv létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre olyan ismétlődő ütemezést, amely minden nap 1:00 ÓRAKOR fut egy évig.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Heti ismétlődő ütemterv létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre hetente csak hétköznapokon futó ütemtervet.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Heti ismétlődő ütemterv létrehozása hétvégek számára

Az alábbi példa bemutatja, hogyan hozhat létre hetente csak hétvégeken futó ütemtervet.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Ismétlődő ütemterv létrehozása a hónap első, tizenötödik és utolsó napjaihoz

Az alábbi példa bemutatja, hogyan hozhat létre olyan ismétlődő ütemtervet, amely egy hónap első, tizenötödik és utolsó napján fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Ütemterv összekapcsolása egy runbook

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha egy runbook paraméterekkel rendelkezik, akkor megadhatja az értékeket. Meg kell adnia minden kötelező paraméter értékét, és megadhatja a választható paraméterek értékét is. Ezeket az értékeket a rendszer minden alkalommal felhasználja, amikor ez az ütemterv elindítja a runbook. Ugyanezt a runbook egy másik ütemtervhez is csatolhatja, és különböző paramétereket adhat meg.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ütemterv összekapcsolása egy runbook a Azure Portal

1. A Azure Portal Automation-fiókjából válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
1. Válassza ki az ütemezni kívánt runbook nevét.
1. Ha a runbook jelenleg nincs ütemtervhez csatolva, lehetősége van arra, hogy új ütemtervet hozzon létre, vagy egy meglévő ütemtervre hivatkozzon.
1. Ha a runbook paraméterekkel rendelkezik, akkor válassza a **Futtatási beállítások módosítása (alapértelmezett: Azure)** lehetőséget, és megjelenik a **Paraméterek** panel. Itt megadhatja a paraméterre vonatkozó információkat.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ütemterv összekapcsolása runbook a PowerShell-lel

Az ütemterv összekapcsolásához használja a [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) parancsmagot. A Parameters paraméter segítségével megadhatja a runbook paramétereinek értékét. A paraméterek értékének megadásával kapcsolatos további információkért lásd: [Runbook elindítása Azure Automationban](../automation-starting-a-runbook.md).
Az alábbi példa azt szemlélteti, hogyan kapcsolhat össze egy ütemtervet egy runbook egy Azure Resource Manager parancsmag használatával paraméterekkel.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Runbookok ütemezett futtatása gyakrabban

Az a leggyakoribb időköz, ameddig Azure Automation konfigurálható egy óra. Ha azt szeretné, hogy az ütemtervek gyakrabban fussanak, mint az, két lehetőség közül választhat:

* Hozzon létre egy [webhookot](../automation-webhooks.md) a runbook, és [Azure Logic apps](../../logic-apps/logic-apps-overview.md) használatával hívja meg a webhookot. Azure Logic Apps részletesebb részletességet biztosít az ütemterv definiálásához.

* Hozzon létre négy olyan ütemtervet, amely 15 percen belül kezdődik, és óránként egyszer fut. Ez a forgatókönyv lehetővé teszi, hogy a runbook 15 percenként fusson a különböző ütemtervekkel.

## <a name="disable-a-schedule"></a>Ütemterv letiltása

Ha letilt egy ütemtervet, az ahhoz társított összes runbook már nem fut az adott ütemterven. Manuálisan letilthatja az ütemtervet, vagy megadhat egy lejárati időt az ütemtervekhez a létrehozásuk gyakorisága alapján. A lejárati idő elérésekor az ütemterv le van tiltva.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Ütemterv letiltása a Azure Portal

1. Az Automation-fiókban válassza az **ütemtervek** lehetőséget a **megosztott erőforrások**területen.
1. Válassza ki az ütemterv nevét a részletek ablaktábla megnyitásához.
1. Az **engedélyezve** érték **nem**értékre vált.

> [!NOTE]
> Ha le szeretné tiltani egy olyan ütemtervet, amely múltbeli kezdési időpontot tartalmaz, a Mentés előtt módosítania kell a kezdési dátumot a jövőbeli időpontra.

### <a name="disable-a-schedule-with-powershell"></a>Ütemterv letiltása a PowerShell-lel

A [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) parancsmag használatával módosíthatja a meglévő ütemtervek tulajdonságait. Az ütemterv letiltásához a paraméternél a FALSE értéket kell megadnia `IsEnabled` .

Az alábbi példa bemutatja, hogyan tilthatja le egy runbook ütemezett használatát egy Azure Resource Manager parancsmag használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Ütemterv eltávolítása

Ha készen áll az ütemtervek eltávolítására, használhatja a Azure Portal vagy a PowerShellt is. Ne feledje, hogy csak az előző szakaszban leírtak szerint letiltott ütemtervet lehet eltávolítani.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ütemterv eltávolítása a Azure Portal használatával

1. Az Automation-fiókban válassza az **ütemtervek** lehetőséget a **megosztott erőforrások**területen.
2. Kattintson az ütemterv nevére a részletek ablaktábla megnyitásához.
3. Kattintson a **Törlés** gombra.

### <a name="remove-a-schedule-with-powershell"></a>Ütemterv eltávolítása a PowerShell-lel

`Remove-AzAutomationSchedule`Meglévő ütemterv törléséhez használhatja a parancsmagot az alábbi ábrán látható módon. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Következő lépések

* [Modulok kezelése az Azure Automationben](modules.md)
* [Runbook végrehajtása az Azure Automationben](../automation-runbook-execution.md)
