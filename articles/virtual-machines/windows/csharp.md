---
title: Létrehozása és kezelése az Azure virtuális gép, C# használatával |} A Microsoft Docs
description: A C# és a egy virtuális gép és a támogató erőforrások üzembe helyezése Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: ce05d097aa69aa1aadb8450e40722448bc5a7de0
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883058"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Létrehozása és kezelése Windows virtuális gépek az Azure-ban C# használatával #

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrásokat kell. Ez a cikk ismerteti a létrehozását, kezelését és C# használatával Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * A csomag telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Körülbelül 20 perc alatt elvégezheti ezeket a lépéseket vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **.NET asztali fejlesztés** a számítási feladatok lapján, és kattintson a **telepítése**. A fentieket összegezve láthatja, hogy **.NET-keretrendszer 4-4.6 Fejlesztőeszközök** automatikusan ki van jelölve az Ön számára. Ha már telepítette a Visual Studio, a .NET-munkaterhelés használatával a Visual Studio indítója is hozzáadhat.
2. A Visual Studióban kattintson a **File (Fájl)** > **New (Új)** > **Project (Projekt)** parancsra.
3. A **sablonok** > **Visual C#** válassza **Console App (.NET Framework)**, adja meg *myDotnetProject* neve a projektre, válassza ki a projekt helyét, majd kattintson **OK**.

## <a name="install-the-package"></a>A csomag telepítése

NuGet-csomagok a legegyszerűbben úgy, hogy először fejezze be a következő lépéseket a kódtárak telepítése. A Visual Studio szükséges kódtárakat, tegye ezeket a lépéseket:

1. Kattintson a **eszközök** > **Nuget-Csomagkezelő**, és kattintson a **Package Manager Console**.
2. Ezt a parancsot a konzolon:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Ebben a lépésben a Kezdés előtt győződjön meg arról, hogy rendelkezik-e a hozzáférést egy [Active Directory egyszerű szolgáltatás](../../active-directory/develop/howto-create-service-principal-portal.md). Egy későbbi lépésben is az Alkalmazásazonosítót, a hitelesítési kulcsot és a bérlő Azonosítóját kell kell rögzíteni.

### <a name="create-the-authorization-file"></a>Az engedélyezési-fájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C#-elemek*. A fájl neve *azureauth.properties*, és kattintson a **Hozzáadás**.
2. Adja hozzá ezeket a hitelesítési tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Cserélje le **&lt;előfizetés-azonosító&gt;** az előfizetés-azonosítójú **&lt;alkalmazásazonosító&gt;** az Active Directory-alkalmazással azonosító, **&lt;hitelesítési kulcs&gt;** az alkalmazás kulccsal és **&lt;bérlőazonosító&gt;** a bérlői azonosító.

3. Mentse a azureauth.properties fájlt. 
4. A Windows engedélyezési létrehozott fájl teljes elérési útja AZURE_AUTH_LOCATION nevű környezeti változó értéke. Ha például a következő PowerShell-parancs használható:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekt a Program.cs fájlt, és adja hozzá ezek using utasításokat a meglévő utasításokat a fájl felül:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a Main metódushoz:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Minden erőforrás tartalmaznia kell egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md).

Adja meg az értékeket az alkalmazás és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>A rendelkezésre állási csoport létrehozása

[A rendelkezésre állási csoportok](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépek, amelyet az alkalmazás karbantartása.

A rendelkezésre állási csoport létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása

A [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) kommunikálni a virtuális gép van szükség.

A virtuális gép nyilvános IP-cím létrehozásához adja hozzá ezt a kódot a Main metódushoz:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Virtuális gép, egy alhálózaton kell lennie egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozzon létre egy alhálózatot és egy virtuális hálózatot, adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása

A hálózati adaptert a virtuális hálózaton kommunikálni valamelyik virtuális gépnél.

Hozzon létre egy hálózati adaptert, adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

A támogató erőforrások létrehozása, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verzióját futtatja. További rendszerképeket kiválasztásával kapcsolatos további tudnivalókért lásd: [és a Windows PowerShell és az Azure CLI Azure-beli virtuálisgép-rendszerképek kiválasztása](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Ha szeretné használni a meglévő lemez Piactéri lemezképet helyett, akkor használja ezt a kódot:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül előfordulhat, hogy szeretne létrehozni a kód ismétlődő vagy bonyolult feladatok automatizálására.

Semmit a virtuális gép van szüksége, ha le szeretné kérni egy példányát:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak lekérése

A virtuális gép adatainak lekérése, adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális gép leállítása és tartsa az összes beállítást, de továbbra is kell fizetnie, vagy egy virtuális gép leállítása és felszabadítása azt. Ha egy virtuális gép fel van szabadítva, az ahhoz kapcsolódó összes erőforrás is felszabadított és számlázási végpontjainak lesznek.

Állítsa le a virtuális gép felszabadítása, anélkül, hogy adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Szabadítsa fel a virtuális gépet szeretne, ha módosítsa ezt a kódot kikapcsolt meghívásához:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

Indítsa el a virtuális gépet, adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

Bizonyos szempontokból az üzembe helyezés során a megfelelő méret a virtuális gép kell tekinteni. További információkért lásd: [Virtuálisgép-méretek](sizes.md).  

Ha módosítani szeretné a virtuális gép méretét, adja hozzá ezt a kódot a Main metódushoz:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális Géphez

Szeretne hozzáadni egy adatlemezt a virtuális gépet, adja hozzá ezt a kódot a Main metódushoz, amely 2 GB-os méretig, a LUN 0 és a egy olvasási és írási gyorsítótárazás típusú han adatlemez hozzáadása:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrások díjkötelesek, mindig érdemes mindig törölheti az erőforrást, amely már nincs rá szükség. Ha törölni szeretné a virtuális gépek és a támogató erőforrások, be kell csak törölje az erőforráscsoportot.

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Main metódushoz:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nagyjából öt perc alatt az a Konzolalkalmazás futtatása teljesen le kell vennie a befejezéshez. 

1. Futtassa a konzolalkalmazást, kattintson a **Start**.

2. Mielőtt lenyomja **Enter** indítása erőforrás törlése, eltarthat néhány percig, ellenőrizze az az Azure Portalon az erőforrások létrehozását. Kattintson a telepítés állapota, az üzembe helyezéssel kapcsolatos információk megtekintéséhez.

## <a name="next-steps"></a>További lépések
* Található információk segítségével egy virtuális gép létrehozása sablon használatával kihasználhatja [üzembe helyezése egy Azure virtuális gépen a C# és a egy Resource Manager-sablon használatával](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* További információ a [Azure-kódtárak a .NET-hez](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

