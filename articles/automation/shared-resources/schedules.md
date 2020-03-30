---
title: Ütemezések az Azure Automationben
description: Az automatizálási ütemezések segítségével ütemezheti a runbookok az Azure Automation automatikus indításához. Ez a témakör azt ismerteti, hogy miként hozhat létre és kezelhet ütemezést, hogy egy adott időpontban vagy ismétlődő ütemezés szerint automatikusan elindíthatja a runbookot.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252661"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Runbook ütemezése az Azure Automationben

Ha egy runbookot az Azure Automationben egy adott időpontban szeretne elindítani, egy vagy több ütemezéshez csatolja. Az ütemezés beállítható úgy, hogy egyszer vagy egy ismétlődő óránkénti vagy napi ütemezésszerint fusson az Azure Portalon. Ütemezheti őket heti, havi, a hét adott napjaira, a hónap napjaira vagy a hónap egy adott napjára. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Az ütemezések jelenleg nem támogatják az Azure Automation DSC-konfigurációkat.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok

Az alábbi táblázatban található parancsmagok ütemezések létrehozásához és kezeléséhez használható az Azure Automation PowerShell használatával. Az [Azure PowerShell-modul](/powershell/azure/overview)részeként szállítanak.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Beolvassa az ütemezést. |
| [Új-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Új ütemezést hoz létre. |
| [Eltávolítás-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Eltávolítja az ütemtervet. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Egy meglévő ütemezés tulajdonságainak beállítása. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Az ütemezett runbookok beolvasása. |
| [Regisztráció-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Runbook ot társít egy ütemezéshez. |
| [Regisztráció megszüntetése az AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |A runbookok ütemezéstől való elhatárolta. |

## <a name="creating-a-schedule"></a>Ütemezés létrehozása

Létrehozhat egy új ütemezést a runbookok hoz létre az Azure Portalon vagy a PowerShell használatával.

> [!NOTE]
> Az Azure Automation az Automation-fiók legújabb moduljait használja egy új ütemezett feladat futtatásakor.  Annak elkerülése érdekében, hogy befolyásolja a runbookok és az általuk automatizált folyamatok, először tesztelje azokat a runbookokat, amelyek összekapcsoltaazták az ütemezéseket egy tesztelésre szánt Automation-fiókkal.  Ez ellenőrzi az ütemezett runbookok továbbra is megfelelően működik, és ha nem, akkor további hibaelhárítási és a szükséges módosításokat, mielőtt a frissített Runbook-verzió éles környezetbe való áttelepítése.
> Az Automation-fiók nem kap automatikusan új modulokat, kivéve, ha manuálisan frissítette őket az [Azure-modulok frissítése](../automation-update-azure-modules.md) lehetőség kiválasztásával a **modulok**közül.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure Portalon

1. Az Azure Portalon az automation-fiókból válassza a Bal oldali **Megosztott erőforrások** szakasz **ütemezése** lehetőséget.
2. Kattintson az **Ütemezés hozzáadása** gombra a lap tetején.
3. Az **Új ütemezés** ablaktáblán írjon be egy **nevet** és tetszés szerint az új ütemezés **leírását.**
4. Adja meg, hogy az ütemezés egy alkalommal vagy ismétlődő ütemezés szerint fut-e az **Egyszer** vagy **az Ismétlődő**lehetőséget választva. Ha a **Egyszer** adja meg a **Kezdés időpontja**lehetőséget, majd kattintson a **Létrehozás gombra.** Ha az Ismétlődő lehetőséget **választja,** adjon meg egy **kezdési időpontot,** és **a Recur minden**esetben adja meg, adja meg, hogy milyen gyakran ismételje meg a runbookot - **óra**, **nap**, **hét**vagy **hónap**szerint.
    1. Ha a **hetet**választja, a hét azon napjainak listáját kapod meg, amelyek közül választhatsz. Jelöljön ki annyi napot, amennyit csak akar. Az ütemezés első futtatása a kezdési időpont után kiválasztott első napon történik. Például a hétvégi ütemezés kiválasztásához válassza **a Szombat** és **vasárnap**lehetőséget .

       ![Hétvégi ismétlődő ütemezés beállítása](../media/schedules/week-end-weekly-recurrence.png)

    2. Ha a **hónapot**választja, különböző lehetőségeket kap. A **Havi előfordulások** beállításhoz válassza a **Hónap napok** vagy **a Hét napjait.** Ha a **Hónap napjai**lehetőséget választja, egy naptár jelenik meg, amely lehetővé teszi, hogy annyi napot válasszon, amennyit csak szeretne. Ha olyan dátumot választ, mint például a 31., amely nem fordul elő az aktuális hónapban, az ütemezés nem fog futni. Ha azt szeretné, hogy az ütemezés az utolsó napon fusson, válassza az **Igen** lehetőséget **a Futtatás a hónap utolsó napján csoportban.** Ha a **Hét napok**lehetőséget választja, a **Recur minden** lehetőség megjelenik. Válassza **az Első,** **a Második,** **a Harmadik,** **a Negyedik**vagy **az Utolsó**lehetőséget. Végül válasszon egy napot, hogy ismételje meg.

       ![Havi ütemezés a hónap első, tizenötödik és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

5. Ha végzett, kattintson a **Létrehozás gombra.**

### <a name="to-create-a-new-schedule-with-powershell"></a>Új ütemezés létrehozása a PowerShell használatával

Ütemezések létrehozásához használja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag. Megadhatja az ütemezés kezdési idejét és a futtatásának gyakoriságát. Az alábbi példák bemutatják, hogyan hozhat létre számos különböző ütemezési forgatókönyvet.

#### <a name="create-a-one-time-schedule"></a>Egyszeri időütemezés létrehozása

A következő mintaparancsok egyetlen időütemezést hoznak létre.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Ismétlődő ütemezés létrehozása

A következő mintaparancsok bemutatják, hogyan hozhat létre ismétlődő ütemezést, amely egy éven át minden nap 1:00 órakor fut.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Heti ismétlődő ütemezés létrehozása

A következő mintaparancsok bemutatják, hogyan hozhat létre heti ütemezést, amely csak hétköznapokon fut.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Heti ismétlődő ütemezés létrehozása hétvégékhez

A következő mintaparancsok bemutatják, hogyan hozhat létre heti ütemezést, amely csak hétvégén fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Ismétlődő ütemezés létrehozása a hónap első, 15.

A következő mintaparancsok bemutatják, hogyan hozhat létre egy ismétlődő ütemezést, amely egy hónap első, 15.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása runbookkal

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha egy runbook paramétereket tartalmaz, akkor értékeket adhat meg számukra. Minden kötelező paraméterhez meg kell adnia az értékeket, és megadhatja a választható paraméterek et. Ezek az értékek minden alkalommal használatosak, amikor a runbookot ez az ütemezés indítja el. Ugyanazt a runbookot csatolhatja egy másik ütemezéshez, és különböző paraméterértékeket adhat meg.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ütemezés összekapcsolása egy runbookhoz az Azure Portalon

1. Az Azure Portalon az automation-fiókból válassza a **Runbookok** lehetőséget a bal oldali **Folyamatautomatizálás** című szakaszban.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nem kapcsolódik egy ütemezéshez, akkor felajánlja a lehetőséget, hogy hozzon létre egy új ütemezést, vagy egy meglévő ütemezésre mutató hivatkozást.
4. Ha a runbook paramétereket tartalmaz, **kiválaszthatja a Futtatási beállítások módosítása (Alapértelmezett:Azure)** lehetőséget, és a **Paraméterek** ablaktábla jelenik meg, ahol megadhatja az adatokat.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Ütemezés csatolása egy Runbookhoz a PowerShell használatával

A [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) parancsmag használatával összeválaszthat egy ütemezést. A Parameters paraméter segítségével megadhatja a runbook paramétereinek értékét. A paraméterértékek megadásáról a [Runbook indítása az Azure Automationben](../automation-starting-a-runbook.md)című témakörben talál további információt.
A következő mintaparancsok bemutatják, hogyan kapcsolhat össze egy ütemezést egy runbookhoz egy Azure Resource Manager-parancsmag paraméterekkel.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>A runbookok gyakoribb ütemezése

Az Azure Automation ütemezése legritkábban egy óra között konfigurálható. Ha ennél gyakrabban kell ütemezni az ütemezéseket, két lehetőség közül választhat:

* Hozzon létre egy [webhookot](../automation-webhooks.md) a runbookhoz, és használja az [Azure Logic Apps-t](../../logic-apps/logic-apps-overview.md) a webhook hívásához. Az Azure Logic Apps részletesebb részletességet biztosít az ütemezés meghatározásakor.

* Hozzon létre négy ütemezést, amelyek óránként 15 percen belül kezdődnek. Ez a forgatókönyv lehetővé teszi, hogy a runbook 15 percenként fusson a különböző ütemezések.

## <a name="disabling-a-schedule"></a>Ütemezés letiltása

Ha letiltja az ütemezést, a hozzá kapcsolt runbookok már nem futnak az adott ütemezés szerint. Manuálisan letilthatja az ütemezést, vagy beállíthat egy lejárati időt a gyakorisággal rendelkező ütemezésekhez, amikor létrehozza őket. A lejárati idő elérése után az ütemezés le van tiltva.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Ütemezés letiltása az Azure Portalról

1. Az Azure Portalon az Automation-fiókból válassza a Bal oldali **Megosztott erőforrások** szakasz **ütemezése** lehetőséget.
2. Kattintson az ütemezés nevére a részletek ablaktábla megnyitásához.
3. **A Módosítás engedélyezve** **nem**értékre.

> [!NOTE]
> Ha le szeretne tiltani egy olyan ütemezést, amelynek a kezdési időpontja a múltban volt, a mentés előtt a kezdési dátumot egy jövőbeli időpontra kell módosítania.

### <a name="to-disable-a-schedule-with-powershell"></a>Ütemezés letiltása a PowerShell használatával

A [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) parancsmag használatával módosíthatja egy meglévő ütemezés tulajdonságait. Az ütemezés letiltásához adja meg a **false** értéket az **IsEnabled** paraméterhez.

A következő mintaparancsok bemutatják, hogyan tilthatja le egy runbook ütemezését egy Azure Resource Manager-parancsmag használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* A runbookok azure Automationben való ismerkedése: [Runbook indítása az Azure Automationben című témakörben](../automation-starting-a-runbook.md)

