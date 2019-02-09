---
title: Oktatóanyag – Magas rendelkezésre állás az Azure-beli Windows rendszerű virtuális gépeken | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja az Azure PowerShellt magas rendelkezésre állású virtuális gépek üzembe helyezésére a rendelkezésre állási csoportokban
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3ee9740f9ef7e364c47bb205315683d1e4ea9294
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977125"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Hozzon létre, és az Azure PowerShell használatával magas rendelkezésre állású virtuális gépek üzembe helyezése

Ebben az oktatóanyagban elsajátíthatja, hogyan növelheti a rendelkezésre állása és megbízhatósága a virtuális gépek (VM) rendelkezésre állási csoportokkal. A rendelkezésre állási csoportok ellenőrizze, hogy az Azure-ban üzembe helyezett virtuális gépek több elkülönített hardvercsomópont a fürtben vannak elosztva. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése


## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

Rendelkezésre állási csoportban egy logikai csoportosítási funkció egymástól a virtuális gép feladatában felderítéséhez használt telepítésekor. Az Azure gondoskodik arról, hogy a virtuális gépek elhelyezésekor rendelkezésre állási csoportba több fizikai kiszolgálón futtassa, állványokon, tárolási egységben és hálózati kapcsolók számítási. Egy hardveres vagy szoftveres hiba történik, ha a virtuális gépeknek csak egy részhalmazát érinti, és a teljes megoldás működési marad. A rendelkezésre állási csoportok nélkülözhetetlenek a megbízható felhőalapú megoldások létrehozásához.

Vegyünk például egy tipikus virtuálisgép-alapú megoldást négy előtérbeli webkiszolgálóval és két háttérbeli virtuális géppel. Az Azure-ban két rendelkezésre állási csoportok meghatározására a virtuális gépek üzembe helyezése előtt érdemes: egyet a webes szint és a egy vissza szintjéhez. Amikor létrehoz egy új virtuális Gépet, adja meg a rendelkezésre állási csoportot paraméterként. Az Azure biztosítja, hogy a virtuális gépek legyenek különítve több fizikai hardvererőforráson. A fizikai hardver, amely egyik kiszolgálóján fut-e hibásan működik, ha ismeri a többi példány a kiszolgálók úgy működik tovább, mert azok más hardveren.

Használjon rendelkezésre állási csoportokat, ha megbízható VM-alapú megoldásokat szeretne üzembe helyezni az Azure-ban.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Az egy adott helyen lévő hardver több frissítési és a tartalék tartományra van osztva. A **frissítési tartomány** virtuális gépek és mögöttes fizikai hardverelemek csoportja, amelyek egyszerre indíthatók újra. Az egyazon **tartalék tartományba** tartozó virtuális gépek közös tárolóval, valamint közös áramforrással és hálózati kapcsolóval rendelkeznek.  

Létrehozhat egy rendelkezésre állási csoportot a [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). Ebben a példában a frissítési és a tartalék tartományok száma van *2* , és a rendelkezésre állási csoport neve *myAvailabilitySet*.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási csoportot a [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) együtt a `-sku aligned` paraméter.

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
Virtuális gépek rendelkezésre állási csoportja, győződjön meg arról, hogy megfelelően elosztva a hardveren belül kell létrehoznia. Egy meglévő virtuális Gépet egy rendelkezésre állási csoport létrehozása után nem adhat hozzá. 


Amikor létrehoz egy virtuális Gépet a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), használja a `-AvailabilitySetName` paramétert adja meg a rendelkezésre állási csoport nevét.

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre két virtuális gépet a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) a rendelkezésre állási csoportban.

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

Ha megtekinti a rendelkezésre állási csoportot a portálon a **erőforráscsoportok** > **Myavailabilityset** > **myAvailabilitySet**, hogy a virtuális gépek vannak elosztva a két tartalék és frissítési tartományokban kell megjelennie.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

A rendelkezésre állási csoportot később további virtuális gépekkel bővítheti, azonban tudnia kell, milyen virtuálisgép-méretek érhetők el a hardveren. Használat [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) kilistázásához az elérhető méretek a hardvert a fürthöz a rendelkezésre állási csoport.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Az Azure Advisor használatával további tudnivalók a virtuális gépek rendelkezésre állásának javítása érdekében. Az Azure Advisor elemzi az konfigurációjának és használatának telemetriai adatait, akkor javasolja a megoldásokat, amelyek alapján javítható a költséghatékonysága, teljesítménye, rendelkezésre állási és az Azure-erőforrások biztonságát.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), válassza a **Minden szolgáltatás** lehetőséget, és írja be az **Advisor** kifejezést. Az Advisor irányítópult személyre szabott javaslatait a kiválasztott előfizetéshez tartozó mutatja be. További információért lásd [az Azure Advisor használatának első lépéseit](../../advisor/advisor-get-started.md).


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


