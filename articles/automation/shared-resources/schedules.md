---
title: Ütemtervek kezelése Azure Automation
description: Az Automation-ütemtervek segítségével ütemezhet runbookok az Azure Automation az automatikus indításhoz. Ismerteti, hogyan lehet ütemtervet létrehozni és felügyelni, hogy automatikusan elindítson egy runbook egy adott időpontban vagy ismétlődő ütemterv szerint.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97dc7cb90f5ffc577a007c23074a0e8a75788fab
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114020"
---
# <a name="manage-schedules-in-azure-automation"></a>Ütemtervek kezelése Azure Automation

Ahhoz, hogy egy runbook egy adott időpontban kezdődjön Azure Automation, egy vagy több ütemtervhez kell csatolni. Az ütemterv beállítható úgy, hogy egyszer, vagy a Azure Portalban lévő runbookok óránként vagy napi rendszerességgel történjen. Azt is megteheti, hogy hetente, havonta vagy a hónap adott napjain, vagy a hónap adott napján van-e beütemezhetve. Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat.

> [!NOTE]
> Az ütemtervek jelenleg nem támogatják Azure Automation DSC-konfigurációkat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok

A következő táblázatban található parancsmagok a PowerShell-lel Azure Automation-ben történő létrehozásához és kezeléséhez használatosak. A [Azure PowerShell modul](/powershell/azure/overview)részeként szállítanak.

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Lekéri az ütemtervet. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Ütemezett runbookok beolvasása. |
| [Új – AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Új ütemtervet hoz létre. |
| [Regisztráció – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook társít egy ütemtervhez. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Egy ütemterv eltávolítása. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Egy meglévő ütemterv tulajdonságainak beállítása. |
| [Regisztráció törlése – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Egy runbook leválasztása az ütemtervből. |

## <a name="creating-a-schedule"></a>Ütemterv létrehozása

A runbookok új ütemtervet hozhat létre a Azure Portal vagy a PowerShell használatával.

> [!NOTE]
> A Azure Automation az Automation-fiók legújabb moduljait használja új ütemezett feladatok futtatásakor.  Ha el szeretné kerülni a runbookok és az általuk automatizálható folyamatok hatását, először tesztelje azokat a runbookok, amelyeken a társított ütemtervek egy tesztelésre kijelölt Automation-fiókkal rendelkeznek.  Ez ellenőrzi, hogy az ütemezett runbookok továbbra is megfelelően működik-e, és ha nem, akkor a frissített runbook verziójának éles környezetbe történő áttelepítése előtt továbbra is elháríthatja és alkalmazhatja a szükséges módosításokat.
> Az Automation-fiókja nem kapja meg automatikusan a modulok új verzióit, hacsak nem frissíti őket manuálisan az [Azure-modulok frissítése](../automation-update-azure-modules.md) lehetőség kiválasztásával a **modulokból**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Új ütemterv létrehozása a Azure Portal

1. A Azure Portal az Automation-fiókjából válassza a bal oldalon található **megosztott erőforrások** szakaszban az **ütemtervek** lehetőséget.
2. Kattintson a lap tetején az **ütemterv hozzáadása** lehetőségre.
3. Az új ütemterv panelen adjon meg egy nevet és opcionálisan egy leírást az új ütemtervhez.
4. Válassza ki, hogy az ütemterv **egyszer** fut-e, vagy egy **ismétlődő**ütemterven. Ha **egyszer**van kiválasztva, adjon meg egy kezdési időpontot, majd kattintson a **Létrehozás**gombra. Ha az **ismétlődő**lehetőséget választja, adjon meg egy kezdési időpontot. Az **ismétlődéshez**válassza ki, hogy milyen gyakran szeretné megismételni a runbook az óra, nap, hét vagy hónap alapján.
    1. Ha a **hét**lehetőséget választja, a hét napjai láthatók. Válasszon annyi napot, amennyit csak szeretne. Az ütemterv első futtatása a kezdési időpont után kiválasztott első napon fog történni. Ha például hétvégi menetrendet szeretne választani, válassza a szombat és a vasárnap lehetőséget. 
    
       ![Hétvégi ismétlődő ütemterv beállítása](../media/schedules/week-end-weekly-recurrence.png)

    2. Ha a **hónap**lehetőséget választja, különböző beállításokat kap. A **havi előfordulások** beállításnál válassza a **hónap napja** vagy a **hét napja**lehetőséget. Ha a **hónap napjait**választja, megjelenik egy naptár, amely lehetővé teszi, hogy a kívánt számú napot válassza ki. Ha olyan dátumot választ, mint az aktuális hónapban nem előforduló 31, az ütemterv nem fog futni. Ha azt szeretné, hogy az ütemezett futtatás az elmúlt nap folyamán fusson, válassza az **Igen** lehetőséget a **hónap utolsó napján**. Ha a **hét napja**lehetőséget választja, az **Ismétlődés minden** beállítás megjelenik. Válassza az **első**, a **második**, a **harmadik**, a **negyedik**vagy a **utolsó**lehetőséget. Végül válasszon ki egy napot a megismétléshez.

       ![Havi ütemterv a hónap első, tizenötödik és utolsó napján](../media/schedules/monthly-first-fifteenth-last.png)

5. Ha elkészült, kattintson a **Létrehozás**gombra.

### <a name="create-a-new-schedule-with-powershell"></a>Új ütemterv létrehozása a PowerShell-lel

Ütemtervek létrehozásához használja a [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) parancsmagot. Megadhatja az ütemterv kezdési idejét és a futtatni kívánt gyakoriságot. Az alábbi példák bemutatják, hogyan hozhat létre számos különböző ütemterv-forgatókönyvet.

#### <a name="create-a-one-time-schedule"></a>Egyszeri ütemterv létrehozása

A következő minta-parancsok egy egyszeri ütemtervet hoznak létre.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Ismétlődő ütemterv létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre olyan ismétlődő ütemezést, amely minden nap 1 órakor fut egy évig.

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

Az alábbi példák azt mutatják be, hogyan hozhat létre hetente csak hétvégeken futó ütemtervet.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Ismétlődő ütemterv létrehozása a hónap első, 15 és utolsó napjaihoz

Az alábbi példa bemutatja, hogyan hozhat létre olyan ismétlődő ütemtervet, amely egy hónap 1., 15. és utolsó napján fut.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemterv összekapcsolása egy runbook

Egy runbook több ütemezéssel is összekapcsolható, és egy ütemezéshez több runbook is kapcsolódhat. Ha egy runbook paraméterekkel rendelkezik, akkor megadhatja az értékeket. Meg kell adnia a kötelező paraméterek értékeit, és a választható paraméterek értékét is megadhatja. Ezeket az értékeket a rendszer minden alkalommal felhasználja, amikor ez az ütemterv elindítja a runbook. Ugyanezt a runbook egy másik ütemtervhez is csatolhatja, és különböző paramétereket adhat meg.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ütemterv összekapcsolása egy runbook a Azure Portal

1. A Azure Portal Automation-fiókjából válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nincs ütemtervhez csatolva, lehetősége van arra, hogy új ütemtervet hozzon létre, vagy egy meglévő ütemtervre hivatkozzon.
4. Ha a runbook paraméterekkel rendelkezik, válassza a **Futtatási beállítások módosítása (alapértelmezett: Azure)** lehetőséget, és megjelenik a paraméterek panel. Itt megadhatja a paraméterre vonatkozó információkat.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ütemterv összekapcsolása runbook a PowerShell-lel

Az ütemterv összekapcsolásához használja a [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) parancsmagot. A Parameters paraméter segítségével megadhatja a runbook paramétereinek értékét. A paraméterek értékeinek megadásával kapcsolatos további információkért lásd: [Runbook elindítása Azure Automationban](../automation-starting-a-runbook.md).
Az alábbi példa bemutatja, hogyan kapcsolhat össze egy ütemtervet egy runbook egy Azure Resource Manager parancsmaggal paraméterekkel.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Runbookok ütemezése gyakrabban

A leggyakoribb intervallum egy óra Azure Automation konfigurálható. Ha az ütemtervek végrehajtásához gyakrabban kell végrehajtani a műveleteket, akkor két lehetőség közül választhat:

* Hozzon létre egy [webhookot](../automation-webhooks.md) a runbook, és használja a [Azure Logic Appst](../../logic-apps/logic-apps-overview.md) a webhook meghívásához. A Azure Logic Apps az ütemterv meghatározásakor részletesebb részletességet biztosít.

* Hozzon létre négy ütemezett ütemtervet, amelyek mindegyike óránként 15 percen belül elindul. Ez a forgatókönyv lehetővé teszi, hogy a runbook 15 percenként fusson a különböző ütemtervekkel.

## <a name="disabling-a-schedule"></a>Ütemterv letiltása

Ha letilt egy ütemtervet, az ahhoz társított összes runbook már nem fut az adott ütemterven. Manuálisan letilthatja az ütemtervet, vagy megadhat egy lejárati időt az ütemtervekhez a létrehozásuk gyakorisága alapján. A lejárati idő elérésekor az ütemterv le lesz tiltva.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Ütemterv letiltása a Azure Portal

1. Az Automation-fiókban válassza az **ütemtervek** lehetőséget a **megosztott erőforrások**területen.
2. Kattintson az ütemterv nevére a részletek ablaktábla megnyitásához.
3. Az **engedélyezve** érték **nem**értékre vált.

> [!NOTE]
> Ha le szeretné tiltani egy olyan ütemtervet, amely múltbeli kezdési időpontot tartalmaz, akkor a Mentés előtt módosítania kell a kezdési dátumot a jövőbeli időpontra.

### <a name="disable-a-schedule-with-powershell"></a>Ütemterv letiltása a PowerShell-lel

A [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) parancsmag használatával módosíthatja a meglévő ütemtervek tulajdonságait. Az ütemterv letiltásához a paraméternél a `IsEnabled` FALSE értéket kell megadnia.

Az alábbi példa bemutatja, hogyan tilthatja le egy runbook egy Azure Resource Manager parancsmag használatával.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Ütemterv eltávolítása

Ha készen áll az ütemtervek eltávolítására, használhatja a Azure Portal vagy a `Remove-AzureRmAutomationSchedule` parancsmagot is. Ne feledje, hogy csak az előző szakaszban leírtak szerint letiltott ütemtervet lehet eltávolítani.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ütemterv eltávolítása a Azure Portal használatával

1. Az Automation-fiókban válassza az **ütemtervek** lehetőséget a **megosztott erőforrások**területen.
2. Kattintson az ütemterv nevére a részletek ablaktábla megnyitásához.
3. Kattintson a **Törlés** gombra.

### <a name="remove-a-schedule-with-powershell"></a>Ütemterv eltávolítása a PowerShell-lel

A [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) parancsmag használatával törölheti a meglévőket. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzureRmAutomationSchedule -AutomationAccountName $automationAccountName ` -Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* A Azure Automation runbookok megkezdéséhez tekintse meg a következő témakört: [Runbook elindítása Azure Automationban](../automation-starting-a-runbook.md).