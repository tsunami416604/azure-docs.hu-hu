---
title: A Notification Hubs telepítése és kezelése a PowerShell-lel
description: Hogyan hozhat létre és kezelheti a Notification Hubs, az Automation PowerShell-lel
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 8312841b5f8e4b9979de8abf32ce1009b4243f06
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388160"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>A Notification Hubs telepítése és kezelése a PowerShell-lel
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan használható létrehozása és kezelése az Azure Notification Hubs PowerShell-lel. Ez a cikk a következő gyakori automatizálási feladatok láthatók.

* Értesítési központ létrehozása
* Hitelesítő adatok beállítása

Ha is szeretne létrehozni egy új service bus-névtér a notification hubs szolgáltatásban, lásd: [a Service Bus kezelése a PowerShell-lel](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Értesítési központ kezelése közvetlenül az Azure PowerShell-parancsmagok által nem támogatott. A PowerShellben a legjobb módja, hogy a Microsoft.Azure.NotificationHubs.dll szerelvény hivatkozik. A szerelvény terjeszthető a [a Microsoft Azure Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Az Azure az előfizetés-alapú platform. Előfizetés beszerzésével kapcsolatos további információkért lásd: [vásárlási lehetőségek], [ajánlatok tagoknak], vagy [ingyenes próbaverzió].
* Egy számítógép az Azure PowerShell használatával. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt].
* PowerShell-parancsfájlok NuGet-csomagok és a .NET-keretrendszer általános ismeretekkel.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Service Bus számára, a .NET-szerelvény hivatkozást
Azure Notification Hubs kezelése még nem áll a PowerShell-parancsmagok az Azure PowerShell. Hozza létre a notification hubs használatával, használhatja a .NET ügyféloldali megadott a [a Microsoft Azure Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Győződjön meg róla, keresse meg azt a parancsfájlt a **Microsoft.Azure.NotificationHubs.dll** szerelvény, amennyiben van telepítve, mint a Visual Studio-projekt NuGet-csomagot. Ahhoz, hogy rugalmas, a parancsfájl ezeket a lépéseket hajtja végre:

1. Meghatározza, hogy az elérési utat, amelyen meghívása.
2. Amíg nem talál egy nevű mappát is az útvonalat járja `packages`. Ebben a mappában jön létre a Visual Studio projekteket NuGet-csomag telepítésekor.
3. Rekurzív keresést a `packages` nevű szerelvény mappáját **Microsoft.Azure.NotificationHubs.dll**.
4. A szerelvény hivatkozik, így a típusok későbbi használatra érhetők el.

Itt látható, hogyan találhatók meg ezeket a lépéseket egy PowerShell-parancsfájlt:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Hozzon létre a NamespaceManager osztályról
A Notification Hubs kiépítéséhez, hozzon létre egy példányt a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) osztályt az SDK-t. 

Használhatja a [Get-AzureSBAuthorizationRule] parancsmagot az Azure PowerShell-lel beolvasni egy engedélyezési szabályt, amellyel meg egy kapcsolati karakterláncot tartalmazza. Egy hivatkozást a `NamespaceManager` példány tárolja a `$NamespaceManager` változó. `$NamespaceManager` egy értesítési központ kiépítéséhez használatos.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Új értesítési központ kiépítése
Üzembe helyez egy új értesítési központ, használja a [A Notification Hubs .NET API-val].

A parancsfájl ezen részében beállított négy helyi változók. 

1. `$Namespace`: Állítsa ezt a beállítást a névtér neve kívánja egy értesítési központ létrehozása.
2. `$Path`: Állítsa ezt az elérési utat az új értesítési központ nevét.  Például "MyHub."    
3. `$WnsPackageSid`: Állítsa ezt a beállítást a csomag biztonsági AZONOSÍTÓJÁT, a Windows-alkalmazáshoz, a [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Állítsa ezt a beállítást a titkos kulcs for a Windows-alkalmazást a [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Ezeket a változókat a névtér csatlakozik, és hozzon létre egy új értesítési központ konfigurálva a Windows értesítési szolgáltatása (WNS) értesítések WNS hitelesítő adatokkal rendelkező Windows-alkalmazás kezeléséhez használhatók. A csomag beszerzésével kapcsolatos információért biztonsági azonosítója és a titkos kulcs tekintse meg a [Ismerkedés a Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag. 

* A parancsfájl kódrészlet a `NamespaceManager` objektum, ellenőrizze, hogy ha az értesítési központ által azonosított `$Path` létezik.
* Ha még nem létezik, a szkript létrehoz `NotificationHubDescription` WNS-sel hitelesítő adatait, és azt a `NamespaceManager` osztály `CreateNotificationHub` metódust.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>További források
* [A Service Bus kezelése a PowerShell használatával](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [Hogyan hozhat létre a Service Bus-üzenetsorokat, üzenettémákat és előfizetéseket, egy PowerShell-parancsfájl használatával](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [A Service Bus-Namespace és a egy Eseményközpontba egy PowerShell-parancsprogram létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Bizonyos előre elkészített parancsfájlok letölthető is érhetők el:

* [Service Bus PowerShell-parancsprogramok](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Vásárlási lehetőségek]: http://azure.microsoft.com/pricing/purchase-options/
[Ajánlatok tagoknak]: http://azure.microsoft.com/pricing/member-offers/
[Ingyenes próbaverzió]: http://azure.microsoft.com/pricing/free-trial/
[Telepítse és konfigurálja az Azure PowerShellt]: /powershell/azureps-cmdlets-docs
[A Notification Hubs .NET API-val]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule

