---
title: Azure-beli virtuális gépek létrehozása és kezelése C# a használatával | Microsoft Docs
description: A C# és a Azure Resource Manager használatával telepítheti a virtuális gépet és annak összes támogató erőforrását.
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
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b88bade886bf8cf22387e8733b8710414c944988
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361135"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Windows rendszerű virtuális gépek létrehozása és kezelése az Azure-ban a használatávalC# #

Egy [Azure-beli virtuális gépnek](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrásra van szüksége. Ez a cikk a VM-erőforrások létrehozását, kezelését és törlését ismerteti a használatával C#. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Visual Studio-projekt létrehozása
> * A csomag telepítése
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezek a lépések körülbelül 20 percet vesznek igénybe.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Visual studiót](https://docs.microsoft.com/visualstudio/install/install-visual-studio). A munkaterhelések lapon válassza ki a **.net Desktop Development** elemet, majd kattintson a **telepítés**gombra. Az összegzésben láthatja, hogy a **.NET-keretrendszer 4 – 4,6 fejlesztői eszközei** automatikusan ki vannak választva. Ha már telepítette a Visual studiót, a .NET-munkaterhelést a Visual Studio Launcher használatával adhatja hozzá.
2. A Visual Studióban kattintson a **File (Fájl)**  > **New (Új)**  > **Project (Projekt)** parancsra.
3. A **sablonok** > **vizualizáció C#** területén válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, írja be a *myDotnetProject* nevet a projekt neveként, válassza ki a projekt helyét, majd kattintson **az OK**gombra.

## <a name="install-the-package"></a>A csomag telepítése

A NuGet csomagok a legegyszerűbben a lépések végrehajtásához szükséges kódtárak telepítésére szolgálnak. A Visual Studióban szükséges kódtárak beszerzéséhez hajtsa végre a következő lépéseket:

1. Kattintson az **eszközök** > **Nuget csomagkezelő**elemre, majd a **Package Manager konzol**elemre.
2. Írja be ezt a parancsot a konzolba:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lépés elkezdése előtt győződjön meg arról, hogy van hozzáférése egy [Active Directory egyszerű szolgáltatáshoz](../../active-directory/develop/howto-create-service-principal-portal.md). Az alkalmazás AZONOSÍTÓját, a hitelesítési kulcsot és a bérlő AZONOSÍTÓját is rögzíteni kell egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. A Megoldáskezelőban kattintson a jobb gombbal a *myDotnetProject* > **új elem** **hozzáadása** > lehetőségre, majd válassza a **szövegfájl** *elemet a vizuális C# elemekben*. Nevezze el a *azureauth. properties*fájlt, majd kattintson a **Hozzáadás**gombra.
2. Adja hozzá az alábbi engedélyezési tulajdonságokat:

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

    Cserélje le  **&lt;az előfizetés&gt; -azonosítót** az előfizetési azonosítóra,  **&lt;az Application-ID&gt; -** t a Active Directory alkalmazás-azonosítóra, **&lt;a hitelesítési kulcsra az&gt;** alkalmazás kulcsa **és&lt;abérlő&gt;** azonosítója a bérlő azonosítójával.

3. Mentse a azureauth. properties fájlt. 
4. Állítson be egy környezeti változót a AZURE_AUTH_LOCATION nevű Windowsban a létrehozott hitelesítési fájl teljes elérési útjával. Például a következő PowerShell-parancs használható:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a létrehozott projekthez tartozó Program.cs-fájlt. Ezután adja hozzá a következő utasításokat a fájl elejéhez a meglévő utasításokhoz:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. A felügyeleti ügyfél létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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

Az összes erőforrást egy [erőforráscsoporthoz](../../azure-resource-manager/resource-group-overview.md)kell foglalni.

Az alkalmazás értékének megadásához és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>A rendelkezésre állási csoport létrehozása

A [rendelkezésre állási](tutorial-availability-sets.md) csoportok megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

A rendelkezésre állási csoport létrehozásához adja hozzá ezt a kódot a Main metódushoz:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) szükséges.

A virtuális gép nyilvános IP-címének létrehozásához adja hozzá ezt a kódot a Main metódushoz:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A virtuális gépnek egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatán kell lennie.

Alhálózat és virtuális hálózat létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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

Hálózati adapter létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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

Most, hogy létrehozta az összes támogatási erőforrást, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot a Main metódushoz:

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
> Ez az oktatóanyag egy olyan virtuális gépet hoz létre, amely a Windows Server operációs rendszer egy verzióját futtatja. További információ a többi rendszerkép kiválasztásáról: [Azure-beli virtuálisgép-rendszerképek navigálása és kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával.
> 
>

Ha a Piactéri rendszerkép helyett meglévő lemezt szeretne használni, használja a következő kódot:

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

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett érdemes lehet kódot létrehozni az ismétlődő vagy összetett feladatok automatizálásához.

Ha bármit kell tennie a virtuális géppel, le kell kérnie a példányát:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>A virtuális géppel kapcsolatos információk lekérése

A virtuális géppel kapcsolatos információk beszerzéséhez adja hozzá ezt a kódot a Main metódushoz:

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

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fizetnie kell, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Ha a virtuális gép fel van foglalva, a hozzá társított összes erőforrás fel van foglalva, és a számlázás véget ér.

Ha le szeretné állítani a virtuális gépet a felszabadítása nélkül, adja hozzá ezt a kódot a Main metódushoz:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Ha felszabadítani szeretné a virtuális gépet, módosítsa a kikapcsoló hívást a következő kódra:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

A virtuális gép elindításához adja hozzá ezt a kódot a Main metódushoz:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A központi telepítés számos aspektusát figyelembe kell venni a virtuális gép méretének meghatározásakor. További információ: virtuális gépek [méretei](sizes.md).  

A virtuális gép méretének módosításához adja hozzá ezt a kódot a Main metódushoz:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

Ha adatlemezt szeretne hozzáadni a virtuális géphez, adja hozzá ezt a kódot a Main metódushoz. Ez a példa egy 2 GB méretű adatlemezt hoz létre, amely a 0 LUN-t és a ReadWrite gyorsítótárazási típusát adja meg:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért kell fizetnie, mindig érdemes törölni a már nem szükséges erőforrásokat. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Main metódushoz:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe ahhoz, hogy a konzol alkalmazás teljes körűen fusson az elejétől a végéig. 

1. A konzol alkalmazás futtatásához kattintson a **Start**gombra.

2. Mielőtt megnyomja az **ENTER** billentyűt az erőforrások törlésének megkezdéséhez, eltarthat néhány percig, hogy ellenőrizze az erőforrások létrehozását a Azure Portalban. A központi telepítésre vonatkozó információk megtekintéséhez kattintson a központi telepítés állapotára.

## <a name="next-steps"></a>További lépések
* Használja ki az [Azure-beli virtuális gépek üzembe helyezése és a Resource Manager-sablonok használatával C# ](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)létrehozott virtuális gépeket használó sablonok használatát.
* További információ a .NET- [hez készült Azure](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet)-kódtárak használatáról.
