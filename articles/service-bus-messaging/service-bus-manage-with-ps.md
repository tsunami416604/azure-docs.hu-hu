---
title: Az Azure Service Bus erőforrásainak kezelése a PowerShell használatával |} A Microsoft Docs
description: PowerShell-modul segítségével létrehozhat, és a Service Bus erőforrásainak kezelése
services: service-bus-messaging
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: cc15947c5ed06d85d95d12aa448624533dc292d3
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393144"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Service Bus erőforrásainak kezelése a PowerShell használatával

A Microsoft Azure PowerShell-parancsfájl-kezelési környezet, amellyel szabályozhatja és automatizálhatja a telepítése és felügyelete az Azure-szolgáltatások. Ez a cikk ismerteti, hogyan használható a [Service Bus Resource Manager PowerShell-modul](/powershell/module/azurerm.servicebus) történő kialakításához és kezeléséhez a Service Bus-entitások (névterek, üzenetsorok, témakörök és előfizetések) egy helyi Azure PowerShell-konzolt vagy parancsfájl használatával.

Service Bus-entitások Azure Resource Manager-sablonok használatával is kezelheti. További információkért tekintse meg a cikket [Azure Resource Manager-sablonok létrehozása a Service Bus-erőforrások](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz a következő előfeltételek vonatkoznak:

* Azure-előfizetés. Előfizetés beszerzésével kapcsolatos további információkért lásd: [vásárlási lehetőségek][purchase options], [ajánlatok tagoknak][member offers], vagy [ingyenes fiók][free account].
* Egy számítógép az Azure PowerShell használatával. Útmutatásért lásd: [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps).
* PowerShell-parancsfájlok NuGet-csomagok és a .NET-keretrendszer általános ismeretekkel.

## <a name="get-started"></a>Bevezetés

Az első lépés, hogy jelentkezzen be az Azure-fiók és az Azure-előfizetést a PowerShell használatával. Kövesse a [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/get-started-azureps) jelentkezzen be az Azure-fiókjával, és a lekérésére és elérhessék az erőforrásokat az Azure-előfizetésében.

## <a name="provision-a-service-bus-namespace"></a>Service Bus-névtér kiépítése

Amikor a Service Bus-névterek dolgozik, használhatja a [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), és [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) parancsmagok.

Ez a példa létrehoz néhány helyi változók a parancsfájl; `$Namespace` és `$Location`.

* `$Namespace` a Service Bus-névtér, amellyel működéséhez szeretnénk neve van.
* `$Location` Adja meg az adatközpontot, amelyben a névtér kiépítve.
* `$CurrentNamespace` a referencia-névteret, amelyet tudjuk lekérdezni (vagy hozzon létre) tárolja.

Egy tényleges szkriptben `$Namespace` és `$Location` paraméterként adhatók.

A parancsfájl ebben a részében a következőket teszi:

1. Próbálja meg lekérni a Service Bus-névtér, a megadott néven.
2. Ha a névtérben található, jelentések, mi található.
3. Ha a névtérben nem található, a névteret hoz létre, és az újonnan létrehozott névtér ezután lekéri.
   
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

### <a name="create-a-namespace-authorization-rule"></a>Egy névtér-engedélyezési szabály létrehozása

Az alábbi példa bemutatja, hogyan kezelheti a névtér-engedélyezési szabályok használatával a [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), és [Remove-AzureRmServiceBusNamespaceAuthorizationRule parancsmagok](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

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
    New-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Egy üzenetsor vagy témakör létrehozásához hajtsa végre az előző szakaszban a szkripttel névtér ellenőrzése. Ezután hozzon létre a várólista:

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

### <a name="modify-queue-properties"></a>Várólista tulajdonságainak módosítása

Az előző szakaszban a parancsfájl végrehajtása után használhatja a [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) parancsmag egy üzenetsorba, a következő példához hasonlóan tulajdonságainak frissítésére:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Kiépítés a többi Service Bus-entitások

Használhatja a [Service Bus PowerShell-modul](/powershell/module/azurerm.servicebus) egyéb entitások, például a témakörök és előfizetések kiépítéséhez. Ezek a parancsmagok szintaktikailag hasonlóak az előző szakaszban bemutatott várólista létrehozására szolgáló parancsmagokhoz.

## <a name="next-steps"></a>További lépések

- A teljes Service Bus Resource Manager PowerShell-modul dokumentációjában [Itt](/powershell/module/azurerm.servicebus). Ezen a lapon az összes elérhető parancsmagjainak listája.
- Az Azure Resource Manager-sablonok használatával kapcsolatos információkért tekintse meg a cikket [Azure Resource Manager-sablonok létrehozása a Service Bus-erőforrások](service-bus-resource-manager-overview.md).
- Információ a [Service Bus .NET kezelési kódtárak](service-bus-management-libraries.md).

Ezek a blogbejegyzések leírtak szerint, van néhány alternatív módszer a Service Bus-entitások kezelését:

* [Hogyan hozhat létre a Service Bus-üzenetsorokat, üzenettémákat és előfizetéseket, egy PowerShell-parancsfájl használatával](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [A Service Bus-Namespace és a egy Eseményközpontba egy PowerShell-parancsprogram létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell-parancsprogramok](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
