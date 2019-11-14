---
title: Az Azure dedikált gazdagépek üzembe helyezése a Azure PowerShell használatával
description: A virtuális gépeket a Azure PowerShell használatával dedikált gazdagépekre helyezheti üzembe.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 92dca6f4f41ff426aebcb8e580653afaa71afff8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033365"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Előzetes verzió: virtuális gépek üzembe helyezése dedikált gazdagépekre a Azure PowerShell használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy dedikált Azure- [gazdagépet](dedicated-hosts.md) a virtuális gépek (VM-EK) üzemeltetéséhez. 

Győződjön meg arról, hogy telepítette a Azure PowerShell 2.4.2-as vagy újabb verzióját, és bejelentkezett egy Azure-fiókba a (`Connect-AzAccount`) használatával. A 2.4.2-es verzió telepítéséhez nyisson meg egy PowerShell-parancssort, és írja be a következőt:

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

Az előnézeti modul funkcióinak a PowerShellben való engedélyezéséhez a PowerShellGet modul legalább 1.6.0 kell lennie. A PowerShell Core legújabb verziói automatikusan beépítettek, de a PowerShell régebbi verzióihoz a következő parancsot is futtathatja a legújabb verzióra való frissítéshez:

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Az Azure dedikált gazdagépek jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Ismert előzetes verzió korlátai**
> - A virtuális gépek méretezési csoportjai jelenleg nem támogatottak a dedikált gazdagépeken.
> - Az előzetes verzió kezdeti kiadása a következő virtuálisgép-sorozatot támogatja: DSv3 és ESv3. 



## <a name="create-a-host-group"></a>Gazda csoport létrehozása

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket. A magas rendelkezésre állás tervezése során további lehetőségek is rendelkezésre állnak. A dedikált gazdagépekhez a következő lehetőségek közül választhat: 
- Több rendelkezésre állási zónára kiterjedő span. Ebben az esetben minden használni kívánt zónában rendelkeznie kell egy gazdagép-csoporttal.
- Több tartalék tartomány között, amelyek fizikai állványokra vannak leképezve. 
 
Mindkét esetben meg kell adnia a gazdagép-csoport tartalék tartományának darabszámát. Ha nem szeretné a csoportban lévő tartalék tartományokat kivonni, használja az 1. számú tartalék tartományt. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használja. Ez a példa egy gazdagép csoportot hoz létre az 1. zónában, 2 tartalék tartománnyal. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Gazdagép létrehozása

Most hozzon létre egy dedikált gazdagépet a gazdagép csoportban. A gazdagép neve mellett meg kell adnia a gazdagéphez tartozó SKU-t is. A gazdagép SKU rögzíti a támogatott virtuálisgép-sorozatot, valamint a dedikált gazdagép hardveres generációját.  Az előzetes verzió ideje alatt a következő gazdagépi SKU-értékeket fogjuk támogatni: DSv3_Type1 és ESv3_Type1.


A gazdagép SKU-ról és a díjszabásról további információt az [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing)című témakörben talál.

Ha a gazdagéphez a tartalék tartományokat állítja be, a rendszer megkéri, hogy adja meg a gazdagép tartalék tartományát. Ebben a példában a gazdagép tartalék tartományát 1-re állítja.


```powershell
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

Ha a rendelkezésre állási zónát a gazda csoport létrehozásakor adta meg, akkor ugyanazt a zónát kell használnia a virtuális gép létrehozásakor. Ebben a példában, mert a gazda csoport az 1. zónában van, létre kell hoznia a virtuális gépet az 1. zónában.  


```powershell
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
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép hibás állapotban lesz létrehozva. 

## <a name="check-the-status-of-the-host"></a>A gazdagép állapotának keresése

Megtekintheti a gazdagép állapotát, és hány virtuális gépet telepíthet a gazdagépre a [GetAzHost](/powershell/module/az.compute/get-azhost) használatával a `-InstanceView` paraméterrel.

```
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A kimenet a következőhöz hasonlóan fog kinézni:

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

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A dedikált gazdagépekre akkor is díjat számítunk fel, ha nincsenek virtuális gépek üzembe helyezése. Minden olyan gazdagépet törölni kell, amelyet jelenleg nem használ a költségek megtakarítására.  

Csak akkor törölhet egy gazdagépet, ha már nem használja a virtuális gépeket. Törölje a virtuális gépeket a [Remove-AzVM](/powershell/module/az.compute/remove-azvm)használatával.

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

A virtuális gépek törlése után törölheti a gazdagépet a [Remove-AzHost](/powershell/module/az.compute/remove-azhost)használatával.

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Miután törölte az összes gazdagépet, törölheti a gazdagépet a [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)használatával. 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

A [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával egyetlen parancsban is törölheti a teljes erőforráscsoportot. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gépet, gazdagépet és gazdagépet is.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Következő lépések

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A [Azure Portal](dedicated-hosts-portal.md)használatával dedikált gazdagépeket is üzembe helyezhet.
