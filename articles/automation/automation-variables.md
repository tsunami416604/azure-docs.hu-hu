---
title: "Az Azure Automationben változó eszközök"
description: "Változó eszközök értékeket összes forgatókönyve és az Azure Automation DSC-konfiguráció számára elérhető.  Ez a cikk ismerteti a változók és a szöveges és a grafikus szerzői őket munkavégzés részleteit."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7c36fce380712da6572e9512a05af9c23c4152a2
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="variable-assets-in-azure-automation"></a>Az Azure Automationben változó eszközök

Változó eszközök értékeket elérhető összes forgatókönyve és DSC-konfigurációk az automation-fiókban. Azok létrehozott, módosított, és lekérése az Azure-portálon, a Windows PowerShell és a runbookot vagy a DSC-konfiguráció. Automatizálási változók az alábbiak a következő esetekben lehet hasznos:

- Ossza meg több runbookok vagy a DSC-konfigurációk közötti értéket.

- Ossza meg a DSC-konfiguráció vagy ugyanaz a runbook több feladat közötti értéket.

- A portálon vagy a Windows PowerShell parancssorban forgatókönyvek vagy a DSC-konfigurációk, például a virtuális gép nevét, egy adott erőforráscsoportot, egy AD-tartomány nevét, stb például adott listáját általános konfigurációs elemek készlete által használt érték kezelése.  

Automatizálási változók megmaradnak, így azok továbbra is elérhetők, még akkor is, ha a runbookot vagy a DSC-konfiguráció nem sikerül. Ez egy runbookot, amely ezután történik egy másik, vagy használja a ugyanaz a runbook vagy a DSC-konfiguráció fut, amikor legközelebb által értéket is lehetővé teszi.

Egy változó létrehozásakor megadhatja, hogy tárolt titkosított. Ha titkosított változó, az Azure Automation tárolja biztonságos helyen és az értéke nem lehet beolvasni a [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) parancsmagot tartalmaz, az Azure PowerShell modul részét képezi. Származik, csak úgy lehetséges, hogy egy titkosított értéke lehet beolvasni a **Get-AutomationVariable** tevékenység egy runbookot vagy a DSC-konfiguráció.

>[!NOTE]
>Az Azure Automationben biztonságos eszközök közé tartozik a hitelesítő adatokat, a tanúsítványokat, a kapcsolatok és a titkosított változók. Ezek az eszközök titkosítva, és tárolja az Azure Automationben létrehozott egyedi kulcs segítségével minden egyes automation-fiókhoz. Ezt a kulcsot a Key Vault van tárolva. A kulcs tárolása biztonságos eszköz, mielőtt Key Vault betöltődnek és majd az eszköz titkosításához használt.

## <a name="variable-types"></a>Változó típusa

