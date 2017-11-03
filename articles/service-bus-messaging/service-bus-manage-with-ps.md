---
title: "Azure Service Bus-erőforrások kezelése a PowerShell használatával |} Microsoft Docs"
description: "PowerShell-modul segítségével hozza létre, és a Service Bus-erőforrások kezelése"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 1520cea72069be5d7bb078a3770d61fade26b853
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>A Service Bus-erőforrások kezelése a PowerShell használatával

A Microsoft Azure PowerShell egy parancsfájl-kezelési környezet, melyekkel automatizálhatja a központi telepítési és felügyeleti Azure-szolgáltatáshoz, és szabályozhatja. Ez a cikk ismerteti, hogyan használható a [Service Bus Resource Manager PowerShell-modul](/powershell/module/azurerm.servicebus) szeretnék telepíteni és kezelni a Service Bus-entitások (névterek, üzenetsorok, témakörök és előfizetések) a helyi Azure PowerShell-konzolt vagy parancsfájl segítségével.

Service Bus-entitások Azure Resource Manager-sablonok segítségével is kezelheti. További információkért lásd: a cikk [Azure Resource Manager-sablonok létrehozása a Service Bus-erőforrások](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz a következő előfeltételek teljesülését:

* Azure-előfizetés. Előfizetés beszerzésével kapcsolatos további információkért lásd: [beszerzési lehetőségek][purchase options], [ajánlatok][member offers], vagy [ingyenes fiókot][free account].
* Az Azure PowerShell számítógép. Útmutatásért lásd: [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps).
* A PowerShell-parancsfájlok, NuGet-csomagok és a .NET-keretrendszer általános ismertetése.

## <a name="get-started"></a>Bevezetés

Az első lépés-e jelentkezni az Azure-fiókot és az Azure-előfizetés a PowerShell használatával. Kövesse az utasításokat a [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps) jelentkezzen be az Azure-fiókjával, és lekérésére és hozzáférés az erőforrásokhoz az Azure-előfizetéshez.

## <a name="provision-a-service-bus-namespace"></a>A Service Bus-névtér kiépítése

A Szolgáltatásbusz-névterek használatakor is használhatja a [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), és [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) parancsmagok.

Ez a példa néhány helyi változók hoz létre a parancsfájl; `$Namespace` és `$Location`.

* `$Namespace`a Service Bus-névtér, amellyel működéséhez szeretnénk neve van.
* `$Location`a névtér azt kiépítése adatközpont azonosítja.
* `$CurrentNamespace`a referencia-névtér, amely azt lekérése (vagy hozzon létre) tárolja.

Egy tényleges parancsfájlban `$Namespace` és `$Location` paraméterként kell.

Ez a kijelző, a parancsfájl a következőket teszi:

1. A rendszer megkísérli lekérni a Service Bus-névtér a megadott névvel.
2. Ha a névtérben található, mi található jelenti.
3. Ha a névtér nem található, akkor hoz létre a névteret, és az újonnan létrehozott névtér ezután lekéri.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Egy névtér engedélyezési szabály létrehozása

A következő példa bemutatja, hogyan névtér engedélyezési szabályok segítségével kezelheti a [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), és [Remove-AzureRmServiceBusNamespaceAuthorizationRule parancsmagok](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Üzenetsor vagy témakör létrehozásához hajtsa végre a névtér-ellenőrzés az előző szakaszban a parancsfájl használatával. Ezután hozzon létre a várólistára:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Várólista-tulajdonságok módosítása

A parancsfájl végrehajtása az előző szakaszban leírt, után is használhatja a [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) parancsmag használatával, az alábbi példában látható módon a várólista tulajdonságainak frissítéséhez:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Kiépítés más Service Bus-entitások

Használhatja a [Service Bus PowerShell modul](/powershell/module/azurerm.servicebus) egyéb entitások, például az üzenettémák és előfizetések kiépítéséhez. Ezek a parancsmagok szintaktikailag hasonlóak a várólista létrehozása parancsmagok az előző szakaszban bemutatott.

## <a name="next-steps"></a>Következő lépések

- A teljes Service Bus Resource Manager PowerShell modul dokumentációjában [Itt](/powershell/module/azurerm.servicebus). Ezen a lapon az összes elérhető parancsmagok sorolja fel.
- Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: a cikk [Azure Resource Manager-sablonok létrehozása a Service Bus-erőforrások](service-bus-resource-manager-overview.md).
- Információ a [Service Bus .NET kezelési kódtárakat](service-bus-management-libraries.md).

Néhány alternatív módja van kezelése a Service Bus-entitások, ezek a blogbejegyzések leírtak szerint:

* [A Service Bus üzenetsorok, témakörök és előfizetések egy PowerShell-parancsfájl használatával létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [A Service Bus Namespace és a PowerShell parancsfájl használatával Eseményközpont létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell parancsfájlok](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
