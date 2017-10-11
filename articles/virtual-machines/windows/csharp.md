---
title: "Létrehozását és kezelését egy Azure virtuális gépen a C# használatával |} Microsoft Docs"
description: "C# és az Azure Resource Manager segítségével telepítheti a virtuális gép és annak támogató erőforrásokat."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 5d9021c2f65b70e36d5ea82992c9fb9d2d6d394a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Létrehozása és kezelése Windows-alapú virtuális gépek az Azure-ban C# #

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrások kell. Ez a cikk ismerteti a létrehozása, kezelése és C# használatával Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * A csomag telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

A lépések elvégzéséhez körülbelül 20 percet vesz igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Válassza ki **.NET asztali fejlesztési** a munkaterhelések lapot, és kattintson a **telepítése**. Az összegzés láthatja, hogy **.NET-keretrendszer 4-4.6 Fejlesztőeszközök** automatikusan ki van jelölve meg. Ha már telepítette a Visual Studio, a .NET alkalmazások és szolgáltatások segítségével a Visual Studio indítója adhat meg.
2. A Visual Studióban kattintson **fájl** > **új** > **projekt**.
3. A **sablonok** > **Visual C#**, jelölje be **Konzolalkalmazás (.NET-keretrendszer)**, adja meg *myDotnetProject* a projekt nevét, válassza ki a projekt helyét, és kattintson **OK**.

## <a name="install-the-package"></a>A csomag telepítése

NuGet-csomagok használata a lépések végrehajtása szükséges kódtárak telepítése a legegyszerűbb. Ahhoz, hogy a Visual Studio szükséges könyvtárak, hajtsa végre ezeket a lépéseket:

1. Kattintson a **eszközök** > **Nuget-Csomagkezelő**, és kattintson a **Csomagkezelő konzol**.
2. Ezt a parancsot a konzolon:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Ez a lépés megkezdése előtt győződjön meg arról, hogy rendelkezik-e a hozzáférést egy [Active Directory szolgáltatás egyszerű](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Is rögzíteni kell az alkalmazás Azonosítóját, a hitelesítési kulcs és a bérlő azonosítója, amelyekre szüksége van egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *myDotnetProject* > **Hozzáadás** > **új elem**, majd válassza ki **szövegfájl** a *Visual C# elemek*. A fájl neve *azureauth.properties*, és kattintson a **Hozzáadás**.
2. Adja hozzá a engedélyezési tulajdonságok:

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

    Cserélje le  **&lt;előfizetés-azonosító&gt;**  az előfizetés-azonosítóval rendelkező  **&lt;alkalmazásazonosító&gt;**  való a Active Directory-azonosítót,  **&lt;hitelesítési kulcs&gt;**  az alkalmazás kulccsal és  **&lt;bérlőazonosító&gt;**  a bérlő azonosítója.

3. Mentse a azureauth.properties fájlt. 
4. A létrehozott engedélyezési fájl teljes elérési útja AZURE_AUTH_LOCATION nevű Windows környezeti változó értéke. Például a következő PowerShell-parancs használható:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a Program.cs fájlra a projekthez létrehozott, és adja hozzá ezeket a fájl felső utasítást, hogy a meglévő utasítás használatával:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Hozzon létre a felügyeleti ügyfél, vegye fel ezt a kódot a fő metódus:

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

Adja meg az alkalmazás az értékét, és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a fő metódus:

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

[Rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépeket, amelyet az alkalmazás karbantartása.

A rendelkezésre állási csoport létrehozása, vegye fel ezt a kódot a fő metódus:

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

A nyilvános IP-cím a virtuális gép létrehozása, vegye fel ezt a kódot a fő metódus:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Az alhálózat szerepelnie kell egy virtuális gépet egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozzon létre egy alhálózatot és egy virtuális hálózatot, vegye fel ezt a kódot a fő metódus:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>A hálózati illesztő létrehozása

A virtuális gépek kell a hálózati adaptert a virtuális hálózaton való kommunikációhoz.

Hozzon létre egy hálózati adapter, vegye fel ezt a kódot a fő metódus:

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

Most, hogy létrehozta a támogató erőforrásokat, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához, vegye fel ezt a kódot a fő metódus:

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
> Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verziója. Más rendszerképek kiválasztásáról kapcsolatos további információkért lásd: [keresse meg és válassza ki azokat a Windows PowerShell és az Azure CLI Azure virtuális gép lemezképeket](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Ha szeretné használni a meglévő lemez Piactéri rendszerkép helyett, használja ezt a kódot:

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

A virtuális gépek életciklusa folyamán érdemes lehet indítása, leállítása vagy törlése a virtuális gépek például a felügyeleti feladatok futtatásához. Emellett érdemes bonyolult vagy ismétlődő feladatok automatizálásához kódot létrehozni.

Amikor kell tennie semmit a virtuális gép, egy példányát kell:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak beolvasása

Ahhoz, hogy a virtuális gép adatait, a fő metódus adja hozzá ezt a kódot:

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

Állítsa le a virtuális gépet és a beállítások megőrzése, de továbbra is azt számlázni, vagy állítsa le a virtuális gépet, és azt felszabadítani. Ha egy virtuális gép fel van szabadítva, vele társított összes erőforrás is felszabadított és számlázási végpontjainak.

A virtuális gép leállítása nélkül felszabadítása azt, vegye fel ezt a kódot a fő metódus:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Ha a virtuális gép felszabadítása, módosítsa ezt a kódot kikapcsolt hívása:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Indítsa el a virtuális Gépet

Indítsa el a virtuális gépet, vegye fel ezt a kódot a fő metódus:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezésével

Telepítési sok szempontját figyelembe kell venni, amikor eldönti, a virtuális gép méretét. További információkért lásd: [Virtuálisgép-méretek](sizes.md).  

Ha módosítani szeretné a virtuális gép méretét, adja hozzá ezt a kódot a fő metódus:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális gép

Adatlemez hozzáadása a virtuális gépet, adja hozzá ezt a kódot a fő metódus, amely 2 GB-nál, 0 és a gyorsítótárazási ReadWrite a logikai egység han adatlemez hozzáadása:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokhoz van szó, ajánlott mindig törli az erőforrást, amely már nem szükséges. Ha törölni szeretné a virtuális gépek és a támogató erőforrásokat, meg kell nyitnia csak törölje a csoportot.

Törölje a csoportot, vegye fel ezt a kódot a fő metódus:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Öt perc a konzol alkalmazás teljesen futtatásához indítás kell vennie a befejezéshez. 

1. Futtassa a konzolalkalmazást, kattintson a **Start**.

2. Ahhoz, hogy nyomja le az ENTER **Enter** erőforrások törlése elindításához eltarthat néhány percig az Azure-portálon az erőforrások létrehozásának ellenőrzése. Kattintson a telepítés állapota a telepítéssel kapcsolatos információk megjelenítéséhez.

## <a name="next-steps"></a>Következő lépések
* Sablon használatával hozhat létre egy virtuális gép található információk segítségével előnyeit [központi telepítése egy Azure virtuális gépen a C# és a Resource Manager-sablon használatával](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* További információ a [Azure-könyvtárakban .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

