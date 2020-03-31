---
title: 'Azure-beli virtuális gép létrehozása és kezelése C használatával #'
description: A C# és az Azure Resource Manager használatával üzembe helyezhet egy virtuális gépet és annak összes támogató erőforrását.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 3930e51f63615abd21a7b04199a0f4767925792a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944517"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Windows-virtuális gépek létrehozása és kezelése az Azure-ban a C használatával # #

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) szüksége van több támogató Azure-erőforrások. Ez a cikk a virtuális gép erőforrások c#használatával történő létrehozását, kezelését és törlését ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * A csomag telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezeket a lépéseket körülbelül 20 perc alatt kell megtennie.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)alkalmazást. A Számítási feladatok lapon válassza a **.NET asztal fejlesztését,** majd kattintson a **Telepítés gombra.** Az összegzésben láthatja, hogy a **.NET Framework 4 - 4.6 fejlesztői eszközök** automatikusan kiválasztásra kerülnek. Ha már telepítette a Visual Studio alkalmazást, a .NET-számítási feladatot hozzáadhatja a Visual Studio launcher használatával.
2. A Visual Studio programban kattintson az**Új** > **projekt** **fájlja** > gombra.
3. A Templates Visual C# **(Sablonok vizuális** >  **Console App (.NET Framework)****csz.** *myDotnetProject* **OK**

## <a name="install-the-package"></a>A csomag telepítése

A NuGet csomagok a legegyszerűbb módja a lépések végrehajtásához szükséges könyvtárak telepítésének. A Visual Studio alkalmazásban szükséges könyvtárak leeléréséhez tegye a következő lépéseket:

1. Kattintson **az Eszközök** > **Nuget csomagkezelő ikonra,** majd a **Csomagkezelő konzol parancsra.**
2. Írja be ezt a parancsot a konzolba:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lépés megkezdése előtt győződjön meg arról, hogy rendelkezik-e hozzáféréssel egy [Active Directory egyszerű szolgáltatáshoz.](../../active-directory/develop/howto-create-service-principal-portal.md) Az alkalmazásazonosítót, a hitelesítési kulcsot és a bérlői azonosítót is rögzítenie kell egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. A Megoldáskezelőben kattintson a jobb gombbal *a myDotnetProject* > **Új elem****hozzáadása** > elemre, majd válassza a **Szövegfájl parancsot** a *Visual C# elemekben.* Nevezze el a fájlt *azureauth.properties*, majd kattintson **a Hozzáadás gombra.**
2. Adja hozzá ezeket az engedélyezési tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Cserélje le ** &lt;az&gt; előfizetés-id-t** az előfizetés-azonosítóra, ** &lt;az alkalmazásazonosítót&gt; ** az Active Directory alkalmazásazonosítóra, ** &lt;&gt; ** a hitelesítési kulcsot az alkalmazáskulcsra és ** &lt;a bérlőazonosítót&gt; ** a bérlői azonosítóra.

3. Mentse az azureauth.properties fájlt. 
4. Állítson be egy AZURE_AUTH_LOCATION nevű környezeti változót a létrehozott engedélyezési fájl teljes elérési útjával. Például a következő PowerShell-parancs használható:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekt Program.cs fájlját. Ezután adja hozzá ezeket a fájl tetején lévő utasításokhoz a következő utasítások használatával:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a fő metódushoz:

    ```csharp
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

Minden erőforrásnak [erőforráscsoportban](../../azure-resource-manager/management/overview.md)kell lennie.

Az alkalmazás értékeinek megadásához és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a Fő metódushoz:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Az elérhetőségi készlet létrehozása

[A rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

Az elérhetőségi készlet létrehozásához adja hozzá ezt a kódot a fő metódushoz:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Nyilvános IP-cím létrehozása

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) szükséges.

A virtuális gép nyilvános IP-címének létrehozásához adja hozzá ezt a kódot a fő metódushoz:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A virtuális gépnek a virtuális [hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatában kell lennie.

Alhálózat és virtuális hálózat létrehozásához adja hozzá ezt a kódot a fő metódushoz:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása

A virtuális gépnek hálózati adapterre van szüksége a virtuális hálózaton való kommunikációhoz.

Hálózati adapter létrehozásához adja hozzá ezt a kódot a Fő metódushoz:

```csharp
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

Most, hogy létrehozta az összes támogató erőforrást, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot a fő metódushoz:

```csharp
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
> Ez az oktatóanyag a Windows Server operációs rendszer egy verzióját futtató virtuális gépet hoz létre. Ha többet szeretne tudni a többi lemezkép kiválasztásáról, olvassa el a [Navigálás és az Azure virtuálisgép-lemezképek kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával című témakört.
> 
>

Ha piactéri lemez lemezt szeretne használni piactéri lemezkép helyett, használja ezt a kódot:

```csharp
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

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül az ismétlődő vagy összetett feladatok automatizálására kód is létrehozható.

Ha valamit tennie kell a virtuális gép, meg kell, hogy egy példányt is:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Információ a virtuális gépről

A virtuális géppel kapcsolatos információk lefűzéséhez adja hozzá ezt a kódot a fő metódushoz:

```csharp
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

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fel kell számítania, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Amikor egy virtuális gép felvan szabadítva, a hozzá társított összes erőforrás is felszabadítva lesz, és a számlázás véget ér.

Ha meg szeretné állítani a virtuális gépet anélkül, hogy osztanunk szeretné, adja hozzá ezt a kódot a fő metódushoz:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Ha fel szeretné osztani a virtuális gépet, módosítsa a PowerOff-hívást erre a kódra:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

A virtuális gép elindításához adja hozzá ezt a kódot a fő metódushoz:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A virtuális gép méretének meghatározásakor a központi telepítés számos aspektusát figyelembe kell venni. További információ: [VM méretek](sizes.md).  

A virtuális gép méretének módosításához adja hozzá ezt a kódot a Fő metódushoz:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

Ha adatlemezt szeretne hozzáadni a virtuális géphez, adja hozzá ezt a kódot a fő metódushoz. Ez a példa 2 GB méretű adatlemezt, 0-s logikai és gyorsítótárazási típusú ReadWrite-et ad hozzá:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért díjat számítunk fel, mindig ajánlott törölni azokat az erőforrásokat, amelyekre már nincs szükség. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Fő metódushoz:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe, amíg ez a konzolalkalmazás teljesen elindul az elejétől a végéig. 

1. A konzolalkalmazás futtatásához kattintson a **Start**gombra.

2. Mielőtt az **Enter** billentyű lenyomásával megszeretné kezdeni az erőforrások törlését, eltarthat néhány percet az erőforrások létrehozásának ellenőrzése az Azure Portalon. Kattintson a központi telepítés állapotára a központi telepítéssel kapcsolatos információk megtekintéséhez.

## <a name="next-steps"></a>További lépések
* Használja ki a sablon használatával hozzon létre egy virtuális gépet az [Azure virtuális gép központi telepítése c# és egy Erőforrás-kezelő sablon használatával](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című elem információinak használatával.
* További információ a [.NET Azure-kódtárak](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet)használatáról.
