---
title: Virtuális gép és tárfiók létrehozása egy méretezhető alkalmazás számára az Azure-ban | Microsoft Docs
description: Ismerkedjen meg a skálázható alkalmazások futtatására szolgáló virtuális gépek üzembe helyezésével az Azure Blob Storage használatával
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.component: blobs
ms.openlocfilehash: 366c6043a851a88447116ca07bd90cc84d30cb7b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629460"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Virtuális gép és tárfiók létrehozása egy méretezhető alkalmazás számára

Ez az oktatóanyag egy sorozat első része. Ez az oktatóanyag egy olyan alkalmazás üzembe helyezését mutatja be, amely nagy mennyiségű véletlenszerű adat fel- és letöltését végzi egy Azure Storage-fiók használatával. Az oktatóanyagban egy virtuális gépen futó konzolalkalmazást hoz majd létre, amely nagy mennyiségű adatot tölt fel és le egy tárfiókba vagy tárfiókból.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Virtuális gép létrehozása
> * Egyéni szkriptbővítmény konfigurálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz az Azure PowerShell-modul Az 0,7 vagy újabb verziója. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
 
A minta 50 nagy méretű fájlt tölt fel egy Azure Storage-fiókban lévő blobtárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Hozzon létre egy tárfiókot az erőforráscsoportban hozta létre a [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) parancsot.

A következő parancsban a `<blob_storage_account>` helyőrző helyett írja be a saját globálisan egyedi Blob Storage-fióknevét.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

A virtuális gép létrehozása [New-azvm parancsmag](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Az üzembe helyezés konfigurálása

Ebben az oktatóanyagban bizonyos előfeltételeket kell telepíteni a virtuális gépen. Az egyéni szkriptbővítmény olyan PowerShell-szkriptek futtatására szolgál, amelyek a következő feladatokat hajtják végre:

> [!div class="checklist"]
> * A .NET Core 2.0 telepítése
> * A Chocolatey telepítése
> * A GIT telepítése
> * A mintatár klónozása
> * NuGet-csomagok visszaállítása
> * 50 1 GB-os véletlenszerű adatokkal feltöltött fájlt hoz létre

Futtassa az alábbi parancsmagot a virtuális gép konfigurációjának véglegesítéséhez. Ez a lépés 5–15 percet is igénybe vehet.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>További lépések

A sorozat első részében megismerkedett a tárfiókok létrehozásával, a virtuális gépek üzembe helyezésével, illetve a virtuális gépnek az előírt előfeltételek szerinti konfigurálásával, például a következőkkel:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Virtuális gép létrehozása
> * Egyéni szkriptbővítmény konfigurálása

A sorozat második részében nagy mennyiségű adatot fog feltölteni egy tárfiókba exponenciális újrapróbálkozás és párhuzamosság alkalmazásával.

> [!div class="nextstepaction"]
> [Nagy mennyiségű nagyméretű fájl párhuzamos feltöltése egy tárfiókba](storage-blob-scalable-app-upload-files.md)
