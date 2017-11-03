---
title: "Azure Automation ütemezései segítségével |} Microsoft Docs"
description: "Az automatizálási ütemezések használatával ütemezhető a runbookok az Azure Automationben automatikus indításra. Ismerteti, hogyan létrehozásához és kezeléséhez az ütemezés szerint, így képes automatikusan elindít egy runbookot egy adott időpontban vagy egy ismétlődő ütemezés szerint."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 446dc79202938ee96cbb090345d9c191060afe76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Runbook ütemezése az Azure Automationben
A megadott időben elindítani Azure Automation forgatókönyv ütemezése, csatolható egy vagy több ütemezés. Ütemezés beállítható úgy, hogy a runbookok a klasszikus Azure portálon, és az Azure-portálon a runbookok egyszeri és egy ismétlődés óránkénti futtatási vagy napi ütemezés, ütemezhető őket heti, havi, a hét meghatározott napjain vagy a hónap napjait, vagy a hónap adott napja.  Egy runbook több ütemezéssel is lehet társítani, és egy ütemezés szerint lehet kapcsolni több runbook.

> [!NOTE]
> Ütemezés jelenleg nem támogatják az Azure Automation DSC-konfigurációk.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-parancsmagjai
A következő táblázatban található parancsmagokkal létrehozása és kezelése a Windows PowerShell segítségével az Azure Automationben ütemezések segítségével. Részét képezi a [Azure PowerShell modul](/powershell/azure/overview).

