---
title: Azure Automation ütemezései segítségével
description: Az automatizálási ütemezések használatával ütemezhető a runbookok az Azure Automationben automatikus indításra. Ismerteti, hogyan létrehozásához és kezeléséhez az ütemezés szerint, így képes automatikusan elindít egy runbookot egy adott időpontban vagy egy ismétlődő ütemezés szerint.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c7bd4d4249d304ee7c1df4ae4b8fc0af476b99c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Runbook ütemezése az Azure Automationben

A megadott időben elindítani Azure Automation forgatókönyv ütemezése, csatolható egy vagy több ütemezés. Ütemezés beállítható úgy, hogy az Azure-portálon a runbookok egyszeri és egy ismétlődés óránkénti futtatási vagy napi ütemezés. Beütemezhet őket heti, havi, a hét meghatározott napjain vagy a hónap napjait, vagy a hónap adott napja. Egy runbook több ütemezéssel is lehet társítani, és egy ütemezés szerint lehet kapcsolni több runbook.

> [!NOTE]
> Ütemezés jelenleg nem támogatják az Azure Automation DSC-konfigurációk.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagjai

A következő táblázatban található parancsmagokkal létrehozása és kezelése a Windows PowerShell segítségével az Azure Automationben ütemezések segítségével. Részét képezi a [Azure PowerShell modul](/powershell/azure/overview).

| Parancsmagok | Leírás |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Ütemezés lekérése. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Létrehoz egy új ütemezést. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Eltávolítja az ütemezés szerint. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Meglévő ütemezés tulajdonságainak beállítása. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Lekéri a runbookok ütemezett. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Az ütemezés szerint egy runbook társítja. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Egy runbook ütemezés dissociates. |

## <a name="creating-a-schedule"></a>Ütemezés létrehozása

A runbookok új ütemtervet hozhat létre, az Azure portálon vagy a Windows PowerShell használatával.

> [!NOTE]
> Azure Automation szolgáltatásbeli használja a legújabb modulok az Automation-fiók egy új ütemezett feladat futtatásakor.  A runbookok és a folyamatok automatizálásához azok érintő elkerülése először tesztelje a runbookokat, amely rendelkezik ütemezések kapcsolódik egy tesztelési dedikált Automation-fiók.  A teszt ellenőrzi a ütemezett runbookok továbbra is megfelelően működjenek, és ha nem, akkor további hibaelhárítása és a frissített runbook-verzió üzemi áttelepítése előtt szükséges változtatások alkalmazásához.
> Az Automation-fiók automatikusan beolvasni modulok új verzióit csak frissítette azokat manuálisan kiválasztásával a [frissítés Azure modulok](automation-update-azure-modules.md) parancsát a **modulok**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure-portálon

1. Válassza ki az Azure portálon, az automation-fiók **ütemezések** szakaszban **megosztott erőforrások** a bal oldalon.
1. Kattintson a **ütemezés hozzáadása** az oldal tetején.
1. A a **új ütemezés** panelen adjon meg egy **neve** és opcionálisan egy **leírás** az új ütemezés.
1. Válassza ki, hogy az ütemezés egy alkalommal fut, vagy feladatról ütemezés kiválasztásával **egyszer** vagy **ismétlődési**. Ha **egyszer** adja meg egy **kezdési időpont**, és kattintson a **létrehozása**. Ha **ismétlődési**, adja meg egy **kezdési időpont** és a gyakoriság, milyen gyakran szeretné a runbook ismételje meg a-az **óra**, **nap**, **hét**, vagy **hónap**. Ha **hét** vagy **hónap** a legördülő listából a **ismétlődési beállítást** a panelen és kiválasztáskor, megjelenik a **ismétlődési beállítást** ablaktábla számára jelenik meg, és kiválaszthatja a hét napja, ha a kiválasztott **hét**. Ha a kiválasztott **hónap**, szerint is választhat **létrehozását** vagy a naptáron a hónap adott napjaira és végezetül szeretné futtatni a hónap utolsó napján, vagy nem, és kattintson a **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Új ütemezés létrehozása a Windows PowerShell használatával

