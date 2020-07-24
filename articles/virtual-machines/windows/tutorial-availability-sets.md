---
title: Oktatóanyag – magas rendelkezésre állás a Windows rendszerű virtuális gépek számára az Azure-ban
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja az Azure PowerShellt magas rendelkezésre állású virtuális gépek üzembe helyezésére a rendelkezésre állási csoportokban
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7fe1c01542df2fcc38982fe2a30f9e94c712eacb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065252"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása és üzembe helyezése az Azure PowerShell-lel

Ebből az oktatóanyagból megtudhatja, hogyan növelheti a Virtual Machines (VM-EK) rendelkezésre állását és megbízhatóságát a rendelkezésre állási csoportok használatával. A rendelkezésre állási csoportok gondoskodnak arról, hogy az Azure-ban üzembe helyezett virtuális gépek több, elkülönített hardverkonfiguráció között legyenek elosztva a fürtben. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése


## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

A rendelkezésre állási csoport egy logikai csoportosítási funkció, amely a virtuálisgép-erőforrások elkülönítését végzi az üzembe helyezésük során. Az Azure biztosítja, hogy a rendelkezésre állási csoporton belüli virtuális gépek több fizikai kiszolgálón, számítási állványokon, tárolási egységeken és hálózati kapcsolókon fussanak. Ha hardveres vagy szoftveres hiba történik, a rendszer csak a virtuális gépek egy részhalmazát érinti, és a teljes megoldás működőképes marad. A rendelkezésre állási csoportok nélkülözhetetlenek a megbízható felhőalapú megoldások létrehozásához.

Vegyünk például egy tipikus virtuálisgép-alapú megoldást négy előtérbeli webkiszolgálóval és két háttérbeli virtuális géppel. Az Azure-ban két rendelkezésre állási csoportot kell megadnia a virtuális gépek üzembe helyezése előtt: egyet a webes réteghez, egyet pedig a hátsó réteghez. Új virtuális gép létrehozásakor paraméterként meg kell adnia a rendelkezésre állási készletet. Az Azure biztosítja, hogy a virtuális gépek több fizikai hardveres erőforrás között legyenek elkülönítve. Ha az egyik kiszolgáló fizikai hardverén fut a probléma, akkor tudja, hogy a kiszolgálók többi példánya továbbra is fut, mert különböző hardveren futnak.

Használjon rendelkezésre állási csoportokat, ha megbízható VM-alapú megoldásokat szeretne üzembe helyezni az Azure-ban.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Az egy adott helyen lévő hardver több frissítési és a tartalék tartományra van osztva. A **frissítési tartomány** virtuális gépek és mögöttes fizikai hardverelemek csoportja, amelyek egyszerre indíthatók újra. Az egyazon **tartalék tartományba** tartozó virtuális gépek közös tárolóval, valamint közös áramforrással és hálózati kapcsolóval rendelkeznek.  

A [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset)használatával létrehozhat egy rendelkezésre állási készletet. Ebben a példában a frissítési és a tartalék tartományok száma *2* , a rendelkezésre állási csoport neve pedig *myAvailabilitySet*.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási készletet a [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) és a `-sku aligned` paraméter használatával.

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
A virtuális gépeket a rendelkezésre állási csoporton belül kell létrehozni annak biztosításához, hogy megfelelően legyenek elosztva a hardveren. A létrehozás után nem adhat hozzá meglévő virtuális gépet egy rendelkezésre állási csoporthoz. 


Amikor [új AzVM](/powershell/module/az.compute/new-azvm)hoz létre egy virtuális gépet, a `-AvailabilitySetName` paraméter használatával megadhatja a rendelkezésre állási csoport nevét.

Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre két virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) a rendelkezésre állási csoporton belül.

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

Ha megtekinti a rendelkezésre állási csoportot a portálon, az **erőforráscsoportok**  >  **myResourceGroupAvailability**  >  **myAvailabilitySet**, látnia kell, hogyan oszlanak meg a virtuális gépek a két hiba és a frissítési tartomány között.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

Amikor létrehoz egy virtuális gépet egy rendelkezésre állási csoporton belül, tudnia kell, hogy milyen virtuálisgép-méretek érhetők el a hardveren. Használja a [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) parancsot a rendelkezésre állási csoportba központilag telepíthető virtuális gépek összes elérhető méretének lekéréséhez.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Azure Advisor segítségével további információkat kaphat a virtuális gépek rendelkezésre állásának javításáról. Azure Advisor elemzi a konfigurációt és a használat telemetria, majd az Azure-erőforrások költséghatékonyságának, teljesítményének, rendelkezésre állásának és biztonságának javítását segítő megoldásokat ajánl fel.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), válassza a **Minden szolgáltatás** lehetőséget, és írja be az **Advisor** kifejezést. Az Advisor-irányítópult személyre szabott javaslatokat jelenít meg a kiválasztott előfizetéshez. További információért lásd [az Azure Advisor használatának első lépéseit](../../advisor/advisor-get-started.md).


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