Az Azure portállal egy változó létrehozásakor meg kell adni egy adatok a legördülő listából, a portál írja be a változó értékének megfelelő vezérlőket tudja megjeleníteni. A változó nem korlátozódik ezt az adattípust, de meg kell adni a változót a Windows PowerShell használatával, ha azt szeretné, hogy más típusú értéket. Ha megad **nincs definiálva**, a változó értékére állítja be **$null**, és meg kell adni az értéket a [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) parancsmag vagy **Set-AutomationVariable** tevékenység. Nem hozható létre, vagy módosítsa az értéket egy változó komplex típus a portálon, de megadhatja a Windows PowerShell használatával bármilyen típusú érték. Összetett típusok vissza, a [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Több érték változóhoz tömb vagy hashtable létrehozásával, és mentse a változó tárolhatja.

A rendelkezésre álló Automation változó típusainak listáját a következők:

* Karakterlánc
* Egész szám
* DateTime
* Logikai
* NULL értékű

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok
A következő táblázatban található parancsmagokkal AzureRM, létrehozását és kezelését az automatizálási hitelesítő eszközök a Windows PowerShell használatával történik. Részét képezi a [AzureRM.Automation modul](/powershell/azure/overview) elérhető Automation-forgatókönyveket és a DSC-konfigurációk.

| Parancsmagok | Leírás |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Egy létező változó értékét kérdezi le.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Új változót hoz létre, és beállítja az értékét.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Eltávolít egy létező változó.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Beállítja egy létező változó értékét.|

## <a name="activities"></a>Tevékenységek
Az alábbi táblázatban a tevékenységek a runbookok és a DSC-konfigurációk hitelesítő adatok eléréséhez használt.

| Tevékenységek | Leírás |
|:---|:---|
|Get-AutomationVariable|Egy létező változó értékét kérdezi le.|
|Set-AutomationVariable|Beállítja egy létező változó értékét.|

> [!NOTE] 
> Kerülendő a változók használata a – Name paraméterében **Get-AutomationVariable** a runbookot vagy a DSC-konfiguráció számára, mivel ez megnehezítheti a runbookok vagy a DSC-konfiguráció és az Automation-változók közti függőségek tervezési időben.

Az alábbi táblázatban a funkciók eléréséhez és lekérni a Python2 runbookban található változók használhatók. 

|Python2 funkciók|Leírás|
|:---|:---|
|automationassets.get_automation_variable|Egy létező változó értékét kérdezi le. |
|automationassets.set_automation_variable|Beállítja egy létező változó értékét. |

> [!NOTE] 
> A Python runbook tetején a "automationassets" modul kell importálnia az eszköz funkciók eléréséhez.

## <a name="creating-a-new-automation-variable"></a>Új automatizálási változó létrehozása

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Új változó létrehozása az Azure portállal

1. Az Automation-fiók, kattintson a **eszközök** csempe majd a a **eszközök** panelen válassza **változók**.
2. Az a **változók** csempe, jelölje be **változó hozzáadása**.
3. Végezze el a beállításokat a a **új változó** panel megnyitásához, és kattintson **létrehozása** az új változó mentéséhez.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Új változó létrehozása a Windows PowerShell használatával

A [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmag új változót hoz létre, és beállítja a kezdeti értékhez. Kérheti le a érték [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Ha az érték egy egyszerű típusú, majd, hogy ugyanolyan típusú adja vissza. Ha egy összetett típus, akkor egy **PSCustomObject** adja vissza.

Az alábbi Példaparancsok szemléltetik egy karakterlánc típusú változó létrehozása, és térjen vissza az értékét.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Az alábbi Példaparancsok szemléltetik egy összetett típus hozzon létre egy változót, és térjen vissza a tulajdonságait. Ebben az esetben a virtuális gépek objektum **Get-AzureRmVm** szolgál.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Egy változó a runbookot vagy a DSC-konfiguráció használata

Használja a **Set-AutomationVariable** automatizálási változó értékét állíthatja be a DSC-konfiguráció, vagy PowerShell-forgatókönyv tevékenység és a **Get-AutomationVariable** ennek lekéréséhez. Ne használja a **Set-AzureRMAutomationVariable** vagy **Get-AzureRMAutomationVariable** parancsmagok a runbookot vagy a DSC-konfiguráció számára, mert azok a munkafolyamat-tevékenységek-nél kevésbé hatékonyak. Akkor is nem lehet lekérdezni a biztonságos változókról, **Get-AzureRMAutomationVariable**. Hozzon létre egy új változót a runbookot vagy a DSC-konfiguráció csak úgy, hogy használja a [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) parancsmag.


### <a name="textual-runbook-samples"></a>Szöveges forgatókönyvként minták

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>És egy egyszerű érték a változóból beolvasása

Az alábbi Példaparancsok szemléltetik egy szöveges forgatókönyvként változó beolvasása és beállítása. A példában feltételezzük, hogy az egész szám típusú nevű *NumberOfIterations* és *NumberOfRunnings* és nevű, karakterlánc típusú változó *példában* már létre van hozva.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>És egy összetett objektumot egy változóban beolvasása

Az alábbi mintakód bemutatja, hogyan szöveges forgatókönyvként összetett érték egy változó frissítése. Ez a példa egy Azure virtuális gépen a beolvasott **Get-AzureVM** és egy meglévő automatizálási változó mentve.  A [változó típusok](#variable-types), ez egy PSCustomObject tárolja.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

Az alábbi kódban értéke olvassa be a változót, és a virtuális gép indításához használt.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>És egy gyűjtemény változóként beolvasása

Az alábbi mintakód bemutatja, hogyan használhat egy változót a szöveges forgatókönyvként összetett értékek gyűjteménye. Ez a példa több Azure virtuális gépeken a rendszer beolvassa **Get-AzureVM** és egy meglévő automatizálási változó mentve. A [változó típusok](#variable-types), ez PSCustomObjects gyűjteménye tárolja.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

A következő kódrészlet a gyűjtemény olvassa be a változót, és minden virtuális gép indításához használt.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>És egy változó Python2 beolvasása
Az alábbi mintakód bemutatja, hogyan használhat egy változót, egy változót, és egy nem létező változó egy Python2 runbook kivétel kezelése.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a variable
    value = automationassets.get_automation_variable("test-variable")
    print value

    # set a variable (value can be int/bool/string)
    automationassets.set_automation_variable("test-variable", True)
    automationassets.set_automation_variable("test-variable", 4)
    automationassets.set_automation_variable("test-variable", "test-string")

    # handle a non-existent variable exception
    try:
        value = automationassets.get_automation_variable("non-existing variable")
    except AutomationAssetNotFound:
        print "variable not found"


### <a name="graphical-runbook-samples"></a>Grafikus forgatókönyv minták

A grafikus runbookokban, vegye fel a **Get-AutomationVariable** vagy **Set-AutomationVariable** a változó a könyvtár ablaktáblán grafikus szerkesztő csomagot jobb gombbal, majd válassza a kívánt tevékenységet.

![Vászonra változó hozzáadása](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>A beállítás értéke egy változó
Az alábbi ábrán egy változó frissíteni egy grafikus forgatókönyv egyszerű érték minta tevékenységeket. Ez a példa egy Azure virtuális gép a beolvasott **Get-AzureRmVM** és a számítógép nevét egy létező automatizálási változó karakterlánc típusú vannak mentve. Nem számít, hogy a [egy folyamat vagy feladatütemezési](automation-graphical-authoring-intro.md#links-and-workflow) óta csak egyetlen objektumot várt kimenet.

![Egyszerű változó beállítása](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>További lépések

* Tevékenységek összekapcsolása a grafikus szerzői kapcsolatos további információkért lásd: [grafikus szerzői hivatkozások](automation-graphical-authoring-intro.md#links-and-workflow)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md). 
