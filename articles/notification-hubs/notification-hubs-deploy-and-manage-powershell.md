---
title: A Notification Hubs telepítése és kezelése a PowerShell-lel
description: Létrehozása és kezelése a PowerShell használatával az automatizáláshoz a Notification Hubs
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
ms.openlocfilehash: d2350d8021925278d6362c8227d408476a569319
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776906"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>A Notification Hubs telepítése és kezelése a PowerShell-lel
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan használja a létrehozása és kezelése Azure Notification Hubs PowerShell használatával. Ez a témakör a következő általános automatizálási feladatok láthatók.

* Egy értesítési központ létrehozása
* A hitelesítő adatokat

Ha is szeretne létrehozni egy új service bus-névtér esetében a notification hubs használatával, lásd: [Service Bus kezelése a PowerShell-lel](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Értesítések hubok kezelése nem támogatja közvetlenül az Azure PowerShell parancsmagok. A legjobb módszer a PowerShell-hoz a Microsoft.Azure.NotificationHubs.dll szerelvény hivatkozik. A szerelvény terjeszthető a [Microsoft Azure Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Azure előfizetés-alapú platform. Előfizetés beszerzésével kapcsolatos további információkért lásd: [megvásárlási lehetőségeinek], [ajánlatok], vagy [ingyenes].
* Az Azure PowerShell számítógép. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt].
* A PowerShell-parancsfájlok, NuGet-csomagok és a .NET-keretrendszer általános ismertetése.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Beleértve a .NET-szerelvény Service Bus
Azure Notification Hubs kezelése még nincs a PowerShell-parancsmagok az Azure PowerShell tartalmazza. A notification hubs telepítéséhez, használhatja a .NET-ügyfél a megadott a [Microsoft Azure Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Először győződjön meg arról, hogy a parancsfájl keresheti meg a **Microsoft.Azure.NotificationHubs.dll** szerelvény, ami a Visual Studio-projektet a NuGet csomag telepítve van. Ahhoz, hogy a rugalmas, a parancsfájl ezeket a lépéseket hajtja végre:

1. Meghatározza, hogy az elérési utat, ahol azt lett meghívva.
2. Az elérési út jár, amíg nem talál egy nevű mappát `packages`. A mappa létrehozása a Visual Studio-projektek NuGet-csomagok telepítésekor.
3. Rekurzív keresést a `packages` egy összeállítás nevű mappa **Microsoft.Azure.NotificationHubs.dll**.
4. A szerelvényre hivatkozik, hogy a típusok későbbi használatra.

Ez hogyan valósíthatók meg ezeket a lépéseket egy PowerShell-parancsfájlt:

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

## <a name="create-the-namespacemanager-class"></a>A NamespaceManager osztály létrehozása
Notification Hubs kiépítéséhez, hozzon létre egy példányát a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) osztályt az SDK-ból. 

Használhatja a [Get-AzureSBAuthorizationRule] parancsmag részét képező Azure PowerShell beolvasni egy engedélyezési szabályt, amellyel meg egy kapcsolati karakterláncot. Egy hivatkozást a `NamespaceManager` példány tárolja a `$NamespaceManager` változó. `$NamespaceManager` egy értesítési központ létrehozásához használt.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Új értesítési központ kiépítése
Új értesítési központ létrehozásához használja a [.NET API-t a Notification Hubs].

Ez a parancsfájl részében beállította négy helyi változók. 

1. `$Namespace` : Válassza az a névtér neve hol szeretne létrehozni egy értesítési központot.
2. `$Path` : Állítsa be ezt az elérési utat az új értesítési központ nevét.  Például "MyHub."    
3. `$WnsPackageSid` : A fájl hozzáadása után a csomag biztonsági AZONOSÍTÓJÁT, a Windows-alkalmazás a a [Windows fejlesztői központ](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Állítsa a titkos kulcsot a Windows-alkalmazás a [Windows fejlesztői központ](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Ezek a változók csatlakozni a névtérhez, és hozzon létre egy új értesítési központ konfigurálva a Windows értesítési szolgáltatások (WNS) értesítések WNS hitelesítő adatokkal rendelkező Windows-alkalmazás segítségével. Információ lekérése a csomag biztonsági azonosítója és a titkos kulcs lásd: a [Ismerkedés a Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) oktatóanyag. 

* A parancsfájl kódrészletet használja a `NamespaceManager` objektum ellenőrizze, hogy ha az értesítési központ által azonosított `$Path` létezik-e.
* Ha még nem létezik, a parancsfájl létrehoz `NotificationHubDescription` WNS-sel való hitelesítő adatait, és adja át, hogy a `NamespaceManager` osztály `CreateNotificationHub` metódust.

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
* [A PowerShell használatával a Service Bus kezelése](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [A Service Bus üzenetsorok, témakörök és előfizetések egy PowerShell-parancsfájl használatával létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [A Service Bus Namespace és a PowerShell parancsfájl használatával Eseményközpont létrehozása](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Néhány előre elkészített parancsfájlok letölthetők is:

* [Service Bus PowerShell parancsfájlok](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[megvásárlási lehetőségeinek]: http://azure.microsoft.com/pricing/purchase-options/
[ajánlatok]: http://azure.microsoft.com/pricing/member-offers/
[ingyenes]: http://azure.microsoft.com/pricing/free-trial/
[telepítse és konfigurálja az Azure Powershellt]: /powershell/azureps-cmdlets-docs
[.NET API-t a Notification Hubs]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx

