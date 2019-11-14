---
title: Rövid útmutató – Windows rendszerű virtuális gép létrehozása Azure PowerShell
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan használható az Azure PowerShell Windows rendszerű virtuális gépek létrehozásra
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 607ad17af42b88737d8ca4ebc379504d9b0b40df
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073454"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure-ban PowerShell-lel

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használja az Azure PowerShell modult a Windows Server 2016-ot futtató virtuális gép üzembe helyezésére az Azure-ban. Az RDP-t a virtuális gépre is telepíti, és telepítenie kell az IIS-webkiszolgálót a virtuális gép működésének megjelenítéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Adja meg az egyes erőforrások nevét, és a `New-AzVM` parancsmag hozza létre, ha még nem léteznek.

Ha a rendszer kéri, adja meg a virtuális gép bejelentkezési hitelesítő adataiként használandó felhasználónevet és jelszót:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeződése után hozzon létre RDP-kapcsolatot a virtuális géphez. A virtuális gép működés közbeni megtekintéséhez telepítse az IIS webkiszolgálót.

A virtuális gép nyilvános IP-címének megtekintéséhez használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) parancsmagot:

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Az alábbi parancs használatával hozzon létre egy távoli asztali munkamenetet a helyszíni számítógépéről. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. 

```powershell
mstsc /v:publicIpAddress
```

A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet **localhost**\\*felhasználónév* formátumban, és adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK** gombra.

A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy **Folytatás** gombra

## <a name="install-web-server"></a>Webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha elkészült, zárja be a virtuális géphez nyitott RDP-kapcsolatot.

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló működésének ellenőrzése

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Használja a virtuális gép az egyik előző lépésben megkapott nyilvános IP-címét. Az alábbi példa az alapértelmezett IIS-webhelyet mutatja:

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalom számára, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
