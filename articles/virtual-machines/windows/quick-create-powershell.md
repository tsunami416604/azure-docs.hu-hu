---
title: "Azure gyors üzembe helyezés – Windows virtuális gép létrehozása PowerShell segítségével | Microsoft Docs"
description: "Gyorsan megismerheti a Windows rendszerű virtuális gép PowerShell segítségével való létrehozásának módját"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ba0a0c336fbdab91121f5c4bff00b24c1088bd8a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>Windows rendszerű virtuális gép létrehozása PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató részletesen bemutatja, hogyan lehet egy Windows Server 2016-ot futtató Azure virtuális gépet létrehozni a PowerShell használatával. Az üzembe helyezés végeztével csatlakozunk a kiszolgálóhoz, és telepítjük az IIS-t.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.



## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal. Csak meg kell adnia az erőforrások nevét, és a New-AzureRMVM parancsmag létrehozza őket, ha még nem léteznek.

Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM$(Get-Random)" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

A virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Jegyezze fel ezt az IP-címet, hogy egy későbbi lépésben csatlakozhasson majd hozzá a böngészőjében a webes kapcsolat tesztelése érdekében.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géppel. Cserélje le az IP-címet a virtuális gépe *publicIPAddress* címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>IIS telepítése a PowerShell használatával

Miután bejelentkezett az Azure-beli virtuális gépre, egyetlen PowerShell-utasítással telepítheti az IIS-t, és engedélyezheti, hogy a helyi tűzfalszabály átengedje a webforgalmat. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált *publicIPAddress* használatával keresse fel. 

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, egy hálózati biztonsági csoport szabályát, valamint telepített egy webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
