---
title: Azure Monitor – PowerShell gyors üzembehelyezési minták
description: A PowerShell használatával elérheti az Azure Monitor funkcióit, például az automatikus skálázást, a riasztásokat, a webhookokat és a keresési tevékenységnaplókat.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274319"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Monitor – PowerShell gyors üzembehelyezési minták
Ez a cikk bemutatja a PowerShell-parancsok mintavételezését az Azure Monitor funkcióinak eléréséhez.

> [!NOTE]
> 2016. szeptember 25-ig az Azure Monitor az "Azure Insights" elnevezés új neve. A névterek és így a következő parancsok azonban továbbra is tartalmazzák az "insights" szót.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>A PowerShell beállítása
Ha még nem tette meg, állítsa be a PowerShell t a számítógépen való futtatásra. További információt a [PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál.

## <a name="examples-in-this-article"></a>Példák ebben a cikkben
A példák a cikkben bemutatják, hogyan használhatja az Azure Monitor parancsmagjait. Az Azure Monitor PowerShell-parancsmagok teljes listáját is áttekintheti az [Azure Monitor (Insights) parancsmagjaiban.](https://docs.microsoft.com/powershell/module/az.applicationinsights)

## <a name="sign-in-and-use-subscriptions"></a>Bejelentkezés és előfizetések használata
Először jelentkezzen be azure-előfizetésbe.

```powershell
Connect-AzAccount
```

Megjelenik egy bejelentkezési képernyő. Miután bejelentkezett a fiókjába, tenantID és alapértelmezett előfizetési azonosító jelenik meg. Az összes Azure-parancsmag az alapértelmezett előfizetés környezetében működik. Az előfizetések listájának megtekintéséhez használja a következő parancsot:

```powershell
Get-AzSubscription
```

A munkakörnyezet megtekintéséhez (amelyhez a parancsok futnak) a következő parancsot használja:

```powershell
Get-AzContext
```
Ha a munkakörnyezetet másik előfizetésre szeretné módosítani, használja a következő parancsot:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Tevékenységnapló lekérése egy előfizetéshez
Használja a [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) parancsmagát.  Az alábbiakban néhány gyakori példa. A tevékenységnapló a műveletek utolsó 90 napjait tartalmazza. Az ezen időpont előtti dátumok használata hibaüzenetet eredményez.  

Nézze meg, mi az aktuális dátum / idő, hogy ellenőrizze, milyen időpontokban kell használni az alábbi parancsokat:
```powershell
Get-Date
```

Naplóbejegyzések betöltése ettől az időponttól/dátumtól napjainkig:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Naplóbejegyzések betöltése egy idő/dátum tartomány között:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzések beszereznie egy adott erőforráscsoportból:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Naplóbejegyzések betöltése egy adott erőforrásszolgáltatótól egy idő/dátum tartomány között:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Az összes naplóbejegyzés beszereznie egy adott hívóval:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

A következő parancs lekéri az utolsó 1000 eseményt a tevékenységnaplóból:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog`számos más paramétert is támogat. További `Get-AzLog` információt a hivatkozásban talál.

> [!NOTE]
> `Get-AzLog`csak 15 napos történelmet biztosít. A **-MaxRecords** paraméter használatával 15 napon túl is lekérdezheti az utolsó N-eseményeket. A 15 napnál régebbi események eléréséhez használja a REST API-t vagy az SDK-t (C# minta az SDK használatával). Ha nem adja meg a **StartTime**értéket, akkor az alapértelmezett érték **az EndTime** mínusz egy óra. Ha nem adja meg **az EndTime**értéket, akkor az alapértelmezett érték az aktuális idő. Minden alkalommal utc-ben van.
> 
> 

## <a name="retrieve-alerts-history"></a>A riasztások előzményei
Az összes riasztási esemény megtekintéséhez lekérdezheti az Azure Resource Manager naplók az alábbi példák használatával.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Egy adott riasztási szabály előzményeinek megtekintéséhez `Get-AzAlertHistory` használhatja a parancsmamot, amely átadja a riasztási szabály erőforrás-azonosítóját.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

A `Get-AzAlertHistory` parancsmag különböző paramétereket támogat. További információ: [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>A riasztási szabályokkal kapcsolatos információk lekérése
Az alábbi parancsok mindegyike a "montest" nevű erőforráscsoporton jár el.

A riasztási szabály összes tulajdonságának megtekintése:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Erőforráscsoport összes riasztásának lekérése:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

A célerőforráshoz beállított összes riasztási szabály lekérése. Például a virtuális gépen beállított összes riasztási szabály.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule`támogatja az egyéb paramétereket. További információ: [Get-AlertRule.](https://msdn.microsoft.com/library/mt282459.aspx)

## <a name="create-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások létrehozása
A `Add-AlertRule` parancsmag segítségével riasztási szabályt hozhat létre, frissíthet vagy letilthatja.

E-mail és webhook-tulajdonságokat a használatával, illetve `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook`a használatával hozhat létre. A riasztási szabály parancsmagban rendelje hozzá ezeket a tulajdonságokat műveletekként a riasztási szabály **Műveletek** tulajdonságához.

Az alábbi táblázat ismerteti a paramétereket és értékeket, amelyek et egy metrika használatával hozlétre.

| parameter | érték |
| --- | --- |
| Név |simpletestdiskwrite |
| A riasztási szabály helye |USA keleti régiója |
| ResourceGroup |montest |
| TargetResourceId azonosító |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| A létrehozott riasztás metrikája |\PhysicalDisk(_Total)\Lemezírás/mp. Tekintse `Get-MetricDefinitions` meg a pontos metrikanevek beolvasásáról szóló parancsmaleletet |
| operátor |GreaterThan |
| Küszöbérték (számláló/mp ehhez a metrikahoz) |1 |
| WindowSize (óó:pp:ss formátum) |00:05:00 |
| aggregátor (a mérőszám statisztikája, amely ebben az esetben az Átlagszámú, ebben az esetben) |Átlag |
| egyéni e-mailek (karakterlánctömb) |'foo@example.com','bar@example.com' |
| küldjön e-mailt tulajdonosoknak, közreműködőknek és olvasóknak |-SendToServiceOwners |

E-mail művelet létrehozása

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Webhook-művelet létrehozása

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Hozza létre a riasztási szabályt a CPU% metrika egy klasszikus virtuális gép

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

A riasztási szabály beolvasása

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

A riasztási parancsmag frissítése is frissíti a szabályt, ha már létezik riasztási szabály a megadott tulajdonságokhoz. Riasztási szabály letiltásához adja meg a **-DisableRule paramétert.**

## <a name="get-a-list-of-available-metrics-for-alerts"></a>A riasztásokhoz rendelkezésre álló mérőszámok listájának beszerzése
A `Get-AzMetricDefinition` parancsmag segítségével megtekintheti egy adott erőforrás összes metrika listáját.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

A következő példa létrehoz egy táblázatot a metrika neve és az egység is.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Az elérhető lehetőségek teljes `Get-AzMetricDefinition` listája elérhető a [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)oldalon.

## <a name="create-and-manage-activity-log-alerts"></a>Tevékenységnapló-riasztások létrehozása és kezelése
A parancsmag `Set-AzActivityLogAlert` segítségével állíthatja be a tevékenységnapló-riasztást. A tevékenységnapló-riasztás megköveteli, hogy először határozza meg a feltételeket a feltételek szótáraként, majd hozzon létre egy riasztást, amely ezeket a feltételeket használja.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

A további webhook-tulajdonságok nem kötelezőek. A program visszakaphatja a tevékenységnapló-riasztás tartalmát a használatával. `Get-AzActivityLogAlert`

## <a name="create-and-manage-autoscale-settings"></a>Automatikus skálázási beállítások létrehozása és kezelése
Egy erőforrás (egy webalkalmazás, virtuális gép, felhőszolgáltatás vagy virtuálisgép-méretezési készlet) csak egy automatikus skálázási beállítás van konfigurálva.
Azonban minden automatikus skálázási beállítás több profillal is rendelkezhet. Például egy teljesítményalapú méretezési profilhoz, a másik pedig egy ütemezésalapú profilhoz. Minden profilon több szabály is konfigurálható. Az automatikus skálázásról további információt az [Alkalmazások automatikus skálázása](../../cloud-services/cloud-services-how-to-scale-portal.md)című témakörben talál.

A következő lépéseket kell használni:

1. Szabály(ok) létrehozása.
2. Profil(ok) létrehozása a korábban létrehozott szabályok hozzárendelése a profilokhoz.
3. Nem kötelező: Hozzon létre értesítéseket az automatikus skálázáshoz a webhook és az e-mail tulajdonságainak konfigurálásával.
4. Hozzon létre egy automatikus skálázási beállítást a célerőforrás nevével az előző lépésekben létrehozott profilok és értesítések leképezésével.

Az alábbi példák bemutatják, hogyan hozhat létre automatikus skálázási beállítást egy windowsos operációs rendszerhez a CPU-kihasználtsági metrika használatával.

Először hozzon létre egy szabályt horizontális felskálázás, a példányok száma növelése.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Ezután hozzon létre egy szabályt a méretezéshez, a példányok számának csökkenésével.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Ezután hozzon létre egy profilt a szabályokhoz.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Hozzon létre egy webhook tulajdonságot.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Hozza létre az értesítési tulajdonságot az automatikus skálázási beállításhoz, beleértve az e-mailt és a korábban létrehozott webhookot.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Végül hozza létre az automatikus skálázási beállítást a korábban létrehozott profil hozzáadásához. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Az automatikus skálázási beállítások kezeléséről a [Get-AutoscaleSetting című](https://msdn.microsoft.com/library/mt282461.aspx)témakörben olvashat bővebben.

## <a name="autoscale-history"></a>Automatikus skálázási előzmények
A következő példa bemutatja, hogyan tekintheti meg a legutóbbi automatikus skálázási és riasztási eseményeket. A tevékenységnapló-keresés segítségével megtekintheti az automatikus skálázási előzményeket.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

A parancsmag `Get-AzAutoScaleHistory` segítségével lekérheti az Automatikus skálázási előzményeket.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

További információ: [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Automatikus skálázási beállítás részleteinek megtekintése
A `Get-Autoscalesetting` parancsmag segítségével további információkat kérhet be az automatikus skálázási beállításról.

A következő példa a "myrg1" erőforráscsoport összes automatikus skálázási beállításának részleteit mutatja be.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

A következő példa a "myrg1" erőforráscsoport összes automatikus skálázási beállításának és különösen a "MyScaleVMSSSetting" nevű automatikus skálázási beállítás részleteinek részleteit mutatja be.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Automatikus skálázási beállítás eltávolítása
A parancsmag `Remove-Autoscalesetting` segítségével törölheti az automatikus skálázási beállításokat.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Tevékenységnapló naplóprofiljának kezelése
Létrehozhat egy *naplóprofilt,* és adatokat exportálhat a tevékenységnaplóból egy tárfiókba, és beállíthatja az adatmegőrzést. Szükség esetén az adatokat az Event Hubra is továbbíthatja. Ez a funkció jelenleg előzetes verzióban érhető el, és előfizetésenként csak egy naplóprofilt hozhat létre. A naplóprofilok létrehozásához és kezeléséhez a következő parancsmagok segítségével hozhat létre és kezelhet naplóprofilokat. Egy adott előfizetést is választhat. Bár a PowerShell alapértelmezés szerint az aktuális előfizetés, bármikor módosíthatja, hogy a használatával. `Set-AzContext` A tevékenységnapló konfigurálásával adatokat továbbíthat az adott előfizetésen belüli bármely tárfiókba vagy Event Hubra. Az adatok JSON formátumban blobfájlokként vannak megírva.

### <a name="get-a-log-profile"></a>Naplóprofil beszereznie
A meglévő naplóprofilok beolvasásához használja a `Get-AzLogProfile` parancsmabot.

### <a name="add-a-log-profile-without-data-retention"></a>Naplóprofil hozzáadása adatmegőrzés nélkül
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Naplóprofil eltávolítása
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Naplóprofil hozzáadása adatmegőrzéssel
A **-RetentionInDays** tulajdonságot a napok számával pozitív egész számként adhatja meg, ahol az adatok megőrződnek.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Naplóprofil hozzáadása megőrzési és EventHub-profillal
Az adatok tárfiókba történő továbbítása mellett streamelheti őket egy Eseményközpontba is. Ebben az előzetes kiadásban a tárfiók konfigurációja kötelező, de az Event Hub konfigurációja nem kötelező.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnosztikai naplók konfigurálása
Számos Azure-szolgáltatás további naplókat és telemetriai adatokat biztosít, amelyek az alábbi műveleteket tehetik le: 
 - konfigurálható adatok mentésére az Azure Storage-fiókban
 - elküldve az Eseményközpontoknak
 - a Log Analytics-munkaterületre. 

A művelet csak erőforrás-szinten hajtható végre. A tárfiók vagy az eseményközpont ugyanabban a régióban kell lennie, ahol a diagnosztikai beállítás konfigurálva van.

### <a name="get-diagnostic-setting"></a>Diagnosztikai beállítás beszerezni
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnosztikai beállítás letiltása

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnosztikai beállítás engedélyezése megőrzés nélkül

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnosztikai beállítás engedélyezése megőrzéssel

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnosztikai beállítás engedélyezése egy adott naplókategória megőrzésével

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Diagnosztikai beállítás engedélyezése az Eseményközpontokhoz

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnosztikai beállítás engedélyezése a Log Analytics szolgáltatáshoz

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Vegye figyelembe, hogy a WorkspaceId tulajdonság a munkaterület *erőforrás-azonosítóját* veszi át. A Log Analytics-munkaterület erőforrás-azonosítóját a következő paranccsal szerezheti be:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Ezek a parancsok kombinálhatók, hogy adatokat küldjenek több célhelyre.

