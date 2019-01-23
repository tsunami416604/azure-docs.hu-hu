---
title: Az Azure Monitor PowerShell gyors üzembe helyezési minták
description: Elérhető az Azure Monitor szolgáltatásait, például az automatikus méretezés, a riasztások, a webhookok és tevékenységeket tartalmazó naplók keresése a PowerShell használatával.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: d3bd4d80f167a496c9b980891f31d30bc86a0cb2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473759"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Az Azure Monitor PowerShell gyors üzembe helyezési minták
Ez a cikk mutatja, a minta PowerShell-parancsok segítségével elérheti az Azure Monitor-funkciók.

> [!NOTE]
> Az Azure Monitor 2016. Szeptembertől 25. az "Azure Insights" nevezett új neve. Azonban a névterek, és így a következő parancsok továbbra is tartalmaz a szó "elemzések."
> 
> 

## <a name="set-up-powershell"></a>Set up PowerShell
Ha még nem tette, PowerShell beállítása a futást a számítógépen. További információkért lásd: [telepítése és konfigurálása PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Ebben a cikkben szereplő példák
A cikkben szereplő példák bemutatják, hogyan használhatja az Azure Monitor-parancsmagokat. Emellett áttekintheti a teljes listáját, az Azure Monitor PowerShell-parancsmagok [Azure Monitor (elemzés) parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.insights).

## <a name="sign-in-and-use-subscriptions"></a>Jelentkezzen be, és előfizetések használata
Első lépésként jelentkezzen be az Azure-előfizetéshez.

```PowerShell
Connect-AzureRmAccount
```

Megjelenik egy bejelentkezési képernyő. Egyszer, jelentkezzen be a fiók a bérlő azonosítója, és alapértelmezett előfizetés-azonosító jelennek meg. Az összes Azure-parancsmagjaival működik az alapértelmezett előfizetést kontextusában. Hozzáfér az előfizetések listájának megtekintéséhez használja a következő parancsot:

```PowerShell
Get-AzureRmSubscription
```

A működő környezet másik előfizetésbe való módosításához használja a következő parancsot:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Tevékenységnapló-előfizetéshez tartozó beolvasása
Használja a `Get-AzureRmLog` parancsmagot.  Az alábbiakban néhány gyakori példa.

Naplóbejegyzés kérhet az időpontot vagy dátumot, hogy:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Kérje le a naplóbejegyzések között egy dátum/idő tartomány:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplózási bejegyzések beolvasása egy adott erőforráscsoporthoz:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Kérje le a naplóbejegyzések egy adott erőforrás-szolgáltató egy dátum/idő tartomány között:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Kérje le egy adott hívónak az összes naplóbejegyzés:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Az alábbi parancs beolvassa a tevékenységnaplóban a legutóbbi 1000 esemény:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` sok más paramétereket támogatja. Tekintse meg a `Get-AzureRmLog` további információt.

> [!NOTE]
> `Get-AzureRmLog` Előzmények 15 napos csak biztosít. Használatával a **– a MaxEvents** paraméter lehetővé teszi, hogy az utolsó N események 15 napos időszak letelte után. Hozzáférési események régebbi, mint 15 napon keresztül használja a REST API vagy SDK-t (C# minta az SDK-val). Ha nem adja meg az **StartTime**, akkor az alapértelmezett érték **EndTime** mínusz egy óra. Ha nem adja meg az **EndTime**, akkor az alapértelmezett érték az aktuális idő. Minden esetben vannak (UTC).
> 
> 

## <a name="retrieve-alerts-history"></a>Riasztások előzményeinek beolvasása
Minden figyelmeztetési események megtekintéséhez lekérdezheti az Azure Resource Manager naplók segítségével az alábbi példák.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Az előzmények adott riasztási szabály megtekintéséhez használhatja a `Get-AzureRmAlertHistory` parancsmag, az erőforrás-Azonosítóját a riasztási szabályt ad át.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

A `Get-AzureRmAlertHistory` parancsmag támogatja a különböző paraméterek. További információkért lásd: [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Riasztási szabályok az információk lekéréséhez
Az alábbi parancsokat az összes "montest" nevű erőforráscsoport cselekedhet.

A riasztási szabály az összes tulajdonság megjelenítése:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Az erőforráscsoport összes riasztás lekéréséhez:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Összes riasztási szabályt beállítani a célként megadott erőforrás lekérése. Például állítsa be a riasztási szabályok az összes virtuális gépen.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` más paramétereket támogatja. Lásd: [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) további információt.

## <a name="create-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások létrehozása
Használhatja a `Add-AlertRule` parancsmag létrehozása, frissítése, vagy tiltsa le egy riasztási szabályt.

E-mailt és webhookot tulajdonságok használatával hozhat létre `New-AzureRmAlertRuleEmail` és `New-AzureRmAlertRuleWebhook`, illetve. A riasztási szabály parancsmag rendelje hozzá ezeket a tulajdonságokat a műveleteket a **műveletek** a riasztási szabály tulajdonságát.

A következő táblázat ismerteti a használt paraméterek és értékek metrikával riasztás létrehozásához.

| paraméter | érték |
| --- | --- |
| Name (Név) |simpletestdiskwrite |
| Ez a riasztási szabály helye |USA keleti régiója |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| A létrehozott riasztás MetricName |\PhysicalDisk (aránya _teljes) \Disk Lemezírások/mp. Tekintse meg a `Get-MetricDefinitions` parancsmaggal kapcsolatos hogyan kérheti le a pontos metrikus nevek |
| Operátor |GreaterThan |
| Küszöbérték (száma/s az a mérőszám) |1 |
| Ablakméret (Igen) |00:05:00 |
| naplózási gyűjtő (statisztikai mérőszám, amely ebben az esetben használja a átlagos száma) |Átlag |
| egyéni e-mailek (karakterlánc-tömbben) |'foo@example.com','bar@example.com' |
| e-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak |-SendToServiceOwners |

E-mail-művelet létrehozása

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Hozzon létre egy Webhook művelettel

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

A riasztási szabály létrehozása a klasszikus virtuális gép CPU % metrikája

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

A riasztási szabály lekérése

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

A Hozzáadás riasztási parancsmag frissíti a szabály is, ha a riasztási szabály már létezik a megadott tulajdonságokat. Riasztási szabály letiltása, adjon meg a paraméter **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>A riasztásokhoz rendelkezésre álló metrikák listájának lekérése
Használhatja a `Get-AzureRmMetricDefinition` parancsmagot, hogy egy adott erőforrás az összes metrikát listájának megtekintéséhez.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

Az alábbi példa egy táblát a metrika neve és a hozzá tartozó egységet állít elő.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Az elérhető lehetőségek teljes listáját `Get-AzureRmMetricDefinition` mindig elérhető legyen [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Tevékenységnapló-riasztások létrehozása és felügyelete
Használhatja a `Set-AzureRmActivityLogAlert` parancsmag egy tevékenységnapló-riasztás beállítása. Tevékenységnapló-riasztás megköveteli, hogy először adja meg a feltételeit tartalmazó feltételek szerint, majd hozzon létre egy riasztást, amely feltételeken használja.

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

A további webhook tulajdonságainak megadása nem kötelező. Visszatérhet a tartalmát egy tevékenység Log riasztási a `Get-AzureRmActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Hozzon létre és automatikus méretezési beállítások kezelése
Egy erőforrás (webes alkalmazás, virtuális gépek, Felhőszolgáltatás vagy virtuálisgép-méretezési) rendelkezhet beállított csak egy automatikus skálázási beállítás.
Minden egyes automatikus skálázási beállítás azonban több profilt is rendelkezik. Például az egyik teljesítmény-alapú méretezési profil és a egy másikat ütemezésen alapuló profilok. Az egyes profilok konfigurálva több szabály is rendelkezhet. Automatikus skálázási kapcsolatos további információkért lásd: [az automatikus Skálázáshoz alkalmazás](../../cloud-services/cloud-services-how-to-scale-portal.md).

Használandó lépései a következők:

1. Szabály létrehozásához.
2. Hozzon létre az Ön által létrehozott szabályok korábban leképezése a profilokat a kívánt módon.
3. Nem kötelező: Webhookok és e-mailek tulajdonságainak konfigurálása az automatikus skálázási értesítések létrehozásához.
4. Hozzon létre egy automatikus skálázási beállítás a profilok és az előző lépésekben létrehozott értesítések leképezésével egy nevet a cél erőforráson.

Az alábbi példák bemutatják, hogyan hozhat létre egy automatikus skálázási beállítás számára a Virtual Machine Scale Set használatával a CPU-kihasználtság mérőszám-alapú Windows operációs rendszer esetén.

Először hozzon létre egy szabályt, amely a horizontális felskálázás, a példányok száma növelését.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Ezután hozzon létre egy szabályt, az egy példány száma csökkenését.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Ezután hozzon létre egy profilt a szabályokat.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Hozzon létre egy webhook tulajdonságot.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Hozzon létre az automatikus skálázási beállítás, beleértve az e-mailek és a webhookot, amely a korábban létrehozott értesítési tulajdonsága.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Végül hozza létre az automatikus skálázási beállítás a korábban létrehozott profil hozzáadásához. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Az automatikus méretezési beállítások kezelésével kapcsolatos további információkért lásd: [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Automatikus skálázási előzmények
Az alábbi példa bemutatja, hogyan megtekintheti a legutóbbi automatikus méretezést és figyelmeztetési események. A tevékenység naplóbeli keresés használatával az automatikus skálázási előzményeinek megtekintése.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Használhatja a `Get-AzureRmAutoScaleHistory` automatikus skálázási előzmények beolvasásához.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

További információkért lásd: [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Az automatikus skálázási beállítás részleteinek megtekintése
Használhatja a `Get-Autoscalesetting` további információ az automatikus skálázási beállítás beolvasásához.

Az alábbi példa bemutatja az összes automatikus skálázási beállítások részleteit a myrg1"erőforrás csoport".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Az alábbi példa bemutatja az erőforrás "myrg1" csoport az összes automatikus skálázási beállítás és a kifejezetten az automatikus skálázási beállítás "MyScaleVMSSSetting" nevű részleteit.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Az automatikus skálázási beállítás törlése
Használhatja a `Remove-Autoscalesetting` parancsmagot, hogy az automatikus skálázási beállítás törlése.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Naplóprofilok a tevékenységnapló kezelése
Létrehozhat egy *naplóprofil* és a tevékenységnapló egy tárfiókot, és hogy az adatok exportálása az adatmegőrzés konfigurálhatja azt. Az adatok igény szerint, az Eseményközpont is adatfolyam. Ez a funkció jelenleg előzetes verzióban és csak egy naplóprofil előfizetésenként hozhat létre. Segítségével a következő parancsmagokat az aktuális előfizetéshez log profilok létrehozásához és kezeléséhez. Azt is beállíthatja egy adott előfizetésben. Bár a PowerShell alapértelmezés szerint az aktuális előfizetésben, módosíthatja, hogy az `Set-AzureRmContext`. Konfigurálhatja a tevékenységnapló adatátirányításhoz minden olyan tárfiókhoz vagy az Eseményközpont adott előfizetésen belül. Adatok JSON formátumban blob-fájlok formájában van megírva.

### <a name="get-a-log-profile"></a>A napló profil beolvasása
A meglévő naplóprofilok beolvasni, használja a `Get-AzureRmLogProfile` parancsmagot.

### <a name="add-a-log-profile-without-data-retention"></a>Adatok megőrzése nélkül log profil hozzáadása
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Napló profil eltávolítása
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Az adatmegőrzési naplót profil hozzáadása
Megadhatja a **- RetentionInDays** tulajdonságot, amelyben az adott számú napon, egy pozitív egész számot, ahol az adatok megőrződnek.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Naplóprofil megőrzési és az EventHub hozzáadása
Az adatok tárfiókba az útválasztáson kívül is streamelheti azt az Eseményközpontba. Ez az előzetes kiadásban a storage-fiók konfigurációjának megadása kötelező, de az Eseményközpont konfigurálása nem kötelező.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnosztikai naplók konfigurálása
Számos Azure-szolgáltatásokat nyújtanak további naplók és a telemetriai adatokból, hogy egy vagy több, a következőket teheti: 
 - úgy konfigurálni, hogy az adatok mentése az Azure Storage-fiókban
 - az Event hubs szolgáltatásba küldött
 - Log Analytics-munkaterület küldi. 

A művelet csak egy erőforrás szinten hajtható végre. A storage-fiók vagy eseményközpont ugyanabban a régióban a célként megadott erőforrás, ahol a diagnosztikai beállítás konfigurálva jelen kell lennie.

### <a name="get-diagnostic-setting"></a>Diagnosztikai beállítás beolvasása
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnosztikai beállítás letiltása

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnosztikai beállítás nélkül adatmegőrzés engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

A megőrzési diagnosztikai beállítás engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Egy adott naplókategória adatmegőrzési-diagnosztikai beállítás engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Az Event Hubs diagnosztikai beállítás engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

A Log Analytics diagnosztikai beállítás engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Vegye figyelembe, hogy a munkaterület azonosítója tulajdonság vesz igénybe a *erőforrás-azonosító* a munkaterület. Az erőforrás-Azonosítóját a Log Analytics-munkaterület a következő paranccsal szerezheti be:

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

Ezek a parancsok kombinálható több célhelyre történő adatküldéshez.

