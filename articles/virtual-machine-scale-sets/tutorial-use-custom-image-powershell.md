---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerkép használata egy méretezési csoportban az Azure PowerShell használatával | Microsoft Docs
description: Megismerheti, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket az Azure PowerShell használatával, amelyek használatával virtuálisgép-méretezési csoportokat helyezhet üzembe
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a3b0f9b2b158bd36259ee96633682e1777333499
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981043"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Oktatóanyag: Hozzon létre, és a egy egyéni rendszerkép használata a virtuálisgép-méretezési csoportok az Azure PowerShell használatával

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. Egyéni virtuálisgép-rendszerkép használatával csökkentheti a feladatok számát a virtuálisgép-példányok üzembe helyezése után. Ez az egyéni virtuálisgép-rendszerkép tartalmaz minden szükséges alkalmazástelepítést és -konfigurációt. A méretezési csoportban létrehozott összes virtuálisgép-példány az egyéni virtuálisgép-rendszerképet használja, és készen állnak az alkalmazás forgalmának kiszolgálására. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális gép létrehozása és testre szabása
> * A virtuális gép megszüntetése és általánosítása
> * Egyéni virtuálisgép-rendszerkép létrehozása a forrás virtuális gépről
> * Az egyéni virtuálisgép-rendszerképet használó méretezési csoport üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="create-and-configure-a-source-vm"></a>Forrás virtuális gép létrehozása és konfigurálása

>[!NOTE]
> Ez az oktatóanyag bemutatja az általánosított virtuálisgép-rendszerképek létrehozásának és használatának folyamatát. A méretezési csoportok speciális virtuális merevlemezről való létrehozása nem támogatott.

Először hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), majd hozzon létre egy virtuális Gépet a [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). Ez a virtuális gép szolgál majd az egyéni virtuálisgép-rendszerkép forrásaként. A következő példában létrehozunk egy *myCustomVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoportban. Amikor a rendszer kéri, adja meg a virtuális gép bejelentkezési hitelesítő adataiként használni kívánt felhasználónevet és jelszót:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

A virtuális Géphez való csatlakozáshoz, a listában a nyilvános IP-címet [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) módon:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Hozzon létre távoli kapcsolatot a virtuális géppel. Az Azure Cloud Shell használata esetén ezt a lépést egy helyi PowerShell-parancssorból vagy egy távoli asztali ügyfélről hajtsa végre. Adja meg a saját IP-címét az előző parancsból. Amikor a rendszer kéri, adja meg az első lépésben, a virtuális gép létrehozásakor használt hitelesítő adatokat:

```powershell
mstsc /v:<IpAddress>
```

A virtuális gép testre szabásához telepítünk egy alapszintű webkiszolgálót. A méretezési csoport virtuálisgép-példánya az üzembe helyezéskor már rendelkezni fog a webalkalmazás futtatásához szükséges összes csomaggal. Nyisson meg egy helyi PowerShell-parancssort a virtuális gépen, és telepítse az IIS-webkiszolgálót az [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) paranccsal az alábbi módon:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

A virtuális gépnek az egyéni rendszerkép használatára való előkészítésének utolsó lépése a virtuális gép általánosítása. A Sysprep minden személyes fiókadatot és -konfigurációt eltávolít, és visszaállítja a virtuális gépet üres állapotba a később üzembe helyezések számára. További információkért lásd: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).

A virtuális gép általánosításához futtassa a Sysprep eszközt, és állítsa a virtuális gépet azonnali élményre. Amikor végzett, utasítsa a Sysprep eszközt a virtuális gép leállítására:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

A virtuális géppel való távoli kapcsolat automatikusan megszakad, amikor a Sysprep befejezte a folyamatot és a virtuális gép leállt.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Egyéni virtuálisgép-rendszerkép létrehozása a forrás virtuális gépről
A forrás virtuális gép most már testre van szabva, és az IIS-webkiszolgáló is telepítve van. Hozzuk létre a méretezési csoporthoz használni kívánt egyéni virtuálisgép-rendszerképet.

Rendszerkép létrehozásához fel kell szabadítani a virtuális gépet. Szabadítsa fel a virtuális Gépet a [Stop-azvm parancsmag](/powershell/module/az.compute/stop-azvm). Ezután adja meg a virtuális gép állapotát általánosítottra az [Set-azvm parancsmag](/powershell/module/az.compute/set-azvm) , hogy az Azure platform felismeri, hogy a virtuális gép készen áll használatra egy egyéni rendszerkép. Rendszerképet csak általánosított virtuális gépből hozhat létre:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

A virtuális gép felszabadítása és általánosítása eltarthat néhány percig.

Most hozzon létre egy rendszerképet a virtuális Gépet a [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) és [New-AzImage](/powershell/module/az.compute/new-azimage). Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a virtuális gépéből:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Méretezési csoport létrehozása az egyéni virtuálisgép-rendszerképből
Most hozzon létre egy méretezési csoportot az [New-AzVmss](/powershell/module/az.compute/new-azvmss) , amely használja a `-ImageName` az előző lépésben létrehozott paraméter használatával határozza meg az egyéni Virtuálisgép-rendszerképet. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó szabályai elosztják a 80-as TCP-porton érkező forgalmat, valamint lehetővé teszik a távoli asztali forgalmat a 3389-es, valamint a PowerShell távoli eljáráshívásokat az 5985-ös TCP-porton. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
A méretezési csoport működés közben, kérje le a terheléselosztó a nyilvános IP-cím megtekintéséhez [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) módon:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Gépelje be a nyilvános IP-címet a webböngészőjébe. Az alapértelmezett IIS-weboldal jelenik meg, ahogy az az alábbi példában is látható:

![Egyéni virtuálisgép-rendszerképről futtatott IIS](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást, az [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképet a méretezési csoportjai esetében az Azure PowerShell segítségével:

> [!div class="checklist"]
> * Virtuális gép létrehozása és testre szabása
> * A virtuális gép megszüntetése és általánosítása
> * Egyéni virtuális gép rendszerképének létrehozása
> * Az egyéni virtuálisgép-rendszerképet használó méretezési csoport üzembe helyezése

A következő oktatóanyag azt mutatja be, hogyan helyezhet alkalmazásokat üzembe a méretezési csoportban.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a méretezési csoportban](tutorial-install-apps-powershell.md)
