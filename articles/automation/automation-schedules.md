---
title: Az Azure Automationben ütemezések
description: Az automatizálási ütemezések runbookok automatikus indításra az Azure automationben ütemezésére szolgálnak. Ismerteti, hogyan hozhat létre és kezelhet az ütemezés szerint, így képes automatikusan elindít egy runbookot egy adott időpontban vagy ismétlődő ütemezés szerint.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3d8492d2a8982c9c85bfc91867f7eb6c2da04e58
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294764"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Runbook ütemezése az Azure Automationben

Az Azure Automation és a egy megadott időpontban el egy runbook ütemezése, kapcsolja össze egy vagy több ütemezés. Ütemezés beállítható úgy, hogy futtassa egyszer vagy egy került az óránkénti vagy napi ütemezés runbookok az Azure Portalon. Is ütemezheti őket hetente, havonta, a hét meghatározott napjain vagy a hónap napjait, vagy egy adott nap a hónapban. Egy runbook több ütemezéssel kapcsolható, és egy ütemezés rendelkezhet több runbook hozzá kell kapcsolni.

> [!NOTE]
> Ütemezések jelenleg nem támogatja az Azure Automation DSC-konfigurációk.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagok

A következő táblázatban található parancsmagokkal hozhat létre és kezelhet az ütemezéseket a Windows PowerShell-lel az Azure Automationben szolgálnak. Részét képezi a [Azure PowerShell-modul](/powershell/azure/overview).

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

Létrehozhat egy új ütemezést a runbookok az Azure Portalon vagy a Windows PowerShell-lel.

> [!NOTE]
> Az Azure Automation használja a legújabb modulokat az Automation-fiók egy új ütemezett feladatot a futása során.  A runbookok és a folyamatok automatizálása azok érintő elkerüléséhez először tesztelje az Automation-fiók tesztelési dedikált csatolt ütemezéssel rendelkező runbookokat.  Ez igazolja az ütemezett runbookok továbbra is megfelelően működjenek, és ha nem, akkor további, és hibaelhárítást a frissített runbook-verzió éles-ba való migrálás előtt meg kell adni a módosítások alkalmazása.
> Az Automation-fiók nem automatikusan kap új verzióit modulok, kivéve, ha frissítette azokat manuálisan kiválasztásával a [frissítés az Azure-modulok](automation-update-azure-modules.md) parancsát a **modulok**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure Portalon

1. Válassza ki az Azure Portalon, az automation-fiók **ütemezések** a szakaszában **megosztott erőforrások** a bal oldalon.
1. Kattintson a **ütemezés hozzáadása** az oldal tetején.
1. Az a **új ütemezés** panelen adjon meg egy **neve** és opcionálisan egy **leírás** az új ütemezés számára.
1. Válassza ki, hogy az ütemezés egy alkalommal fut le, vagy ismétlődő ütemezés szerint kiválasztásával **egyszer** vagy **ismétlődő**. Ha **egyszer** adjon meg egy **kezdési idő**, és kattintson a **létrehozás**. Ha **ismétlődő**, adjon meg egy **kezdési idő** és a **Ismétlődés minden**, válassza ki a gyakoriságot, hogy milyen gyakran szeretné a runbook ismételje meg – a **óra**, **nap**, **hét**, vagy **hónap**.
    1. Ha **hét**, választhat a hét azon napjai, listájának rendelkezésre állnak. Válassza ki a kívánt számú nappal. Az első futtatásakor az ütemezés a kiválasztott a kezdő időpont utáni első napján fog történni.
    2. Ha **hónap**, akkor különböző lehetőségeket vannak megadva. Az a **havi occurrances** lehetőséget, válassza ki vagy **hónap napjai** vagy **napok**. Ha úgy dönt, **hónap napjai** naptárban látható, amely lehetővé teszi, hogy válassza ki a kívánt számú nappal. Ha például nem következik be, az aktuális hónap 31 dátumot, az ütemezés nem fog futni. Ha azt szeretné, hogy az utolsó nappal futtatni az ütemezést, válassza a **Igen** alatt **Futtatás a hónap utolsó napján**. Ha úgy dönt, **napok**, a **Ismétlődés minden** lehetőség jelenik meg. Válasszon **első**, **második**, **harmadik**, **negyedik**, vagy **utolsó**. Végül válassza ki a naponta, vagy ismételje meg a.
