---
title: Nyilvános IP-cím leválaszthatása Azure-beli virtuális gépről
titlesuffix: Azure Virtual Network
description: A nyilvános IP-cím virtuális gépről való szétválasztani
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900745"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Nyilvános IP-cím leválaszthatása Azure-beli virtuális gépről 

Ebben a cikkben megtudhatja, hogyan választhatja el a nyilvános IP-címet egy Azure virtuális gép (VM).

Használhatja az [Azure Portalon,](#azure-portal)az Azure [parancssori felület](#azure-cli) (CLI) vagy a [PowerShell](#powershell) egy nyilvános IP-címet egy virtuális gép.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Tallózással keresse meg, vagy keresse meg azt a virtuális gépet, amelyhez le szeretné választani a nyilvános IP-címet, majd jelölje ki.
3. A Virtuálisgép lapon válassza az **Áttekintés**lehetőséget, és válassza ki a nyilvános IP-címet az alábbi képen látható módon:

   ![Nyilvános IP kiválasztása](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. A nyilvános IP-cím lapon válassza az **Áttekintés**lehetőséget, majd a **Disszociate**lehetőséget az alábbi képen látható módon:

    ![Nyilvános IP-cím széthatárolt](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. A **Nyilvános IP-cím elválasztása**csoportban válassza az **Igen**lehetőséget.

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy használja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A cli parancsokban válassza a **Try it (Kipróbálás)** gombot. Válassza a **Próbálja meg** egy felhőshell, amely bejelentkezhet az Azure-fiókba.

1. Ha a CLI-t helyileg használja a `az login`Bash alkalmazásban, jelentkezzen be az Azure-ba a segítségével.
2. A nyilvános IP-cím egy virtuális géphez csatlakoztatott hálózati adapter IP-konfigurációjához van társítva. Az [az hálózati nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) paranccsal elválaszthat egy nyilvános IP-címet egy IP-konfigurációtól. A következő példa egy *myVMPublicIP* nevű nyilvános IP-címet távolít el egy *myVMVMNic* nevű meglévő hálózati adapter *ipconfigmyVM* nevű IP-konfigurációjától, amely egy *myResourceGroup* nevű virtuális géphez van csatolva. *myResourceGroup*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Ha nem tudja a virtuális géphez csatolt hálózati adapter nevét, az [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) parancs segítségével tekintse meg őket. A következő parancs például felsorolja a *myResourceGroup*nevű erőforráscsoportban egy *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában a *myVMVMNic* a hálózati adapter neve.

   - Ha nem tudja a hálózati adapter IP-konfigurációjának nevét, az [az hálózati nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) paranccsal olvassa be azokat. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter nyilvános IP-konfigurációinak nevét egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Ha nem tudja a hálózati adapter nyilvános IP-konfigurációjának nevét, az [az hálózati nic ip-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) paranccsal lekérheti őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter nyilvános IP-konfigurációinak nevét egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt,](/powershell/azure/install-az-ps)vagy használja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. A PowerShell előre telepítve van, és a fiókjával való használatra van konfigurálva. Válassza a **Try it** gombot a következő PowerShell-parancsokban. Válassza a **Próbálja meg** egy felhőshell, amely bejelentkezhet az Azure-fiókba.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a segítségével. `Connect-AzAccount`
2. A nyilvános IP-cím egy virtuális géphez csatlakoztatott hálózati adapter IP-konfigurációjához van társítva. Hálózati adapter beszerezéséhez használja a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) parancsot. Állítsa a Nyilvános IP-cím értékét null értékre, majd a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) paranccsal írja az új IP-konfigurációt a hálózati adapterre.

   A következő példa egy *myVMPublicIP* nevű nyilvános IP-címet távolít el egy *myVMVMNic* nevű hálózati adaptertől, amely egy *myVM*nevű virtuális géphez van csatolva. Minden erőforrás egy *myResourceGroup*nevű erőforráscsoportba tartozik.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Ha nem tudja a virtuális géphez csatlakoztatott hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal tekintse meg őket. A következő parancs például felsorolja a *myResourceGroup*nevű erőforráscsoportban egy *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *myVMVMNic* a neve a hálózati adapter.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem tudja a hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal olvassa be azokat. A következő parancs például felsorolja egy *myVMVMNic* nevű hálózati illesztő IP-konfigurációjának nevét egy *myResourceGroup*nevű erőforráscsoportban:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A kimenet egy vagy több, a következő példához hasonló sort tartalmaz. A példakimeneti, *ipconfigmyVM* egy IP-konfiguráció neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [társíthat nyilvános IP-címet egy virtuális géphez.](associate-public-ip-address-vm.md)
