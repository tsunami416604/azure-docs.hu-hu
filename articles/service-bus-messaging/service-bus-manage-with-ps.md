---
title: Az Azure Service Bus-erőforrások kezelése a PowerShell használatával | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan azure PowerShell-modul használatával hozzon létre és kezelje a Service Bus-entitások (névterek, várólisták, témakörök, előfizetések).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759261"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>A PowerShell használata a Service Bus-erőforrások kezeléséhez

A Microsoft Azure PowerShell egy parancsfájl-kezelési környezet, amely segítségével szabályozhatja és automatizálhatja az Azure-szolgáltatások üzembe helyezését és felügyeletét. Ez a cikk azt ismerteti, hogy a [Service Bus Resource Manager PowerShell modul](/powershell/module/az.servicebus) használatával hogyan építheti ki és kezelheti a Service Bus-entitásokat (névterek, várólisták, témakörök és előfizetések) egy helyi Azure PowerShell-konzol vagy -parancsfájl használatával.

A Service Bus-entitásokat az Azure Resource Manager-sablonok használatával is kezelheti. További információt a [Service Bus-erőforrások létrehozása az Azure Resource Manager-sablonok használatával című témakörben talál.](service-bus-resource-manager-overview.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Az előfizetés beszerzésével kapcsolatos további információkért [lásd: Vásárlási lehetőségek][purchase options], [tagajánlatok][member offers]vagy [ingyenes fiók.][free account]
* Egy Azure PowerShellt rendelkező számítógép. További információt az [Azure PowerShell-parancsmagok – első lépések](/powershell/azure/get-started-azureps).
* A PowerShell-parancsfájlok, a NuGet-csomagok és a .

## <a name="get-started"></a>Bevezetés

Az első lépés a PowerShell használata az Azure-fiókba és az Azure-előfizetésbe való bejelentkezéshez. Kövesse az [Azure PowerShell-parancsmagok első lépései](/powershell/azure/get-started-azureps) című útmutató utasításait az Azure-fiókjába való bejelentkezéshez, valamint az Azure-előfizetés erőforrásainak beolvasásához és eléréséhez című útmutatóban.

## <a name="provision-a-service-bus-namespace"></a>Service Bus-névtér kiépítése

A Service Bus névtereivel végzett munka során használhatja a [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)és [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) parancsmagokat.

Ez a példa néhány helyi változót hoz létre a parancsfájlban; `$Namespace` és `$Location`.

* `$Namespace`annak a Service Bus névtérnek a neve, amellyel dolgozni szeretnénk.
* `$Location`azonosítja azt az adatközpontot, amelyben a névteret kiépítjük.
* `$CurrentNamespace`tárolja az általunk beolvasott (vagy létrehozási) referencianévteret.

Egy tényleges parancsfájlban, `$Namespace` és `$Location` paraméterekként is átadhatók.

A szkript nek ez a része a következőket teszi:

1. Megkísérli a megadott nevű Service Bus-névtér beolvasását.
2. Ha a névtér megtalálható, akkor jelenti a talált jelentést.
3. Ha a névtér nem található, létrehozza a névteret, majd beolvassa az újonnan létrehozott névteret.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Névtérengedélyezési szabály létrehozása

A következő példa bemutatja, hogyan kezelhetők a névtér engedélyezési szabályai a [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)és [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) parancsmagokkal.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Várólista vagy témakör létrehozásához hajtson végre névtérellenőrzést az előző szakaszparancsfájljával. Ezután hozza létre a várólistát:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Várólista tulajdonságainak módosítása

Az előző szakaszban végrehajtott parancsfájl végrehajtása után a [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) parancsmag segítségével frissítheti egy várólista tulajdonságait, ahogy az a következő példában látható:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Egyéb Service Bus-entitások kiépítése

A Service [Bus PowerShell modul](/powershell/module/az.servicebus) használatával más entitásokat, például témaköröket és előfizetéseket is kiépíthet. Ezek a parancsmagok szintaktikailag hasonlóak az előző szakaszban bemutatott várólista-létrehozási parancsmagokhoz.

## <a name="next-steps"></a>További lépések

- A Service Bus Resource Manager PowerShell-modul teljes [dokumentációját itt](/powershell/module/az.servicebus)találja. Ez a lap az összes elérhető parancsmavet megjeleníti.
- Az Azure Resource Manager-sablonok használatáról a [Service Bus-erőforrások létrehozása az Azure Resource Manager-sablonok használatával](service-bus-resource-manager-overview.md)című témakörben olvashat.
- Információ a [Service Bus .NET felügyeleti tárakról](service-bus-management-libraries.md).

A Service Bus-entitások kezelésének néhány alternatív módja is van, az alábbi blogbejegyzésekben leírtak szerint:

* [A Service Bus-várólisták, témakörök és előfizetések létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Service Bus-névtér és eseményközpont létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell-parancsfájlok](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
