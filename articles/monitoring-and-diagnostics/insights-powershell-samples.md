---
title: "Figyelő PowerShell Azure gyors üzembe helyezési minta. | Microsoft Docs"
description: "PowerShell Azure figyelő szolgáltatásokkal, például az automatikus skálázás, riasztások, webhookok és tevékenységi naplóit keresés eléréséhez használja."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: 48f064884c2a6d0a55cc58a44169ed03c62de46d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>A figyelő PowerShell Azure gyors üzembe helyezési-minták
Ez a cikk jeleníti meg, akkor minták segítséget nyújtanak a figyelő az Azure-szolgáltatások elérésének PowerShell-parancsokat. Az Azure figyelő lehetővé teszi az automatikus skálázás Felhőszolgáltatásokat, a virtuális gépek és a Web Apps és riasztási értesítések küldését, vagy hívja a webes URL-címek, a konfigurált telemetriai adatok értékek alapján.

> [!NOTE]
> Az Azure figyelő csak 2016. Szeptembertől 25. az "Azure Insights" nevezett új neve. Azonban a névterek, és így a következő parancsok továbbra is tartalmazza az "insights".
> 
> 

## <a name="set-up-powershell"></a>PowerShell beállítása
Ha még nem tette meg, beállítása PowerShell futtatásához a számítógépen. További információkért lásd: [telepítése és konfigurálása PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>A cikkben szereplő példák
A cikkben a példák bemutatják, hogyan használhatja az Azure-figyelő parancsmagok. A következő Azure figyelő PowerShell-parancsmagok teljes listáját is felhasználhatja [Azure Monitor (Insights) parancsmagok](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Jelentkezzen be, és előfizetések használata
Első lépésként jelentkezzen be az Azure-előfizetéshez.

```PowerShell
Login-AzureRmAccount
```

Ehhez szükséges, hogy jelentkezzen be. Ha így tesz, a fiókjához, megjelenik a TenantID és alapértelmezett előfizetés-azonosító. Az Azure parancsmagok működik az alapértelmezett előfizetés keretében. Rendelkezik hozzáféréssel előfizetések listájának megtekintéséhez használja a következő parancsot.

```PowerShell
Get-AzureRmSubscription
```

Módosítsa a működő környezetét egy másik előfizetésbe, a következő paranccsal.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Az előfizetéshez tevékenységnapló beolvasása
Használja a `Get-AzureRmLog` parancsmag.  Az alábbiakban néhány gyakori példán.

Napló bejegyzéseinek lekérése a időpontot vagy dátumot van:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Egy időpontot vagy dátumot tartományba eső naplóbejegyzések beolvasása:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Naplóbejegyzéseket beszerzése egy adott erőforráscsoporthoz:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Egy adott erőforrás-szolgáltató egy időpontot vagy dátumot tartományba eső beolvasása a naplóbejegyzések:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Minden naplóbejegyzések adott hívó az beszerzése:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

A következő parancsot a legutóbbi 1000 események átveszi a műveletnapló:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`sok más paramétereket támogatja. Tekintse meg a `Get-AzureRmLog` további információt.

> [!NOTE]
> `Get-AzureRmLog`csak a előzmények számított 15 nyújt. Használja a **- MaxEvents** paraméter lehetővé teszi az utolsó N események meghaladja a 15 nap. 15 napnál régebbi hozzáférés események használja a REST API-t vagy az SDK-val (C# minta a SDK használatával). Ha nem adja meg **StartTime**, akkor az alapértelmezett érték **EndTime** mínusz 1 óra. Ha nem adja meg **EndTime**, akkor az alapértelmezett érték az aktuális idő. Minden alkalommal UTC szerepelnek.
> 
> 

## <a name="retrieve-alerts-history"></a>Riasztások listájáról beolvasása
Minden riasztási események megtekintéséhez az Azure Resource Manager naplók a következő példák segítségével lekérdezheti.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Egy adott riasztási szabály előzményeinek megtekintése, használhatja a `Get-AzureRmAlertHistory` parancsmag, az erőforrás-azonosítója a riasztási szabály alapjaként.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

A `Get-AzureRmAlertHistory` parancsmag különböző paramétereket támogatja. További információkért lásd: [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>A riasztási szabályokhoz adatbeolvasás
A következő parancsok közül mindegyik intézkedjen "montest" nevű erőforráscsoport.

A riasztási szabály az összes tulajdonság megjelenítése:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Az erőforráscsoport összes riasztás beolvasása:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

A tároló-erőforrások esetében minden riasztási szabályok beolvasása. Például a virtuális gép beállított összes riasztási szabályok.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`más paramétereket támogatja. Lásd: [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) további információt.

## <a name="create-metric-alerts"></a>Riasztások metrika létrehozása
Használhatja a `Add-AlertRule` parancsmag létrehozására, frissítésére, és tiltsa le a riasztási szabályt.

E-mailek és a webhook tulajdonságok használatával hozhat létre `New-AzureRmAlertRuleEmail` és `New-AzureRmAlertRuleWebhook`, illetve. A riasztási szabály parancsmag-példában rendelje hozzá azokat a műveleteket, mint a **műveletek** a riasztási szabály tulajdonsága.

A következő táblázat ismerteti a használt paraméterek és értékek használatával metrika riasztás létrehozása.

| A paraméter | érték |
| --- | --- |
| Név |simpletestdiskwrite |
| A riasztási szabály helye |USA keleti régiója |
| ResourceGroup |montest |
| Targetresourceid azonosítója |/Subscriptions/S1/resourceGroups/montest/Providers/Microsoft.COMPUTE/virtualMachines/testconfig |
| A riasztás létrehozott MetricName |\PhysicalDisk (_Total) \Disk/mp. Tekintse meg a `Get-MetricDefinitions` parancsmag lekéri a pontos mérték nevét kapcsolatos |
| Operátor |GreaterThan |
| A küszöbérték (száma másodpercenként a Ez a mérőszám a) |1 |
| Ablakméret (ÓÓ: pp: formátum) |00:05:00 |
| a gyűjtő (statisztikai mérőszám, amely ebben az esetben használja az átlagos száma) |Átlagos |
| egyéni e-mailek (karakterlánc-tömbben) |'foo@example.com','bar@example.com' |
| e-mail küldése a tulajdonosok, közreműködő szerepkörrel rendelkező személyek és olvasók |-SendToServiceOwners |

E-mailek művelet létrehozása

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

A Webhook művelet létrehozása

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

A riasztási szabály létrehozása a klasszikus virtuális gép CPU % metrika

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

A riasztási szabály beolvasása

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

A Hozzáadás riasztási parancsmag frissíti a szabály is, ha a riasztási szabály már létezik a megadott tulajdonságok. Riasztási szabály letiltásához adja meg az értékét **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>A riasztások elérhető listájának lekérdezése
Használhatja a `Get-AzureRmMetricDefinition` parancsmag használatával megtekintheti az összes egy adott erőforrás metrikáit listáját.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

A következő példa olyan táblát, amely a mérték nevének és a időegységét hoz létre.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Az elérhető lehetőségek teljes listáját `Get-AzureRmMetricDefinition` érhető el [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Automatikus skálázási beállítások létrehozása és kezelése
Egy erőforrás, például egy webalkalmazást, a virtuális gép, a felhőalapú szolgáltatás vagy a virtuálisgép-méretezési csoport rendelkezhet beállított csak egy automatikus skálázási beállítás.
Van azonban, az minden automatikus skálázási beállítás profiljainak. Például egy méretezési teljesítmény-alapú profil és egy másikat a ütemezésalapú profilra. Az egyes profilok rendelkezhet több szabály konfigurálva. Automatikus méretezéssel kapcsolatos további információkért lásd: [automatikus skálázás alkalmazás hogyan](../cloud-services/cloud-services-how-to-scale.md).

Használjuk lépései a következők:

1. Szabályok létrehozása.
2. A profilok leképezése a korábban létrehozott szabályok profil létrehozásához.
3. Választható lehetőség: Az automatikus skálázás értesítések létrehozásához webhook és e-mailek tulajdonságainak konfigurálása.
4. A cél erőforráson nevű leképezi a profilok és az előző lépésben létrehozott értesítések létrehozása az automatikus skálázási beállítás.

Az alábbi példák bemutatják, hogyan hozhat létre az automatikus skálázási beállítás egy virtuálisgép-méretezési csoportban Windows operációs rendszer esetén a CPU-kihasználtság metrika segítségével.

Először hozzon létre egy szabályt, amely kibővített, a példány számának növelését.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Ezután hozzon létre egy szabályt, amely méretezési az, egy példány száma csökken.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Ezután hozzon létre szabályokat egy profilt.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Hozzon létre egy webhook tulajdonságot.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Az értesítési tulajdonságot, az automatikus skálázási beállítás, beleértve az e-mailek és a korábban létrehozott webhook létrehozása.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Végezetül hozza létre a fentiekben létrehozott profil hozzáadása az automatikus skálázási beállítás.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Automatikus skálázási beállítások kezelésével kapcsolatos további információkért lásd: [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Automatikus skálázás előzmények
A következő példa bemutatja, hogyan megtekintheti a legutóbbi automatikus skálázás és figyelmeztetési események. A tevékenység napló keresési segítségével az automatikus skálázás előzményeinek megtekintése.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Használhatja a `Get-AzureRmAutoScaleHistory` parancsmag használatával kérhet le az automatikus skálázás előzményeit.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

További információkért lásd: [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Az automatikus skálázási beállítás részleteinek megtekintése
Használhatja a `Get-Autoscalesetting` parancsmag automatikus skálázási beállítás kapcsolatos további információk lekéréséhez.

A következő példa az erőforrás "myrg1" csoport az összes automatikus skálázási beállítás részleteit jeleníti meg.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

A következő példa az erőforrás "myrg1" csoport az összes automatikus skálázási beállítás és a kifejezetten a "MyScaleVMSSSetting" nevű automatikus skálázási beállítás részleteit jeleníti meg.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Távolítsa el az automatikus skálázási beállítás
Használhatja a `Remove-Autoscalesetting` parancsmag segítségével törölje az automatikus skálázási beállítás.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>A műveletnapló napló profilok kezelése
Létrehozhat egy *profilt naplózni* és a tevékenységnapló egy tárfiókot, és az adatok exportálása az adatmegőrzés konfigurálhatja azt. Az adatok szükség esetén az Eseményközpont is adatfolyam. Vegye figyelembe, hogy ez a funkció jelenleg előzetes állapotban van, akkor csak egy naplófájl profil előfizetésenként hozhat létre. Segítségével a következő parancsmagokat a jelenlegi előfizetés napló profilok létrehozásához és kezeléséhez. Egy adott előfizetést is beállíthatja. PowerShell alapértelmezett az aktuális előfizetésben, de bármikor módosíthatja, hogy használatával `Set-AzureRmContext`. Konfigurálhatja a tevékenységnapló útvonal az adatokat bármely tárfiók vagy az Eseményközpont kiválasztásával adott előfizetésen belül. Adatok blob fájlok JSON formátumban van megírva.

### <a name="get-a-log-profile"></a>A napló profil beolvasása
A meglévő napló profilok olvasni, használja a `Get-AzureRmLogProfile` parancsmag.

### <a name="add-a-log-profile-without-data-retention"></a>Az adatok megőrzése nélkül napló profil hozzáadása
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Napló-profil eltávolítása
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Az adatok megőrzése napló profil hozzáadása
Megadhatja a **- RetentionInDays** hány nap, mint egy pozitív egész számot, ahol az adatok megőrződnek tulajdonság.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>A megőrzési és EventHub napló profil hozzáadása
Az adatok tárfiókba az útválasztáson kívül akkor is is adatfolyamként az Eseményközpontba. Vegye figyelembe, hogy az előzetes kiadás és a tárolási fiók konfigurációjának megadása kötelező, de az Event Hubs konfigurálása nem kötelező.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Diagnosztikai naplók konfigurálása
Sok Azure-szolgáltatások további naplókat és az adatok mentése az Azure Storage-fiók, az Event Hubs küldeni konfigurálva, illetve egy OMS Naplóelemzési munkaterület küldött telemetriai adatot adja meg. Ez a művelet csak egy erőforrás szinten hajtható végre, és a tárolási fiók vagy az event hub ugyanabban a régióban a cél erőforráson, ahol a diagnosztika beállítás konfigurálva jelen kell lennie.

### <a name="get-diagnostic-setting"></a>Diagnosztikai beállításának beolvasása
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Diagnosztikai beállítás letiltása

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Diagnosztikai beállítás nélkül megőrzési engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Diagnosztikai beállításának megőrzést engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Egy adott napló kategória megőrzést diagnosztikai beállítás engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Az Event Hubs diagnosztikai beállításának engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Diagnosztikai beállításának OMS engedélyezése

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId 76d785fd-d1ce-4f50-8ca3-858fc819ca0f -Enabled $true

```
