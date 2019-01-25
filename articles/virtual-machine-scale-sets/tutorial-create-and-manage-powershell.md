---
title: Oktatóanyag – Azure virtuálisgép-méretezési csoport létrehozása és kezelése | Microsoft Docs
description: Arra vonatkozó tudnivalók, hogyan használhatja a PowerShellt virtuálisgép-méretezési csoportok létrehozásához, valamint néhány olyan általános kezelési feladat elvégzéséhez, amilyen a példányok elindítása és leállítása, vagy a méretezési csoport kapacitásának módosítása.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0aa4b8fd606c45f2dea702140c34fc93bcd4c5a4
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885371"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Oktatóanyag: Létrehozása és kezelése az Azure PowerShell használatával virtuálisgép-méretezési csoportot
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Virtuálisgép-példányok adott méreteinek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 6.0.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuálisgép-méretezési csoport létrejötte előtt létre kell hozni. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *EastUS* régióban. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Az erőforráscsoport nevének meghatározására a méretezési csoport létrehozásakor vagy módosításakor kerül sor a jelen oktatóanyag keretein belül.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési csoportot a [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) paranccsal. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

A méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="view-the-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok megtekintése
A méretezési csoportban futó virtuálisgép-példányok listájának megjelenítéséhez használja a [Get-AzureRmVssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancsot az alábbi módon:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében két virtuálisgép-példány látható a méretezési csoportban:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Az egy adott virtuálisgép-példánnyal kapcsolatos további információk megtekintéséhez adja hozzá az `-InstanceId` paramétert a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancshoz. A következő példa információkat tekint meg az *1*-es számú virtuálisgép-példányról:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Kapcsolatadatok listázása
A rendszer egy nyilvános IP-címet rendel hozzá a terheléselosztóhoz, amely a forgalmat az egyéni virtuálisgép-példányokhoz irányítja. Alapértelmezés szerint a rendszer a hálózati címfordítási (NAT) szabályokat adja hozzá az Azure-terheléselosztóhoz, amely továbbítja a távoli kapcsolati forgalmat az egyes virtuális gépekre egy adott porton keresztül. A méretezési csoportban lévő virtuálisgép-példányok csatlakoztatásához létesítsen távoli kapcsolatot a hozzárendelt nyilvános IP-címhez és portszámhoz.

A méretezési csoportokban lévő virtuálisgép-példányokhoz való kapcsolódáshoz használható NAT-portok listázásához először kérje le a terheléselosztói objektumot a [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer) paranccsal. Ezután tekintse meg a bejövő NAT-szabályokat a [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) paranccsal:

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Az alábbi példakimeneten a példány neve, a terheléselosztó nyilvános IP-címe és a portszám látható, amelyeket a NAT-szabályok a forgalom továbbításához használnak:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

A szabály *neve* illeszkedik a VM-példány nevéhez, amelyet egy előző [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) parancs adott vissza. Ha például a *0-s* virtuálisgép-példányhoz szeretne csatlakozni, használja a *myScaleSet3389.0* elemet, és kapcsolódjon az *50001*-es porthoz. Az *1-es* virtuálisgép-példányhoz való csatlakozáshoz használja a *myScaleSet3389.1* elemben lévő értéket, és kapcsolódjon az *50002*-es porthoz. A PowerShell távoli eljáráshívás használatához csatlakozzon az *5985*-ös *TCP*-port megfelelő virtuálisgép-példány szabályához.

Tekintse meg a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) paranccsal.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Példa a kimenetre:

```powershell
IpAddress
---------
52.168.121.216
```

Távoli kapcsolaton keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg saját nyilvános IP-címét, valamint a kívánt VM-példány portszámát, amint az az előző parancsokban látható. Ha a rendszer erre kéri, adja meg a méretezési csoport létrehozása során használt hitelesítő adatokat (a mintaparancsokban ezek alapértelmezés szerint az *azureuser* és a *P@ssw0rd!*). Az Azure Cloud Shell használata esetén ezt a lépést egy helyi PowerShell-parancssorból vagy egy távoli asztali ügyfélről hajtsa végre. A következő példa az *1* nevű virtuálisgép-példányhoz csatlakozik:

```powershell
mstsc /v 52.168.121.216:50001
```

A virtuálisgép-példányba történő bejelentkezést követően szükség szerint manuális konfigurációs módosításokat hajthat végre. Egyelőre zárja be a távoli kapcsolatot.


