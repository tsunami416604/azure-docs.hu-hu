---
title: "Egyéni Virtuálisgép-lemezképek létrehozása az Azure PowerShell használatával |} Microsoft Docs"
description: "Útmutató – hozzon létre egy egyéni Virtuálisgép-lemezkép az Azure PowerShell használatával."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/07/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7001e5df235d65c531b9102f879bde9693c4f853
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Egy Azure-VM PowerShell-lel egyéni lemezkép létrehozása

Egyéni lemezképek piactéren elérhető rendszerkép hasonló, de Ön hozza létre őket. Egyéni lemezképek a rendszerindítási beállításokat, például alkalmazások, alkalmazás, és más operációs rendszer konfigurációjában kerüli használható. Ebben az oktatóanyagban létrehoz egy Azure virtuális gép saját egyéni rendszerképét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Sysprep és a virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egy egyéni lemezképből
> * Az előfizetésben a képek felsorolása
> * Lemezkép törlése

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépéseket a meglévő virtuális igénybe vehet, és kapcsolja be, amelyek segítségével hozzon létre új Virtuálisgép-példányok újra felhasználható egyéni lemezképként adatok találhatók.

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gépet. Ha szükséges, ez [parancsfájl minta](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) hozhat létre egyet. Az oktatóanyag lépéseinek működő cseréje esetén a az erőforráscsoportot és a virtuális gép nevét, amennyiben szükséges.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Hozzon létre egy virtuális gép lemezképét, meg kell készíteni a virtuális gép a virtuális gép normalizálása, felszabadítása, és majd a forrás virtuális gép általánosítva van az Azure-ban.

### <a name="generalize-the-windows-vm-using-sysprep"></a>A Windows virtuális gép Sysprep generalize

A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancssort rendszergazdaként. Lépjen be *%windir%\system32\sysprep*, majd futtassa a *sysprep.exe*.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki *adja meg a rendszer Out-of-Box élmény (OOBE)*, és győződjön meg arról, hogy a *Generalize* jelölőnégyzet be van jelölve.
4. A **leállítási beállítások**, jelölje be *leállítási* majd **OK**.
5. A Sysprep befejezését követően a virtuális gép leáll. **Ne indítsa újra a virtuális gép**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Felszabadítani, és a virtuális gép megjelölése általánosítva

A képfájl létrehozásához a virtuális gép kell felszabadítása. lehetséges, és az Azure-ban általánosítva van megjelölve.

A virtuális gép használatával felszabadítása [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

A virtuális gép állapotának beállítása `-Generalized` használatával [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>A lemezkép létrehozása

Most a virtuális gép lemezképét segítségével létrehozható [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) és [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Az alábbi példakód létrehozza nevű kép *myImage* nevű VM *myVM*.

Helyezze a virtuális gépet. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

A lemezkép-konfiguráció létrehozása.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

A lemezkép létrehozása.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása lemezkép alapján

Most, hogy egy lemezképet, létrehozhat egy vagy több új virtuális gépek a lemezképből. Virtuális gép létrehozása egy egyéni lemezképből nagyon hasonlít a Piactéri lemezképről virtuális gép létrehozása. A Piactéri lemezkép használata esetén meg kell adnia az információkat a kép, kép szolgáltató, ajánlat, SKU és verziót. Az egyéni lemezképet ugyanúgy kell adja meg az egyéni lemezképet erőforrás Azonosítóját. 

A következő parancsfájl egy változó létrehozhatunk *$image* az egyéni lemezkép használatával kapcsolatos információk tárolására [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) és használjuk majd [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) , és adja meg a azonosító használatával a *$image* változó imént létrehozott. 

A parancsfájl létrehoz egy nevű virtuális gép *myVMfromImage* egy új erőforráscsoportot az egyéni lemezkép alapján nevű *myResourceGroupFromImage* a a *USA nyugati régiója* helyét.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Lemezkép-kezelési 

Néhány példa általános kezelési kép feladatok és hogyan hajthatja végre ezeket a PowerShell használatával.

Listázza az összes lemezkép neve.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Lemezkép törlése. Ebben a példában a nevű rendszerkép törlése *myOldImage* a a *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egyéni Virtuálisgép-lemezképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Sysprep és a virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egy egyéni lemezképből
> * Az előfizetésben a képek felsorolása
> * Lemezkép törlése

Előzetes megtudhatja, hogyan magas rendelkezésre állású virtuális gépek kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