Használja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmaggal hozhat létre ütemezés. Az ütemezés és a gyakoriság fusson a kezdési idő megadása.

Az alábbi Példaparancsok szemléltetik a 15. és az Azure Resource Manager parancsmagjával havonta 30 ütemezés létrehozása.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása runbookkal

Egy runbook több ütemezéssel is lehet társítani, és egy ütemezés szerint lehet kapcsolni több runbook. Ha a runbook paraméterekkel rendelkezik, majd is értékeket ad meg a számukra. Adjon meg értéket minden kötelező paraméterhez, és előfordulhat, hogy adjon meg értékeket a választható paramétereket. Ezek az értékek használt minden alkalommal, amikor a runbook az ütemezés szerint elindult. Ugyanaz a runbook egy másik ütemezés csatolja, és adjon meg másik paraméterértékeket.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Az ütemezés összekapcsolása runbookkal a az Azure-portálon

1. Válassza ki az Azure portálon, az automation-fiók **Runbookok** szakaszban **folyamat** a bal oldalon.
1. Kattintson az ütemezni kívánt runbook nevére.
1. Ha a runbook jelenleg nem kapcsolódik egy ütemezést, majd a rendszer felajánlja létrehozhat egy új ütemezést, vagy meglévő ütemezés mutató hivatkozást.
1. Ha a runbook paraméterekkel rendelkezik, válassza a beállítás **(alapértelmezett: Azure) futtatási beállítások módosítása** és a **paraméterek** ablaktábla oszlik, ahol megadhatja a ennek megfelelően.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Ütemezés összekapcsolása runbookkal a Windows PowerShell

Használhatja a [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) parancsmag összekapcsolhat egy ütemezést. A paraméterek paraméterrel megadhatja a gyermekrunbook paramétereinek értékeit. További információk a paraméterértékek meghatározásáról: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).
A következő mintaparancsok bemutatják, hogyan kapcsolhat össze egy ütemezést egy runbookhoz, az Azure Resource Manager parancsmaggal paraméterekkel.

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

A leggyakoribb egy Azure Automation ütemezési konfigurálhatja a program óránként. Ütemezések gyakrabban, amelyek végrehajtásához írja elő, ha van két lehetőség közül választhat:

* Hozzon létre egy [webhook](automation-webhooks.md) runbook és használatára vonatkozó [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) a webhook hívni. Azure Schedulerrel még granularitási biztosít, ütemezés meghatározásakor.

* Hozzon létre négy ütemezéseket, minden más futó óránként egyszer 15 percen belül minden indítása. Ebben a forgatókönyvben lehetővé teszi, hogy a runbook futtatását 15 percenként a különböző ütemezéssel.

## <a name="disabling-a-schedule"></a>Ütemezés letiltása

Ha letilt egy ütemezést, minden olyan forgatókönyvben kapcsolni már nem adott ütemezés szerint futtatja. Manuálisan ütemezésének letiltása, vagy az ütemezések gyakorisággal lejárati idő beállítása a létrehozott. A lejárati idő elérésekor a ütemterv le lett tiltva.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure-portálról ütemezésének letiltása

1. Válassza ki az Azure portálon, az Automation-fiók **ütemezések** szakaszban **megosztott erőforrások** a bal oldalon.
1. Kattintson a nevére, nyissa meg a részleteket tartalmazó ablaktáblán a kívánt ütemezést.
1. Változás **engedélyezett** való **nem**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>A Windows PowerShell-lel ütemezésének letiltása

Használhatja a [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) parancsmag meglévő ütemezés tulajdonságainak módosításához. Az ütemezés letiltásához adja meg a **hamis** a a **IsEnabled** paraméter.

Az alábbi Példaparancsok szemléltetik egy runbook az Azure Resource Manager parancsmagjával ütemezésének letiltása.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* Ismerkedés az Azure Automation runbookjai, lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)