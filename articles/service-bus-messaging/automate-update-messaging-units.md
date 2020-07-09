---
title: Azure Service Bus – üzenetkezelési egységek automatikus frissítése
description: Ez a cikk bemutatja, hogyan használható egy Azure Automation runbook egy Service Bus névtér üzenetkezelési egységének automatikus frissítéséhez.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341494"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus névtér üzenetkezelési egységeinek automatikus frissítése 
Ez a cikk bemutatja, hogyan lehet automatikusan frissíteni egy Service Bus névtér [üzenetkezelési egységeit](service-bus-premium-messaging.md) erőforrás-(CPU-vagy memória-) használat alapján. 

A cikkben szereplő példa azt mutatja be, hogyan növelhető az üzenetkezelési egységek egy Service Bus névtér esetében, ha a névtér CPU-használata 75% fölé esik. A magas szintű lépések a következők:

1. Hozzon létre egy Azure Automation runbook olyan PowerShell-parancsfájllal, amely egy Service Bus-névtérhez tartozó üzenetkezelési egységeket méretezi (növeli). 
2. Hozzon létre egy CPU-használati riasztást a Service Bus névtérben, amely meghívja a PowerShell-parancsfájlt, amikor a névtér CPU-kihasználtsága 75% fölé kerül. 

> [!IMPORTANT]
> Ez a cikk csak a Azure Service Bus **prémium** szintjére vonatkozik. 


## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása
Hozzon létre egy Premier szintű Service Bus névteret. A névtér létrehozásához kövesse a [névtér létrehozása a Azure Portalban](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) című cikk lépéseit. 

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása
Hozzon létre egy Azure Automation fiókot az Azure Automation- [fiók létrehozása](../automation/automation-quickstart-create-account.md) című cikk utasításait követve. 

## <a name="import-azservice-module-from-gallery"></a>Importálás az. Service modul a katalógusból
Importálja `Az.Accounts` és modulokat a katalógusból `Az.ServiceBus` az Automation-fiókba. A `Az.ServiceBus` modul a `Az.Accounts` modultól függ, ezért először telepíteni kell. 