1. Ezzel a módszerrel kattintson **létrehozás**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Új ütemezés létrehozása a Windows PowerShell-lel

Használja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag ütemezés létrehozásához. Megadhatja a kezdési időpont az ütemezés és a gyakoriság kell futnia.

A következő mintaparancsok bemutatják, hogyan hozhat létre egy ütemezést a 15. és a egy Azure Resource Manager-parancsmag segítségével havonta 30.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása egy runbook

Egy runbook több ütemezéssel kapcsolható, és egy ütemezés rendelkezhet több runbook hozzá kell kapcsolni. Ha a runbook paraméterekkel rendelkezik, majd megadhat értékeket a számukra. Értékeket kell megadnia minden kötelező paraméterhez, és előfordulhat, hogy adjon meg értékeket a kötelező paramétereket. Ezekkel az értékekkel minden alkalommal, amikor a runbook az ütemezés szerint elindult. Csatolja ugyanazon a runbookon belül egy másik ütemezést, és adja meg a különböző paraméterértékekkel.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Összekapcsolhat egy ütemezést egy runbookhoz, az Azure portal használatával

1. Válassza ki az Azure Portalon, az automation-fiók **Runbookok** a szakaszában **Folyamatautomatizálás** a bal oldalon.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nem kapcsolódik egy ütemezést, akkor létrehozhat egy új ütemezést vagy összekapcsolás meglévő ütemezéssel érhetők el.
4. Ha a runbook paraméterekkel rendelkezik, válassza a beállítást **futtatási beállítások (alapértelmezett: Azure) módosítása** és a **paraméterek** ablaktábla be az adatokat annak megfelelően jelenik meg.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Összekapcsolhat egy ütemezést egy runbookkal a Windows PowerShell-lel

Használhatja a [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) parancsmag összekapcsolhat egy ütemezést. Megadhatja a runbook-paraméterek értékei a paraméterek paraméterrel. További információk a paraméterértékek meghatározásáról: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md).
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

* Hozzon létre egy [webhook](automation-webhooks.md) runbook és használatára vonatkozó [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) a webhook hívása. Az Azure Scheduler biztosít a több részletes granularitási ütemezés meghatározásakor.

* Hozzon létre négy ütemezések eltérés körülbelül óránként futó 15 percen belül minden indítása. Ebben a forgatókönyvben lehetővé teszi, hogy a runbook futtatása 15 percenként az eltérő ütemezésekkel való.

## <a name="disabling-a-schedule"></a>Ütemezés letiltása

Ha letilt egy ütemezést, bármely olyan runbookhoz, hozzá kell kapcsolni már nem adott ütemezés szerint futtatja. Manuálisan ütemezés letilthatja vagy gyakorisággal ütemezések esetében lejárati idő beállítása, azok létrehozásakor. Ha eléri a lejárati időt, az ütemterv le lett tiltva.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Az Azure Portalról ütemezésének letiltása

1. Válassza ki az Azure Portalon, az Automation-fiók **ütemezések** a szakaszában **megosztott erőforrások** a bal oldalon.
1. Kattintson a jobb oldali ablaktáblában nyissa meg az ütemezés nevét.
1. Változás **engedélyezve** való **nem**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>A Windows PowerShell-lel ütemezésének letiltása

Használhatja a [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) parancsmag meglévő ütemezés tulajdonságainak módosításához. Tiltsa le az ütemezést, adja meg a **hamis** a a **IsEnabled** paraméter.

Az alábbi Példaparancsok szemléltetik egy runbook egy Azure Resource Manager-parancsmaggal ütemezésének letiltása.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>További lépések

* Ismerkedés az Azure Automation runbookjai, lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md)
