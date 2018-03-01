---
title: "Rendelkezésre állási csoportokra vonatkozó oktatóanyag Azure-beli Windows virtuális gépekhez | Microsoft Docs"
description: "Tudnivalók az Azure-beli Windows virtuális gépek rendelkezésre állási csoportjaival kapcsolatban."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 247f86dafe35d69dd742583d246862b739d9fe90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-use-availability-sets"></a>A rendelkezésre állási csoportok használata

Ebben az oktatóanyagban megtanulhatja, hogyan növelheti Azure-beli virtuálisgép-megoldásai rendelkezésre állását és megbízhatóságát a rendelkezésre állási csoportok elnevezésű képesség használatával. A rendelkezésre állási csoportok biztosítják, hogy az Azure-ban üzembe helyezett virtuális gépek több elkülönített hardvercsomópont között legyenek elosztva a fürtben. Ezáltal biztosítható, hogy ha hardveres vagy szoftveres hiba fordul elő az Azure-ban, az a virtuális gépeknek csak egy részhalmazát érintse, és a teljes megoldás továbbra is elérhető és működőképes maradjon. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése
> * Az Azure Advisor ellenőrzése

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

A rendelkezésre állási csoport egy logikai csoportosítási funkció, amelyet az Azure-ban használva biztosíthatja, hogy a csoportba helyezett virtuálisgép-erőforrások egymástól elkülönítve legyenek üzembe helyezve az Azure-adatközpontokban. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Ha hardveres vagy Azure-beli szoftveres hiba lép fel, az a virtuális gépeknek csak egy részhalmazát érinti, a teljes alkalmazás azonban tovább üzemel, és továbbra is elérhető marad az ügyfelek számára. A rendelkezésre állási csoport egy alapvető funkció, ha megbízható felhőalapú megoldásokat kíván létrehozni.

Vegyünk például egy tipikus virtuálisgép-alapú megoldást négy előtérbeli webkiszolgálóval és két háttérbeli virtuális géppel, amelyek egy adatbázist futtatnak. Az Azure-ban két rendelkezésre állási csoportot érdemes meghatározni a virtuális gépek üzembe helyezése előtt: egy rendelkezésre állási csoportot a webes szint, egyet pedig az adatbázisszint számára. Amikor létrehoz egy új virtuális gépet, megadhatja a rendelkezésre állási csoportot paraméterként az „az vm create” parancsban. Ekkor az Azure automatikusan biztosítja, hogy a rendelkezésre állási csoportban létrehozott virtuális gépek el legyenek különítve több fizikai hardvererőforráson. Ha a fizikai hardver, amelyen az egyik webes vagy adatbázis-kiszolgáló virtuális gép üzemel, meghibásodik, biztos lehet abban, hogy a webes és az adatbázis-kiszolgáló virtuális gép többi példánya tovább üzemel, mivel azok más hardveren találhatók.

Használjon rendelkezésre állási csoportokat, ha megbízható VM-alapú megoldásokat szeretne üzembe helyezni az Azure-ban.

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási csoportot a [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) paranccsal hozhat létre. Ebben a példában a frissítési és a tartalék tartományok számát egyaránt *2*-re állítjuk a *myResourceGroupAvailability* erőforráscsoport *myAvailabilitySet* nevű rendelkezésre állási csoportja esetében.

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Hozzon létre egy felügyelt rendelkezésre állási csoportot a [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) paranccsal, az `-sku aligned` paraméterrel.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Virtuális gépek létrehozása rendelkezésre állási csoportban
A virtuális gépeket a rendelkezésre állási csoportban kell létrehozni, hogy azok biztosan megfelelően legyenek elosztva a hardveren. Meglévő virtuális gépet nem adhat hozzá rendelkezésre állási csoporthoz annak létrejötte után. 

Az egy adott helyen lévő hardver több frissítési és a tartalék tartományra van osztva. A **frissítési tartomány** virtuális gépek és mögöttes fizikai hardverelemek csoportja, amelyek egyszerre indíthatók újra. Az egyazon **tartalék tartományba** tartozó virtuális gépek közös tárolóval, valamint közös áramforrással és hálózati kapcsolóval rendelkeznek. 

Ha a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal hoz létre virtuális gépet, az `-AvailabilitySetName` paraméterrel adhatja meg a rendelkezésre állási csoport nevét.

Először a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre két virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal a rendelkezésre állási csoportban.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
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

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Job` parancsmaggal tekintheti meg. A két virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe. Ha befejeződött, két virtuális géppel rendelkezik majd elosztva a mögöttes hardveren. 

Ha megtekinti a rendelkezésre állási csoportot a portálon az Erőforráscsoportok > myResourceGroupAvailability > myAvailabilitySet pont alatt, láthatja, hogy a virtuális gépek hogyan oszlanak el a két tartalék és frissítési tartományban.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

A rendelkezésre állási csoportot később további virtuális gépekkel bővítheti, azonban tudnia kell, milyen virtuálisgép-méretek érhetők el a hardveren. A [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) paranccsal listázhatja a rendelkezésre állási csoport hardverfürtjén elérhető összes méretet.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Az Azure Advisor ellenőrzése 

Az Azure Advisor használatával is kaphat további tanácsokat a virtuális gépek rendelkezésre állásának javítására vonatkozóan. Az Azure Advisor segít alkalmazni az Azure-környezetek optimalizálására vonatkozó ajánlott eljárásokat. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), válassza a **Minden szolgáltatás** lehetőséget, és írja be az **Advisor** kifejezést. Az Advisor irányítópult személyre szabott javaslatokat jelenít meg a kiválasztott előfizetésre vonatkozóan. További információért lásd [az Azure Advisor használatának első lépéseit](../../advisor/advisor-get-started.md).


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


