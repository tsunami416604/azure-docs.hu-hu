---
title: Nyilvános IP-cím társítása virtuális géphez
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan társíthat nyilvános IP-címet egy virtuális géphez.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647458"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Nyilvános IP-cím társítása virtuális géphez

Ebben a cikkben megtudhatja, hogyan társíthat nyilvános IP-címet egy meglévő virtuális géphez (VM). Ha szeretne csatlakozni egy virtuális gép hez az internetről, a virtuális gép nek rendelkeznie kell egy nyilvános IP-címet társított. Ha nyilvános IP-címmel rendelkező új virtuális gép et szeretne létrehozni, ezt megteheti az [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), az [Azure parancssori felület (CLI)](virtual-network-deploy-static-pip-arm-cli.md)vagy a [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)használatával. A nyilvános IP-címek névleges díjkötelesek. További információt az [árképzés ben](https://azure.microsoft.com/pricing/details/ip-addresses/)talál. Az előfizetésenként használható nyilvános IP-címek száma korlátozva van. További részletek: [limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Használhatja az [Azure Portalon,](#azure-portal)az Azure [parancssori felület](#azure-cli) (CLI) vagy a [PowerShell](#powershell) társíthatja a nyilvános IP-címet egy virtuális géphez.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Tallózással keresse meg, vagy keresse meg azt a virtuális gépet, amelyhez hozzá szeretné adni a nyilvános IP-címet, majd jelölje ki.
3. A **Beállítások csoportban**válassza a **Hálózat**lehetőséget, majd jelölje ki azt a hálózati adaptert, amelyhez hozzá szeretné adni a nyilvános IP-címet, ahogy az az alábbi képen látható:

   ![Hálózati adapter kiválasztása](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > A nyilvános IP-címek a virtuális géphez csatlakoztatott hálózati adapterekhez vannak társítva. Az előző képen a virtuális gép csak egy hálózati adapter. Ha a virtuális gép több hálózati adapterek, akkor minden jelenik meg, és akkor válassza ki a hálózati adapter szeretné társítani a nyilvános IP-címet.

4. Válassza az **IP-konfigurációkat,** majd válasszon egy IP-konfigurációt, ahogy az az alábbi képen látható:

   ![IP-konfiguráció kiválasztása](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > A nyilvános IP-címek a hálózati adapter IP-konfigurációihoz vannak társítva. Az előző képen a hálózati adapter egy IP-konfigurációval rendelkezik. Ha a hálózati adapter több IP-konfigurációval rendelkezik, azok mind megjelennek a listában, és ki kell választania azt az IP-konfigurációt, amelyhez a nyilvános IP-címet társítani szeretné.

5. Válassza **az Engedélyezve**lehetőséget, majd válassza **az IP-cím *(A szükséges beállítások megadása)*** lehetőséget. Válasszon egy meglévő nyilvános IP-címet, amely automatikusan bezárja a **Nyilvános IP-cím kiválasztása mezőt.** Ha nem rendelkezik elérhető nyilvános IP-címekkel, létre kell hoznia egyet. Ebből megtudhatja, hogyan, olvassa el [a Nyilvános IP-cím létrehozása című témakört.](virtual-network-public-ip-address.md#create-a-public-ip-address) Válassza a **Mentés**lehetőséget, ahogy az a következő képen látható, majd zárja be az IP-konfiguráció jelölőnégyzetét.

   ![Nyilvános IP-cím engedélyezése](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > A nyilvános IP-címek jelennek meg azok, amelyek ugyanabban a régióban, mint a virtuális gép. Ha több nyilvános IP-címet hozott létre a régióban, itt jelenik meg. Ha bármelyik szürkén jelenik meg, annak az az oka, hogy a cím már egy másik erőforráshoz van társítva.

6. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet, ahogy az a következő képen látható. Az IP-cím megjelenése eltarthat néhány másodpercig.

   ![Hozzárendelt nyilvános IP-cím megtekintése](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészletek listáját a [Microsoft Azure Datacenter DATACenter IP-tartományok című témakörben tájékírja.](https://www.microsoft.com/download/details.aspx?id=41653) A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a címet a régió egy adott készletéből szeretné hozzárendelni, használjon [nyilvános IP-címelőtagot.](public-ip-address-prefix.md)

7. [Hálózati forgalom engedélyezése a virtuális gép biztonsági](#allow-network-traffic-to-the-vm) szabályokegy hálózati biztonsági csoportban.

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy használja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A cli parancsokban válassza a **Try it (Kipróbálás)** gombot. Válassza a **Próbálja meg** egy felhőshell, amely bejelentkezhet az Azure-fiókba.

1. Ha a CLI-t helyileg használja a `az login`Bash alkalmazásban, jelentkezzen be az Azure-ba a segítségével.
2. A nyilvános IP-cím egy virtuális géphez csatlakoztatott hálózati adapter IP-konfigurációjához van társítva. Az [az hálózati nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) paranccsal társíthat egy nyilvános IP-címet egy IP-konfigurációhoz. A következő példa egy *myVMPublicIP* nevű meglévő nyilvános IP-címet társít egy *myVMVMNevű* meglévő hálózati illesztő *ipconfigmyVM* nevű IP-konfigurációjához, amely egy *myResourceGroup*nevű erőforráscsoportban található.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Ha nem rendelkezik egy meglévő nyilvános IP-címet, az [az-hálózat nyilvános ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) paranccsal hozzon létre egyet. A következő parancs például létrehoz egy *myVMPublicIP* nevű nyilvános IP-címet egy *myResourceGroup*nevű erőforráscsoportban.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Az előző parancs nyilvános IP-címet hoz létre, amely több olyan beállításalapértelmezett értékeit tartalmaz, amelyeket esetleg testre szeretne szabni. Ha többet szeretne tudni a nyilvános IP-cím beállításokról, olvassa el [a Nyilvános IP-cím létrehozása című témakört.](virtual-network-public-ip-address.md#create-a-public-ip-address) A cím az egyes Azure-régiókhoz használt nyilvános IP-címek készletéből van hozzárendelve. Az egyes régiókban használt címkészletek listáját a [Microsoft Azure Datacenter DATACenter IP-tartományok című témakörben tájékírja.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Ha nem tudja a virtuális géphez csatolt hálózati adapter nevét, az [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) parancs segítségével tekintse meg őket. A következő parancs például felsorolja a *myResourceGroup*nevű erőforráscsoportban egy *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában a *myVMVMNic* a hálózati adapter neve.

   - Ha nem tudja a hálózati adapter IP-konfigurációjának nevét, az [az hálózati nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) paranccsal olvassa be azokat. A következő parancs például felsorolja egy *myVMVMNic* nevű hálózati illesztő IP-konfigurációjának nevét egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános [IP-címet az az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) paranccsal. A következő példa a *myResourceGroup*nevű erőforráscsoport ban egy *myVM* nevű meglévő virtuális géphez rendelt IP-címeket mutatja be.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészletek listáját a [Microsoft Azure Datacenter DATACenter IP-tartományok című témakörben tájékírja.](https://www.microsoft.com/download/details.aspx?id=41653) A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a címet a régió egy adott készletéből szeretné hozzárendelni, használjon [nyilvános IP-címelőtagot.](public-ip-address-prefix.md)

4. [Hálózati forgalom engedélyezése a virtuális gép biztonsági](#allow-network-traffic-to-the-vm) szabályokegy hálózati biztonsági csoportban.

## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt,](/powershell/azure/install-az-ps)vagy használja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. A PowerShell előre telepítve van, és a fiókjával való használatra van konfigurálva. Válassza a **Try it** gombot a következő PowerShell-parancsokban. Válassza a **Próbálja meg** egy felhőshell, amely bejelentkezhet az Azure-fiókba.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a segítségével. `Connect-AzAccount`
2. A nyilvános IP-cím egy virtuális géphez csatlakoztatott hálózati adapter IP-konfigurációjához van társítva. A [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) és a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) parancsokkal lejuthat a virtuális hálózatés alhálózat leésével, amelyben a hálózati illesztő található. Ezután a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal beszerezhet egy hálózati adaptert, a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) paranccsal pedig lekell szereznie egy meglévő nyilvános IP-címet. Ezután a [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) paranccsal társíthatja a nyilvános IP-címet az IP-konfigurációhoz, a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) paranccsal pedig az új IP-konfigurációt a hálózati adapterhez.

   A következő példa egy *myVMPublicIP* nevű meglévő nyilvános IP-címet társít egy *myVMVMNic* nevű meglévő hálózati illesztő *ipconfigmyVM* nevű IP-konfigurációjához, amely egy *myVMSubnet* nevű alhálózatban található egy *myVMVNet*nevű alhálózatban. Minden erőforrás egy *myResourceGroup*nevű erőforráscsoportba tartozik.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Ha nem rendelkezik meglévő nyilvános IP-címmel, a [New-AzPublicIpAddress paranccsal](/powershell/module/Az.Network/New-AzPublicIpAddress) hozzon létre egyet. A következő parancs például *létrehoz* egy dinamikus nyilvános IP-címet *myVMPublicIP* néven egy *myResourceGroup* nevű erőforráscsoportban az *eastus* régióban.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Az előző parancs nyilvános IP-címet hoz létre, amely több olyan beállításalapértelmezett értékeit tartalmaz, amelyeket esetleg testre szeretne szabni. Ha többet szeretne tudni a nyilvános IP-cím beállításokról, olvassa el [a Nyilvános IP-cím létrehozása című témakört.](virtual-network-public-ip-address.md#create-a-public-ip-address) A cím az egyes Azure-régiókhoz használt nyilvános IP-címek készletéből van hozzárendelve. Az egyes régiókban használt címkészletek listáját a [Microsoft Azure Datacenter DATACenter IP-tartományok című témakörben tájékírja.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Ha nem tudja a virtuális géphez csatlakoztatott hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal tekintse meg őket. A következő parancs például felsorolja a *myResourceGroup*nevű erőforráscsoportban egy *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *myVMVMNic* a neve a hálózati adapter.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem tudja annak a virtuális hálózatnak vagy alhálózatnak a nevét, amelyben a hálózati adapter található, a `Get-AzNetworkInterface` paranccsal tekintse meg az adatokat. A következő parancs például lekéri egy *myVMVMNic* nevű hálózati adapter virtuális hálózati és alhálózati adatait egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *myVMVNET* a virtuális hálózat neve, *és myVMSubnet* a neve az alhálózat.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Ha nem tudja a hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal olvassa be azokat. A következő parancs például felsorolja egy *myVMVMNic* nevű hálózati illesztő IP-konfigurációjának nevét egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *ipconfigmyVM* egy IP-konfiguráció neve.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános [IP-címet a Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) paranccsal. A következő példa a *myVMPublicIP* nevű nyilvános IP-címhez rendelt címet mutatja be egy *myResourceGroup*nevű erőforráscsoportban.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Ha nem tudja az IP-konfigurációhoz rendelt nyilvános IP-cím nevét, futtassa a következő parancsokat a bekésezéséhez:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *myVMPublicIP* az IP-konfigurációhoz rendelt nyilvános IP-cím neve.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészletek listáját a [Microsoft Azure Datacenter DATACenter IP-tartományok című témakörben tájékírja.](https://www.microsoft.com/download/details.aspx?id=41653) A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a címet a régió egy adott készletéből szeretné hozzárendelni, használjon [nyilvános IP-címelőtagot.](public-ip-address-prefix.md)

4. [Hálózati forgalom engedélyezése a virtuális gép biztonsági](#allow-network-traffic-to-the-vm) szabályokegy hálózati biztonsági csoportban.

## <a name="allow-network-traffic-to-the-vm"></a>Hálózati forgalom engedélyezése a virtuális gépre

Mielőtt az internetről csatlakozhatna a nyilvános IP-címhez, győződjön meg arról, hogy a szükséges portok meg vannak nyitva bármely hálózati biztonsági csoportban, amelya hálózati adapterhez, a hálózati adapter alhálózatához kapcsolódik, vagy mindkettőben. Bár a biztonsági csoportok a hálózati csatoló privát IP-címére szűrik a forgalmat, amint a bejövő internetes forgalom megérkezik a nyilvános IP-címre, az Azure a nyilvános címet a privát IP-címre fordítja le, így ha egy hálózati biztonsági csoport megakadályozza a a nyilvános IP-címmel folytatott kommunikáció sikertelen. A hálózati adapterek és alhálózatának hatékony biztonsági szabályait a [Portál](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [cli](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)vagy [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)segítségével tekintheti meg.

## <a name="next-steps"></a>További lépések

Bejövő internetes forgalmat engedélyezhet a virtuális géphez egy hálózati biztonsági csoporttal. A hálózati biztonsági csoport létrehozásáról a [Hálózati biztonsági csoportok kal című témakörben](manage-network-security-group.md#work-with-network-security-groups)olvashat. A hálózati biztonsági csoportokról a [Biztonsági csoportok](security-overview.md)ról olvashat bővebben.
