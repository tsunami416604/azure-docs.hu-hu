---
title: Az Azure dedikált állomásainak üzembe helyezése az Azure PowerShell használatával
description: Telepítse a virtuális gépeket dedikált állomásokra az Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082849"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Virtuális gépek telepítése dedikált állomásokra az Azure PowerShell használatával

Ez a cikk végigvezeti, hogyan hozhat létre egy Azure [dedikált gazdagépet](dedicated-hosts.md) a virtuális gépek (Virtuális gépek) üzemeltetéséhez. 

Győződjön meg arról, hogy telepítette az Azure PowerShell 2.8.0-s `Connect-AzAccount`vagy újabb verzióját, és be van jelentkezve egy Azure-fiókba a alkalmazásban. 

## <a name="limitations"></a>Korlátozások

- A virtuálisgép-méretezési csoportok jelenleg nem támogatottak dedikált állomásokon.
- A dedikált gazdagépek számára elérhető méretek és hardvertípusok régiónként eltérőek lehetnek. További információ a gazdagép [díjszabási oldalán.](https://aka.ms/ADHPricing)

## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása

A **gazdagépcsoport** olyan erőforrás, amely dedikált gazdagépek gyűjteményét képviseli. Hozzon létre egy gazdagépcsoportot egy régióban és egy rendelkezésre állási zónát, és adjon hozzá állomásokat. A magas rendelkezésre állás tervezésekor további lehetőségek is rendelkezésre állnak. Az alábbi lehetőségek egyikét vagy mindkettőt használhatja a dedikált gazdagépekkel: 
- Több rendelkezésre állási zónára is kiterjedjen. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagépcsoporttal.
- Több tartalék tartományban, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagépcsoport tartaléktartomány-számát. Ha nem szeretné span tartalék tartományok a csoportban, használja a tartalék tartomány száma 1. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. Ez a példa létrehoz egy állomáscsoportot az 1. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Állomás létrehozása

Most hozzunk létre egy dedikált gazdagép a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a termékváltozatot is az állomásszámára. A gazdatermékváltozat rögzíti a támogatott virtuális gép sorozat, valamint a hardver-generálás a dedikált gazdagép.

A gazdagép számos szolgáltatásáról és díjszabásáról az [Azure dedikált gazdagépdíj-szabása](https://aka.ms/ADHPricing)című témakörben talál további információt.

Ha tartalék tartományszámot állít be a gazdagépcsoporthoz, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát. Ebben a példában az állomás tartalék tartományát 1-re állítjuk.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a dedikált gazdagépen. 

Ha a gazdagépcsoport létrehozásakor megadott egy rendelkezésre állási zónát, akkor ugyanazt a zónát kell használnia a virtuális gép létrehozásakor. Ebben a példában, mivel a gazdagépcsoport az 1 zónában van, létre kell hoznunk a virtuális gép 1 zónában.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép sikertelen állapotban jön létre. 

## <a name="check-the-status-of-the-host"></a>Az állomás állapotának ellenőrzése

Ellenőrizheti a gazdagép állapotát, és hány virtuális gépet továbbra is telepíthet a [getazhost](/powershell/module/az.compute/get-azhost) használatával a `-InstanceView` paraméterrel.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A kimenet így fog kinézni:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Meglévő virtuális gép hozzáadása 

Hozzáadhat egy meglévő virtuális gép egy dedikált gazdagép, de a virtuális gép először le kell állítani\Felszabadított. Mielőtt áthelyezne egy virtuális gép egy dedikált gazdagép, győződjön meg arról, hogy a virtuális gép konfigurációja támogatott:

- A virtuális gép méretének ugyanolyan méretűnek kell lennie, mint a dedikált gazdagépnek. Például ha a dedikált gazdagép DSv3, majd a virtuális gép mérete lehet Standard_D4s_v3, de nem lehet Standard_A4_v2. 
- A virtuális gép nek ugyanabban a régióban kell lennie, mint a dedikált gazdagép.
- A virtuális gép nem lehet része a közelség elhelyezési csoport. Távolítsa el a virtuális gép a közelség elhelyezési csoport, mielőtt áthelyezi egy dedikált gazdagép. További információ: [Virtuális gép áthelyezése a közelségelhelyezési csoportból](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A virtuális gép nem lehet egy rendelkezésre állási csoportban.
- Ha a virtuális gép egy rendelkezésre állási zónában van, akkor a gazdagépcsoportokkal azonos rendelkezésre állási zónának kell lennie. A virtuális gép és a gazdagépcsoport rendelkezésre állási zóna beállításainak egyezniük kell.

Cserélje le a változók értékeit a saját adataira.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Akkor is díjat számítunk fel a dedikált gazdagépekért, ha nincsenek üzembe helyezve virtuális gépek. Törölje azokat a gazdagépeket, amelyeket jelenleg nem használ a költségek megtakarítására.  

Csak akkor törölheti az állomást, ha már nincsenek olyan virtuális gépek, amelyek azt használják. Törölje a virtuális gépeket az [Remove-AzVM](/powershell/module/az.compute/remove-azvm)használatával.

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

A virtuális gépek törlése után törölheti az állomást az [Remove-AzHost](/powershell/module/az.compute/remove-azhost)használatával.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Miután törölte az összes állomást, törölheti a gazdagépcsoportot az [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)paranccsal a teljes erőforráscsoportot törölheti is egyetlen parancsban. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gépet, állomást és gazdagépcsoportot.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>További lépések

- Van egy mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy régióban. [here](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- Dedikált állomásokat is üzembe helyezhet az [Azure Portal használatával.](dedicated-hosts-portal.md)
