---
title: "Méretezhető alkalmazások virtuális gép és a storage-fiók létrehozása az Azure-ban |} Microsoft Docs"
description: "Egy Azure blob storage használatával méretezhető alkalmazás futtatásához használt virtuális gép telepítése"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 011812f5e32537321301dad0c654bca341b3606d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Virtuális gép és a méretezhető alkalmazások storage-fiók létrehozása

Ez az oktatóanyag egy sorozat része. Ez az oktatóanyag bemutatja, hogy az alkalmazás, amely feltölti és töltse le a nagy adatmennyiségek véletlenszerű egy Azure storage-fiók. Amikor végzett, van egy feltöltés, és töltse le a nagy mennyiségű adatok tárfiókba egy virtuális gépen futó konzolalkalmazással.

A rész az adatsorozatok megismerheti, hogyan:

> [!div class="checklist"]
> * Create a storage account
> * Virtuális gép létrehozása
> * Egyéni parancsfájl-kiterjesztés konfigurálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Create a storage account
 
A minta 50 nagy fájlok feltöltése az Azure Storage-fiók egy blob-tárolóba. A storage-fiók egy egyedi névteret tárolhatja és érheti el az Azure storage-adatobjektumok biztosít. Hozzon létre egy tárfiókot az Ön által létrehozott erőforráscsoport a [New-AzureRmStorageAccount](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccount) parancsot.

Az alábbi parancs helyettesítse a saját globálisan egyedi nevet a Blob storage-fiók, ahol megjelenik a `<blob_storage_account>` helyőrző.

```powershell-interactive
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Deploy konfigurációja

Ebben az oktatóanyagban nincsenek előfeltételek, amelyet a virtuális gépen kell telepíteni. Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával futtatni egy PowerShell-parancsfájlt, amely a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Telepítse a .NET 2.0
> * A telepítés chocolatey
> * Telepítse a GIT
> * A minta-tárház klónozása
> * Állítsa vissza a NuGet-csomagok
> * 1 GB-os 50 fájl véletlenszerű adatokat hoz létre

Futtassa a következő parancsmagot a virtuális gép konfigurációjának véglegesítéséhez. Ez a lépés 5-15 percet is igénybe vehet.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to instal .NET core, dependancies, and pre-create the files to upload.
Set-AzureRMVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Következő lépések

Az adatsorozat egyik részében megismerte tárfiók létrehozásához, egy virtuális gép telepítése és konfigurálása a virtuális gép például hogyan szükséges előfeltételek:

> [!div class="checklist"]
> * Create a storage account
> * Virtuális gép létrehozása
> * Egyéni parancsfájl-kiterjesztés konfigurálása

Előzetes nagy mennyiségű adatot feltölteni a exponenciális újrapróbálkozási és a párhuzamosság használó tárfiókot az adatsorozat két részét.

> [!div class="nextstepaction"]
> [Nagy mennyiségű tárfiókba párhuzamosan nagy fájlok feltöltése](storage-blob-scalable-app-upload-files.md)
