---
title: Értesítési központok telepítése és kezelése a PowerShell használatával
description: Értesítési központok létrehozása és kezelése az Automatizáláshoz való PowerShell használatával
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264644"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Értesítési központok telepítése és kezelése a PowerShell használatával

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja az Azure értesítési központok létrehozása és kezelése a PowerShell használatával. A következő gyakori automatizálási feladatok jelennek meg ebben a cikkben.

- Értesítési központ létrehozása
- Hitelesítő adatok beállítása

Ha új szolgáltatásbusz-névteret is létre kell hoznia az értesítési központokhoz, olvassa [el a Service Bus kezelése a PowerShell használatával című témakört.](../service-bus-messaging/service-bus-powershell-how-to-provision.md)

Az Értesítési központok kezelését nem támogatja közvetlenül az Azure PowerShell ben található parancsmagok. A PowerShell legjobb megközelítése a Microsoft.Azure.NotificationHubs.dll szerelvényhivatkozás. A szerelvény a [Microsoft Azure Notification Hubs NuGet csomaggal kerül terjesztésre.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Az Azure egy előfizetés-alapú platform. Az előfizetés beszerzéséről további információt a [Vásárlási lehetőségek], [a Tagajánlatok]vagy [az Ingyenes próbaverzió]című témakörben talál.
- Egy Azure PowerShellt rendelkező számítógép. További információt az [Azure PowerShell telepítése és konfigurálása]című témakörben talál.
- A PowerShell-parancsfájlok, a NuGet-csomagok és a .

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Hivatkozás a Service Bus .NET szerelvényére

Az Azure Értesítési központ kezelése még nem szerepel az Azure PowerShell PowerShell Ben található PowerShell-parancsmagok között. Az értesítési központok kiépítéséhez használhatja a [Microsoft Azure Notification Hubs NuGet csomagban](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)biztosított .NET-ügyfelet.

Először győződjön meg arról, hogy a parancsfájl meg tudja találni a **Microsoft.Azure.NotificationHubs.dll** szerelvényt, amely NuGet-csomagként van telepítve egy Visual Studio-projektben. A rugalmas munkavégzés érdekében a parancsfájl a következő lépéseket hajtja végre:

1. Azt az elérési utat határozza meg, amelyen a meghívás tanéven.
2. Addig járja be az elérési `packages`utat, amíg meg nem találja a nevű mappát. Ez a mappa akkor jön létre, amikor NuGet csomagokat telepít Visual Studio-projektekhez.
3. Rekurzív módon megkeresi `packages` a mappát `Microsoft.Azure.NotificationHubs.dll`egy nevű szerelvényre.
4. A kódösszeállításra hivatkozik, hogy a típusok későbbi használatra is elérhetők.

A következő lépések egy PowerShell-parancsfájlban vannak megvalósítva:

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

## <a name="create-the-namespacemanager-class"></a>Az `NamespaceManager` osztály létrehozása

Értesítési központok kiépítése, hozzon létre egy példányt a NamespaceManager osztály az SDK.To provision Notification Hubs, create a instance of the [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) class from the SDK.

Az Azure PowerShell részét képező [Get-AzureSBAuthorizationRule] parancsmag használatával lekérheti a kapcsolati karakterlánc biztosításához használt engedélyezési szabályt. A `NamespaceManager` példányra mutató hivatkozás a `$NamespaceManager` változóban tárolódik. `$NamespaceManager`az értesítési központ kiépítéséhez szolgál.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Új értesítési központ kiépítése

Új értesítési központ kiépítéséhez használja a [.NET API-t az értesítési központokhoz.]

A parancsfájl nak ebben a részében négy helyi változót állíthat be.

1. `$Namespace`: Állítsa be ezt annak a névtérnek a nevére, ahol értesítési központot szeretne létrehozni.
2. `$Path`: Állítsa be ezt az elérési utat az új értesítési központ nevére.  Például "MyHub".
3. `$WnsPackageSid`: Állítsa be ezt a Windows alkalmazás biztonsági azonosítójának csomagjára a [Windows fejlesztői központból](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Állítsa ezt a Windows alkalmazás titkos kulcsára a [Windows fejlesztői központból](https://developer.microsoft.com/en-us/windows).

Ezek a változók a névtérhez való csatlakozásra és a Windows értesítési szolgáltatások (WNS) értesítéseinek wns-hitelesítő adatokkal történő kezelésére konfigurált új értesítési központ létrehozására szolgálnak. A csomag BIZTONSÁGI AZONOSÍTÓjának és titkos kulcsának beszerzéséről az Első lépések az [értesítési központokkal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag című témakörben talál további információt.

- A parancsfájlkódrészlet az `NamespaceManager` objektum segítségével ellenőrzi, hogy az `$Path` értesítési központ által azonosított található-e.
- Ha nem létezik, a `NotificationHubDescription` parancsfájl wns hitelesítő adatokkal `NamespaceManager` hoz `CreateNotificationHub` létre, és átadja azt az osztálymetódusnak.

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

- [A Service Bus kezelése a PowerShell használatával](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [A Service Bus-várólisták, témakörök és előfizetések létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Service Bus-névtér és eseményközpont létrehozása PowerShell-parancsfájl használatával](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Néhány kész szkript is letölthető:

- [Service Bus PowerShell-parancsfájlok](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Vásárlási lehetőségek]: https://azure.microsoft.com/pricing/purchase-options/
[Tagajánlatok]: https://azure.microsoft.com/pricing/member-offers/
[Ingyenes próba]: https://azure.microsoft.com/pricing/free-trial/
[Az Azure PowerShell telepítése és konfigurálása]: /powershell/azureps-cmdlets-docs
[.NET API értesítési központokhoz]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