Részletes útmutatásért lásd: [modul importálása a modul-galériából](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>PowerShell-runbook létrehozása és közzététele

1. Hozzon létre egy PowerShell-runbook a [PowerShell-Runbook létrehozása](../automation/automation-quickstart-create-runbook.md) című cikk utasításait követve. 

    Az alábbiakban egy PowerShell-szkriptet használhat egy Service Bus névtér üzenetkezelési egységeinek növelésére. Ez az Automation-runbook található PowerShell-szkript 1 – 2, 2 és 4 közötti, illetve 4 – 8 értékre növeli a MUs-t. A tulajdonság megengedett értékei a következők: 1, 2, 4 és 8. Egy másik runbook is létrehozhat az üzenetkezelési egységek csökkentése érdekében.

    A **namespacename tulajdonság** és a **resourceGroupName** paraméterek a parancsfájlnak a riasztási forgatókönyvtől függetlenül történő tesztelésére szolgálnak. 
    
    A **WebHookData** paraméter arra vonatkozik, hogy a riasztás olyan információkat adjon át, mint az erőforráscsoport neve, az erőforrás neve stb. futásidőben. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Tesztelje a munkafüzetet](../automation/manage-runbooks.md#test-a-runbook) a **Namespacename tulajdonság** és a **resourceGroupName** paraméterek értékeinek megadásával. Győződjön meg arról, hogy a névtérben lévő üzenetkezelési egységek frissülnek. 
3. Miután sikeresen elvégezte a tesztelést, [tegye közzé a munkafüzetet](..//automation/manage-runbooks.md#publish-a-runbook) , hogy az elérhető legyen, hogy műveletként hozzá lehessen adni egy riasztást a névtérhez később. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Riasztás létrehozása a névtéren a runbook elindításához
A létrehozott Automation-runbook elindításához tekintse Service Bus meg a [riasztások használata Azure Automation runbook elindításához](../automation/automation-create-alert-triggered-runbook.md) című cikket. Létrehozhat például egy riasztást a **CPU-használaton névtér** vagy a **memória mérete** alapján, és hozzáadhat egy műveletet a létrehozott Automation-runbook elindításához. A metrikákkal kapcsolatos további információkért lásd: [Erőforrás-használati metrikák](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

Az alábbi eljárás bemutatja, hogyan hozhat létre olyan riasztást, amely elindítja az Automation runbook, amikor a névtér **CPU-használata** a **75%** fölé kerül.

1. A névtér **Service Bus névtér** lapján válassza a bal oldali menüben a **riasztások** elemet, majd az eszköztáron válassza az **+ új riasztási szabály** lehetőséget. 
    
    ![Riasztások lap – új riasztási szabály gomb](./media/automate-update-messaging-units/alerts-page.png)
2. A **riasztási szabály létrehozása** lapon kattintson a **feltétel kiválasztása**elemre. 

    ![Riasztási szabály létrehozása lap – feltétel kiválasztása](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. A **jel logikai beállítása** lapon válassza a **CPU** lehetőséget a jel számára. 

    ![Jel logikai beállítása – a CPU kiválasztása](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Adja meg a **küszöbértéket** (ebben a példában ez **75%**), majd válassza a **kész**lehetőséget. 

    ![A CPU-jel konfigurálása](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Most a **riasztás létrehozása lapon**kattintson a **műveleti csoport kiválasztása**elemre.
    
    ![Műveleti csoport kiválasztása](./media/automate-update-messaging-units/select-action-group-button.png)
6. Válassza a **műveleti csoport létrehozása** gombot az eszköztáron. 

    ![Műveleti csoport létrehozása gomb](./media/automate-update-messaging-units/create-action-group-button.png)
7. A **műveleti csoport hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg a műveleti csoport **nevét** . 
    2. Adja meg a műveleti csoport **rövid nevét** .
    3. Válassza ki azt az **előfizetést** , amelyben létre kívánja hozni ezt a műveleti csoportot.
    4. Válassza ki az **erőforráscsoportot**. 
    5. A **műveletek** szakaszban adja meg **a művelet nevét**, majd válassza az **Automation Runbook** lehetőséget a **művelet típusához**. 

        ![Műveleti csoport hozzáadása lap](./media/automate-update-messaging-units/add-action-group-page.png)
8. A **Runbook konfigurálása** lapon hajtsa végre a következő lépéseket:
    1. A **Runbook forrásnál**válassza a **felhasználó**lehetőséget. 
    2. Az **előfizetés**mezőben válassza ki az Automation-fiókot tartalmazó Azure- **előfizetést** . 
    3. Az **Automation-fiókhoz**válassza ki az **Automation-fiókját**.
    4. A **Runbook**válassza ki a Runbook. 
    5. A **Runbook konfigurálása** lapon kattintson **az OK gombra** . 
        ![Runbook konfigurálása](./media/automate-update-messaging-units/configure-runbook.png)
9. A **műveleti csoport hozzáadása** lapon kattintson **az OK gombra** . 
5. Most a **riasztási szabály létrehozása** lapon adja meg **a szabály nevét**, majd válassza a **riasztási szabály létrehozása**lehetőséget. 
    ![Riasztási szabály létrehozása](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Ha a névtér CPU-használata a 75-nál nagyobb, akkor a riasztás elindítja az Automation runbook, ami növeli az Service Bus névtér üzenetkezelési egységeit. Hasonlóképpen létrehozhat egy riasztást egy másik Automation-runbook is, amely csökkenti az üzenetkezelési egységeket, ha a névtér CPU-kihasználtsága 25 alá kerül. 

## <a name="next-steps"></a>További lépések
Az üzenetkezelési egységekről a prémium szintű [üzenetkezelés](service-bus-premium-messaging.md) című témakörben olvashat bővebben.
