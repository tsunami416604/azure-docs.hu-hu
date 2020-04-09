---
title: Oktatóanyag – Windows-virtuális gépek magas rendelkezésre állása az Azure-ban
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja az Azure PowerShellt magas rendelkezésre állású virtuális gépek üzembe helyezésére a rendelkezésre állási csoportokban
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 59bf06d2b279bad792bdc42a7c3b6acc2bc304b8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985711"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása és üzembe helyezése az Azure PowerShell-lel

Ebben az oktatóanyagban megtudhatja, hogyan növelheti a virtuális gépek (VM-ek) rendelkezésre állását és megbízhatóságát a rendelkezésre állási készletek használatával. A rendelkezésre állási csoportok győződjön meg arról, hogy az Azure-ban üzembe helyezett virtuális gépek több, elkülönített hardvercsomópont között vannak elosztva egy fürtben. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése


## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

A rendelkezésre állási csoport egy logikai csoportosítási képesség a virtuális gép erőforrásainak egymástól való elkülönítése, amikor üzembe helyeziőket. Az Azure gondoskodik arról, hogy a rendelkezésre állási csoporton belül helyezett virtuális gépek több fizikai kiszolgálón, számítási állványokon, tárolóegységeken és hálózati kapcsolókon fussanak. Ha hardver- vagy szoftverhiba történik, a virtuális gépeknek csak egy részhalmaza érintett, és a teljes megoldás működőképes marad. A rendelkezésre állási készletek elengedhetetlenek a megbízható felhőalapú megoldások létrehozásához.

Vegyünk például egy tipikus virtuálisgép-alapú megoldást négy előtérbeli webkiszolgálóval és két háttérbeli virtuális géppel. Az Azure-ral két rendelkezésre állási készletet szeretne definiálni a virtuális gépek üzembe helyezése előtt: egyet a webes réteghez és egyet a hátsó réteghez. Amikor új virtuális gép létrehozása, adja meg a rendelkezésre állási készlet paraméterként. Az Azure gondoskodik arról, hogy a virtuális gépek több fizikai hardvererőforrás között vannak elkülönítve. Ha az egyik kiszolgáló által futtatott fizikai hardver probléma, akkor tudja, hogy a kiszolgálók többi példánya továbbra is futni fog, mert különböző hardveren vannak.

Használjon rendelkezésre állási csoportokat, ha megbízható VM-alapú megoldásokat szeretne üzembe helyezni az Azure-ban.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Az egy adott helyen lévő hardver több frissítési és a tartalék tartományra van osztva. A **frissítési tartomány** virtuális gépek és mögöttes fizikai hardverelemek csoportja, amelyek egyszerre indíthatók újra. Az egyazon **tartalék tartományba** tartozó virtuális gépek közös tárolóval, valamint közös áramforrással és hálózati kapcsolóval rendelkeznek.  

A [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset)beállítással rendelkezésre állási csoportot hozhat létre. Ebben a példában a frissítési és a tartalék tartományok száma *2,* és az elérhetőségi készlet neve *myAvailabilitySet*.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási készletet a [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) használatával a `-sku aligned` paraméterrel.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Virtuális gépek létrehozása rendelkezésre állási csoportban
Virtuális gépeket kell létrehozni a rendelkezésre állási csoporton belül, hogy megbizonyosodjon arról, hogy azok megfelelően elosztva a hardver között. Nem adhat hozzá egy meglévő virtuális gép egy rendelkezésre állási csoport létrehozása után. 


Amikor létrehoz egy virtuális gép [a New-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/new-azvm)használja a `-AvailabilitySetName` paramétert, hogy adja meg a rendelkezésre állási csoport nevét.

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre két virtuális gépet [a New-AzVM-mel](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) a rendelkezésre állási csoportban.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

A két virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe. Ha befejeződött, két virtuális géppel rendelkezik majd elosztva a mögöttes hardveren. 

Ha megnézi a rendelkezésre állási készlet a portálon az **erőforráscsoportok** > **myResourceGroupAvailability** > **myAvailabilitySet,** látnia kell, hogy a virtuális gépek között a két hiba és a tartomány frissítése.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

Amikor virtuális gép egy rendelkezésre állási csoporton belül, tudnia kell, hogy milyen virtuális gép méretei érhetők el a hardveren. A [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) paranccsal lekéri a rendelkezésre állási csoportban üzembe helyezhető virtuális gépek összes rendelkezésre álló méretét.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Az Azure Advisor segítségével további információkat kaphat a virtuális gépek rendelkezésre állásának javításáról. Az Azure Advisor elemzi a konfigurációs és használati telemetriai adatokat, majd olyan megoldásokat javasol, amelyek segíthetnek az Azure-erőforrások költséghatékonyságának, teljesítményének, rendelkezésre állásának és biztonságának javításában.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), válassza a **Minden szolgáltatás** lehetőséget, és írja be az **Advisor** kifejezést. Az Advisor irányítópulton személyre szabott javaslatok jelennek meg a kiválasztott előfizetéshez. További információért lásd [az Azure Advisor használatának első lépéseit](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-méretezési csoportokat ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)


