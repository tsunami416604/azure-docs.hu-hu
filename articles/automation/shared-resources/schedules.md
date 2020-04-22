---
title: Ütemezések kezelése az Azure Automationben
description: Az automatizálási ütemezések segítségével ütemezheti a runbookok az Azure Automation automatikus indításához. Ez a témakör azt ismerteti, hogy miként hozhat létre és kezelhet ütemezést, hogy egy adott időpontban vagy ismétlődő ütemezés szerint automatikusan elindíthatja a runbookot.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732795"
---
# <a name="manage-schedules-in-azure-automation"></a>Ütemezések kezelése az Azure Automationben

Ha egy runbookot az Azure Automationben egy adott időpontban szeretne elindítani, egy vagy több ütemezéshez csatolja. Az ütemezés beállítható úgy, hogy egyszer vagy egy ismétlődő óránkénti vagy napi ütemezésszerint fusson az Azure Portalon. Ütemezheti őket heti, havi, a hét adott napjaira, a hónap napjaira vagy a hónap egy adott napjára. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Az ütemezések jelenleg nem támogatják az Azure Automation DSC-konfigurációkat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok ütemezések létrehozásához és kezeléséhez használható az Azure Automation PowerShell használatával. Az [Azure PowerShell-modul](/powershell/azure/overview)részeként szállítanak.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Beolvassa az ütemezést. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Az ütemezett runbookok beolvasása. |
| [Új-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Új ütemezést hoz létre. |
| [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook ot társít egy ütemezéshez. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Eltávolítja az ütemtervet. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Egy meglévő ütemezés tulajdonságainak beállítása. |
| [Regisztráció megszüntetése-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |A runbookok ütemezéstől való elhatárolta. |

## <a name="creating-a-schedule"></a>Ütemezés létrehozása

Létrehozhat egy új ütemezést a runbookok hoz létre az Azure Portalon vagy a PowerShell használatával.

> [!NOTE]
> Az Azure Automation az Automation-fiók legújabb moduljait használja egy új ütemezett feladat futtatásakor.  Annak elkerülése érdekében, hogy befolyásolja a runbookok és az általuk automatizált folyamatok, először tesztelje azokat a runbookokat, amelyek összekapcsoltaazták az ütemezéseket egy tesztelésre szánt Automation-fiókkal.  Ez ellenőrzi az ütemezett runbookok továbbra is megfelelően működik, és ha nem, akkor további hibaelhárítási és a szükséges módosításokat, mielőtt a frissített Runbook-verzió éles környezetbe való áttelepítése.
> Az Automation-fiók nem kap automatikusan új modulokat, kivéve, ha manuálisan frissítette őket az [Azure-modulok frissítése](../automation-update-azure-modules.md) lehetőség kiválasztásával a **modulok**közül.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure Portalon

1. Az Azure Portalon az automation-fiókból válassza a Bal oldali **Megosztott erőforrások** szakasz **ütemezése** lehetőséget.
2. Kattintson az **Ütemezés hozzáadása** gombra a lap tetején.
3. Az Új ütemezés ablaktáblán írja be az új ütemezés nevét és leírását.
4. Adja meg, hogy az ütemezés egy alkalommal vagy ismétlődő ütemezés szerint fut-e az **Egyszer** vagy **az Ismétlődő**lehetőséget választva. Ha az **Egyszer**lehetőséget választja, adja meg a kezdési időpontot, majd kattintson a **Létrehozás gombra.** Ha az Ismétlődő lehetőséget **választja,** adja meg a kezdési időpontot. A **Recur every**, adja meg, hogy milyen gyakran szeretné megismételni a runbookot - óra, nap, hét vagy hónap szerint.
    1. Ha a **hetet**választja, a hét napjai közül választhat. Jelöljön ki annyi napot, amennyit csak akar. Az ütemezés első futtatása a kezdési időpont után kiválasztott első napon történik. Ha például egy hétvégi ütemezést szeretne választani, válassza a Szombat és vasárnap lehetőséget. 
    
       ![Hétvégi ismétlődő ütemezés beállítása](../media/schedules/week-end-weekly-recurrence.png)

    2. Ha a **hónapot**választja, különböző lehetőségeket kap. A **Havi előfordulások** beállításhoz válassza a **Hónap napok** vagy **a Hét napjait.** Ha a **Hónap napjai**lehetőséget választja, egy naptár jelenik meg, amely lehetővé teszi, hogy annyi napot válasszon, amennyit csak szeretne. Ha olyan dátumot választ, mint például a 31., amely nem fordul elő az aktuális hónapban, az ütemezés nem fog futni. Ha azt szeretné, hogy az ütemezés az utolsó napon fusson, válassza az **Igen** lehetőséget **a Futtatás a hónap utolsó napján csoportban.** Ha a **Hét napok**lehetőséget választja, a **Recur minden** lehetőség megjelenik. Válassza **az Első,** **a Második,** **a Harmadik,** **a Negyedik**vagy **az Utolsó**lehetőséget. Végül válasszon egy napot, hogy ismételje meg.

       ![Havi ütemezés a hónap első, tizenötödik és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

5. Ha végzett, kattintson a **Létrehozás gombra.**

### <a name="create-a-new-schedule-with-powershell"></a>Új ütemezés létrehozása a PowerShell használatával

A [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) parancsmag használatával ütemezéseket hozhat létre. Megadhatja az ütemezés kezdési idejét és a futtatásának gyakoriságát. Az alábbi példák bemutatják, hogyan hozhat létre számos különböző ütemezési forgatókönyvet.

#### <a name="create-a-one-time-schedule"></a>Egyszeri ütemezés létrehozása

A következő mintaparancsok egyetlen időütemezést hoznak létre.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Ismétlődő ütemezés létrehozása

A következő példa bemutatja, hogyan hozhat létre egy ismétlődő ütemezést, amely egy éven át minden nap 1:00 órakor fut.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Heti ismétlődő ütemezés létrehozása

A következő példa bemutatja, hogyan hozhat létre heti ütemezést, amely csak hétköznapokon fut.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Heti ismétlődő ütemezés létrehozása hétvégékhez

A következő mintaparancsok bemutatják, hogyan hozhat létre heti ütemezést, amely csak hétvégén fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Ismétlődő ütemezés létrehozása a hónap első, 15.

A következő példa bemutatja, hogyan hozhat létre egy ismétlődő ütemezést, amely egy hónap 1., 15.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása runbookkal

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha egy runbook paramétereket tartalmaz, akkor értékeket adhat meg számukra. Minden kötelező paraméterhez meg kell adnia az értékeket, és megadhatja a választható paraméterek et. Ezek az értékek minden alkalommal használatosak, amikor a runbookot ez az ütemezés indítja el. Ugyanazt a runbookot csatolhatja egy másik ütemezéshez, és különböző paraméterértékeket adhat meg.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ütemezés csatolása egy runbookhoz az Azure Portalon

1. Az Azure Portalon az automation-fiókból válassza a **Runbookok** lehetőséget a **Folyamatautomatizálás**csoportban.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nem kapcsolódik egy ütemezéshez, akkor felajánlja a lehetőséget, hogy hozzon létre egy új ütemezést, vagy egy meglévő ütemezésre mutató hivatkozást.
4. Ha a runbook paramétereket, kiválaszthatja a **futtatási beállítások módosítása (Default:Azure)** lehetőséget, és megjelenik a Paraméterek ablaktábla. Itt adhatja meg a paraméteradatokat.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ütemezés csatolása egy Runbookhoz a PowerShell használatával

A [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) parancsmag gal a schedule összekapcsolásához. A Parameters paraméter segítségével megadhatja a runbook paramétereinek értékét. A paraméterértékek megadásáról a [Runbook indítása az Azure Automationben](../automation-starting-a-runbook.md)című témakörben talál további információt.
A következő példa bemutatja, hogyan csatolhat egy ütemezést egy runbookhoz egy Azure Resource Manager-parancsmag paraméterekhasználatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Runbookok ütemezése a gyakoribb futtatáshoz

Az Azure Automation ütemezése legritkábban egy óra között konfigurálható. Ha ennél gyakrabban kell ütemezni az ütemezéseket, két lehetőség közül választhat:

* Hozzon létre egy [webhookot](../automation-webhooks.md) a runbookhoz, és használja az [Azure Logic Apps-t](../../logic-apps/logic-apps-overview.md) a webhook hívásához. Az Azure Logic Apps részletesebb részletességet biztosít az ütemezés meghatározásakor.

* Hozzon létre négy ütemezést, amelyek óránként 15 percen belül kezdődnek. Ez a forgatókönyv lehetővé teszi, hogy a runbook 15 percenként fusson a különböző ütemezések.

## <a name="disabling-a-schedule"></a>Ütemezés letiltása

Ha letiltja az ütemezést, a hozzá kapcsolt runbookok már nem futnak az adott ütemezés szerint. Manuálisan letilthatja az ütemezést, vagy beállíthat egy lejárati időt a gyakorisággal rendelkező ütemezésekhez, amikor létrehozza őket. A lejárati idő elérése után az ütemezés le van tiltva.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Ütemezés letiltása az Azure Portalról

1. Az Automation-fiókban válassza a **Megosztott erőforrások csoport** **Ütemezések** lehetőséget.
2. Kattintson az ütemezés nevére a részletek ablaktábla megnyitásához.
3. **A Módosítás engedélyezve** **nem**értékre.

> [!NOTE]
> Ha le szeretne tiltani egy olyan ütemezést, amelynek a kezdési időpontja a múltban volt, a mentés előtt a kezdési dátumot egy jövőbeli időpontra kell módosítania.

### <a name="disable-a-schedule-with-powershell"></a>Ütemezés letiltása a PowerShell használatával

A [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) parancsmag segítségével módosíthatja egy meglévő ütemezés tulajdonságait. Az ütemezés letiltásához adja `IsEnabled` meg a Hamis értéket a paraméterhez.

A következő példa bemutatja, hogyan tilthatja le egy runbook ütemezését egy Azure Resource Manager-parancsmag használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* A runbookok azure Automationben való ismerkedése: [Runbook indítása az Azure Automationben című témakörben.](../automation-starting-a-runbook.md)