## <a name="understand-vm-instance-images"></a>A virtuálisgép-példányok rendszerképeinek ismertetése
Az Azure Marketplace-en számos rendszerkép található, amelyekkel új virtuálisgép-példányokat lehet létrehozni. Az elérhető közzétevők listájának megtekintéséhez használja a [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) parancsot.

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Egy adott közzétevő rendszerképeinek listájának megtekintéséhez használja a [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancsot. A rendszerképek listája a `-PublisherName` (közzétevő) vagy az `–Offer` (ajánlat) alapján is szűrhető. Az alábbi példában a listát az összes olyan rendszerképre szűrjük, amelyek közzétevője a *MicrosoftWindowsServer*, és amelyek ajánlata megfelel a *WindowsServer*-nek:

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

A például szolgáló következő kimenet az összes elérhető Windows Server-rendszerképet megjeleníti:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Az oktatóanyag elején a méretezési csoport létrehozásakor a *Windows Server 2016 DataCenter* alapértelmezett virtuálisgép-rendszerkép lett megadva a virtuálisgép-példányokhoz. Másik virtuálisgép-rendszerképet is megadhat a [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancs kimenete alapján. Az alábbi példa egy `-ImageName` paraméterrel rendelkező méretezési csoportot hoz létre a *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest* virtuálisgép-rendszerképének megadásához. Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>A virtuálisgép-példányok méreteinek ismertetése
A virtuálisgép-példány mérete, más néven *SKU*, a virtuálisgép-példány által elérhető számítási erőforrások, azaz a processzor, a grafikus processzor és a memória mennyiségét határozza meg. A méretezési csoportban lévő virtuálisgép-példányokat a várható számítási igényeknek megfelelően kell méretezni.

### <a name="vm-instance-sizes"></a>A virtuálisgép-példányok mérete
Az alábbi táblázat a virtuális gépek gyakori méreteit használati esetek alapján kategorizálja.

| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításra optimalizált](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tárolásra optimalizált](../virtual-machines/windows/sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Elérhető virtuálisgép-példányméretek keresése
Egy adott régióban elérhető virtuálisgép-példányméretek megtekintéséhez használja a [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancsot. 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

A kimenet a következő sűrített példához hasonló, amelyben az egyes virtuálisgép-méretekhez hozzárendelt erőforrások láthatók:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Az oktatóanyag elején a méretezési csoport létrehozásakor a *Standard_DS1_v2* alapértelmezett virtuálisgép-termékváltozat (SKU) lett megadva a virtuálisgép-példányokhoz. Megadhat egy másik virtuálisgép-példányméretet a [Get-AzureRm-VMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancs kimenete alapján. Az alábbi példa a `-VmSize` paraméterrel a méretezési csoportot a *Standard_F1* virtuálisgép-példányméret megadásával hozza létre. Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
A méretezési csoport létrehozása során két virtuálisgép-példányt igényelt. A méretezési csoportban lévő virtuálisgép-példányok számának növeléséhez vagy csökkentéséhez manuálisan módosíthatja a kapacitást. A méretezési csoport létrehozza vagy eltávolítja a szükséges számú virtuálisgép-példányt, majd konfigurálja a terheléselosztót a forgalom elosztásához.

Először hozzon létre egy méretezési csoportot a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) paranccsal, majd adja meg az `sku.capacity` új értékét. A kapacitás módosításának alkalmazásához használja az [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) parancsot. Az alábbi példa a méretezési csoportban lévő virtuálisgép-példányok számát *3*-ra állítja:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja a [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) parancsot:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében látható, hogy a méretezési csoport kapacitása mostantól *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Általános kezelési feladatok
Mostantól létrehozhat egy méretezési csoportot, listát készíthet a kapcsolatadatokról és virtuálisgép-példányokhoz csatlakozhat. Megismerte, hogyan használhatók a különböző operációsrendszer-lemezképek a virtuálisgép-példányokhoz, hogyan választhatók ki a különféle virtuálisgép-méretek és hogyan méretezhető manuálisan a példányok száma. A napi felügyelet részeként szükség lehet a méretezési csoportban lévő virtuálisgép-példányok leállítására, elindítására vagy újraindítására.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok leállítása és felszabadítása
A méretezési csoportban lévő egy vagy több virtuális gép leállításához használja a [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. A következő példa leállítja az *1-es* példányt:

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.

### <a name="start-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok indítása
A méretezési csoportban lévő egy vagy több virtuális gép indításához használja a [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. A következő példa elindítja az *1-es* példányt:

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok újraindítása
A méretezési csoportban lévő egy vagy több virtuális gép újraindításához használja a [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss) parancsot. Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. A következő példa újraindítja az *1-es* példányt:

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforráscsoportok törlésével az összes bennük foglalt erőforrás, azaz a virtuálisgép-példányok, a virtuális hálózat és a lemezek is törölve lesznek. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag az Azure PowerShell néhány alapszintű méretezésicsoport-létrehozási és -kezelési műveletét mutatta be:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

Folytassa a következő oktatóanyaggal, amely a méretezésicsoport-lemezeket ismerteti.

> [!div class="nextstepaction"]
> [Adatlemezek használata méretezési csoportokkal](tutorial-use-disks-powershell.md)