| Parancsmagok | Leírás |
|:--- |:--- |
| **Az Azure Resource Manager parancsmagok** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Ütemezés lekérése. |
| [Új AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Létrehoz egy új ütemezést. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Eltávolítja az ütemezés szerint. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Meglévő ütemezés tulajdonságainak beállítása. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Lekéri a runbookok ütemezett. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Az ütemezés szerint egy runbook társítja. |
| [AzureRmAutomationScheduledRunbook regisztrációjának törlése](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Egy runbook ütemezés dissociates. |
| **Azure szolgáltatásfelügyelet-parancsmagokkal** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Ütemezés lekérése. |
| [Új AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Létrehoz egy új ütemezést. |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Eltávolítja az ütemezés szerint. |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Meglévő ütemezés tulajdonságainak beállítása. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Lekéri a runbookok ütemezett. |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Az ütemezés szerint egy runbook társítja. |
| [AzureAutomationScheduledRunbook regisztrációjának törlése](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Egy runbook ütemezés dissociates. |

## <a name="creating-a-schedule"></a>Ütemezés létrehozása
A runbookok új ütemtervet hozhat létre az Azure portálon, a klasszikus portálon, vagy a Windows PowerShell használatával. Új ütemezés létrehozására, ha egy runbook egy ütemezést az Azure klasszikus vagy az Azure portál használatával is rendelkezik.

> [!NOTE]
> Azure Automation szolgáltatásbeli használja a legújabb modulok az Automation-fiók egy új ütemezett feladat futtatásakor.  A runbookok és a folyamatok automatizálásához azok érintő elkerülése először tesztelje a runbookokat, amely rendelkezik ütemezések kapcsolódik egy tesztelési dedikált Automation-fiók.  A teszt ellenőrzi a ütemezett runbookok továbbra is megfelelően működjenek, és ha nem, akkor további hibaelhárítása és a frissített runbook-verzió üzemi áttelepítése előtt szükséges változtatások alkalmazásához.  
>  Az Automation-fiók automatikusan beolvasni modulok új verzióit csak frissítette azokat manuálisan kiválasztásával a [frissítés Azure modulok](automation-update-azure-modules.md) parancsát a **modulok** . 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Új ütemezés létrehozása az Azure-portálon
1. Válassza ki az Azure portálon, az automation-fiók **ütemezések** szakaszban **megosztott erőforrások** a bal oldalon. 
2. Kattintson a **ütemezés hozzáadása** az oldal tetején.
4. A a **új ütemezés** panelen adjon meg egy **neve** és opcionálisan egy **leírás** az új ütemezés.
5. Válassza ki, hogy az ütemezés egy alkalommal fut, vagy feladatról ütemezés kiválasztásával **egyszer** vagy **ismétlődési**.  Választásakor **egyszer** adjon meg egy **kezdési időpont** majd **létrehozása**.  Ha **ismétlődési**, adja meg egy **kezdési időpont** és a gyakoriság, milyen gyakran szeretné a runbook ismételje meg a-az **óra**, **nap**, **hét**, vagy **hónap**.  Ha **hét** vagy **hónap** a legördülő listából a **ismétlődési beállítást** a panelen és kiválasztáskor, megjelenik a **ismétlődési beállítást** ablaktábla számára jelenik meg, és kiválaszthatja a hét napja, ha a kiválasztott **hét**.  Ha a kiválasztott **hónap**, szerint is választhat **létrehozását** vagy a naptáron a hónap adott napjaira és végezetül szeretné futtatni a hónap utolsó napján, vagy nem, és kattintson a **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Új ütemezés létrehozása a klasszikus Azure portálon
1. A klasszikus Azure portálon válassza ki az Automation, és válassza az Automation-fiók nevét.
2. Válassza ki a **eszközök** fülre.
3. Az ablak alján kattintson **beállítás hozzáadása**.
4. Kattintson a **ütemezés hozzáadása**.
5. Adjon meg egy **neve** és opcionálisan egy **leírás** az új ütemezés. Az ütemezés futtatható **egyszer**, **óránkénti**, **napi**, **heti**, vagy **havi**.
6. Adjon meg egy **kezdete** és egyéb beállítások kiválasztott ütemezéstípustól függően.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Új ütemezés létrehozása a Windows PowerShell használatával
Használhatja a [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) parancsmag új ütemezés létrehozása az Azure Automationben klasszikus runbookok vagy [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmaggal a runbookok az Azure portálon. Meg kell adnia az ütemezés és a gyakoriság fusson kezdési idejét.

Az alábbi Példaparancsok szemléltetik a 15. és az Azure Resource Manager parancsmagjával havonta 30 ütemezés létrehozása.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Az alábbi Példaparancsok szemléltetik hozzon létre egy új ütemezést, amely a minden nap 3:30 PM 2015. január 20 kezdődően az Azure szolgáltatásfelügyelet parancsmagjával.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Ütemezés összekapcsolása runbookkal
Egy runbook több ütemezéssel is lehet társítani, és egy ütemezés szerint lehet kapcsolni több runbook. Ha a runbook paraméterekkel rendelkezik, majd is értékeket ad meg a számukra. Adjon meg értéket minden kötelező paraméterhez, és előfordulhat, hogy adjon meg értékeket a választható paramétereket.  Ezek az értékek használt minden alkalommal, amikor a runbook az ütemezés szerint elindult.  Ugyanaz a runbook egy másik ütemezés csatolja, és adjon meg másik paraméterértékeket.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Az ütemezés összekapcsolása runbookkal a az Azure-portálon
1. Válassza ki az Azure portálon, az automation-fiók **Runbookok** szakaszban **folyamat** a bal oldalon.
2. Kattintson az ütemezni kívánt runbook nevére.
3. Ha a runbook jelenleg nem kapcsolódik egy ütemezést, majd a rendszer felajánlja létrehozhat egy új ütemezést, vagy meglévő ütemezés mutató hivatkozást.  
4. Ha a runbook paraméterekkel rendelkezik, válassza a beállítás **(alapértelmezett: Azure) futtatási beállítások módosítása** és a **paraméterek** ablaktábla oszlik, ahol megadhatja a ennek megfelelően.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Az ütemezés összekapcsolása runbookkal a klasszikus Azure portálon
1. A klasszikus Azure portálon, válassza ki a **Automation** és kattintson az Automation-fiók nevét.
2. Válassza ki a **Runbookok** fülre.
3. Kattintson az ütemezni kívánt runbook nevére.
4. Kattintson a **ütemezés** fülre.
5. Ha a runbook jelenleg nem kapcsolódik egy ütemezéshez, akkor a rendszer felajánlja a lehetőséget **hivatkozásra egy új ütemezést** vagy **meglévő ütemezés mutató hivatkozás**.  Ha a runbook jelenleg hozzá van kapcsolva egy ütemezést, kattintson a **hivatkozás** ezek a beállítások eléréséhez az ablak alján.
6. Ha a runbook paraméterekkel rendelkezik, ezek értékeinek megadására kéri.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Ütemezés összekapcsolása runbookkal a Windows PowerShell
Használhatja a [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) összekapcsolhat egy ütemezést egy klasszikus runbookhoz vagy [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) parancsmaggal a runbookok az Azure portálon.  A paraméterek paraméterrel megadhatja a gyermekrunbook paramétereinek értékeit. Lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) a paraméterértékek meghatározásáról a további információt.

A következő mintaparancsok bemutatják, hogyan kapcsolhat össze egy ütemezést egy runbookhoz, az Azure Resource Manager parancsmaggal paraméterekkel.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
A következő mintaparancsok bemutatják, miként kapcsolhat össze egy ütemezést egy Azure szolgáltatásfelügyeleti parancsmaggal paraméterekkel.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Ütemezés letiltása
Ha letilt egy ütemezést, minden hozzá kapcsolt forgatókönyvből azt már nem adott ütemezés szerint futtatja. Manuálisan ütemezésének letiltása, vagy az ütemezések gyakorisággal lejárati idő beállítása a létrehozott. A lejárati idő elérésekor a ütemterv le lett tiltva.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure-portálról ütemezésének letiltása
1. Válassza ki az Azure portálon, az Automation-fiók **ütemezések** szakaszban **megosztott erőforrások** a bal oldalon.
2. Kattintson a nevére, nyissa meg a részleteket tartalmazó ablaktáblán a kívánt ütemezést.
3. Változás **engedélyezett** való **nem**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>A klasszikus Azure portálon ütemezésének letiltása
Az ütemezés részleteit megjelenítő oldalon az ütemezés a klasszikus Azure portálon ütemezés letilthatja.

1. A klasszikus Azure portálon válassza ki az Automation, és kattintson az Automation-fiók nevét.
2. Válassza az eszközök lapot.
3. Kattintson a nevére, nyissa meg annak információs lapját a kívánt ütemezést.
4. Változás **engedélyezett** való **nem**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>A Windows PowerShell-lel ütemezésének letiltása
Használhatja a [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) parancsmag klasszikus runbook meglévő ütemezés tulajdonságainak módosításához vagy [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) parancsmaggal a runbookok az Azure portálon. Az ütemezés letiltásához adja meg a **hamis** a a **IsEnabled** paraméter.

Az alábbi Példaparancsok szemléltetik egy runbook az Azure Resource Manager parancsmagjával ütemezésének letiltása.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Az alábbi Példaparancsok szemléltetik az Azure szolgáltatásfelügyelet parancsmaggal ütemezésének letiltása.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Következő lépések
* Ismerkedés az Azure Automation runbookjai, lásd: [Runbook elindítása az Azure Automationben](automation-starting-a-runbook.md) 

