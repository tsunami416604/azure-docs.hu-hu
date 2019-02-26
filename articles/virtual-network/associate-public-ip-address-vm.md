---
title: Virtuális gép nyilvános IP-cím társítása
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan társítsa a virtuális gép nyilvános IP-címet.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: be3e9b8003bc6eb585177b285255658c44c7fc36
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808656"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Virtuális gép nyilvános IP-cím társítása

Ebből a cikkből elsajátíthatja egy meglévő virtuális gép (VM) egy nyilvános IP-címet társíthatja. Kapcsolódás virtuális Géphez az internetről szeretne, ha a virtuális gép nyilvános IP-cím társítva van hozzá kell rendelkeznie. Ha szeretne létrehozni egy új virtuális gép nyilvános IP-címmel, így segítségével teheti a [az Azure portal](virtual-network-deploy-static-pip-arm-portal.md), a [az Azure parancssori felület (CLI)](virtual-network-deploy-static-pip-arm-cli.md), vagy [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Nyilvános IP-címek egy névleges díj rendelkezik. További információkért lásd: [díjszabás](https://azure.microsoft.com/pricing/details/ip-addresses/). Előfizetésenként használható nyilvános IP-címek száma korlátozva van. További információkért lásd: [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits-1).

Használhatja a [az Azure portal](#azure-portal), az Azure [parancssori felület](#azure-cli) (CLI), vagy [PowerShell](#powershell) társítani a virtuális gép nyilvános IP-címet.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg, vagy keresse meg a virtuális gép nyilvános IP-cím hozzáadása és jelölje ki a kívánt.
3. Alatt **beállítások**, jelölje be **hálózatkezelés**, majd válassza ki a hálózati adapter hozzáadni kívánt nyilvános IP-cím, az alábbi ábrán látható módon:

   ![Válasszon hálózati adaptert](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Nyilvános IP-címek egy virtuális Géphez csatolt hálózati adapterek társítva. Az előző ábrán a virtuális gép csak egy hálózati adapter rendelkezik. Ha a virtuális gép több hálózati adapterrel, mindegyik megjelenik, és válassza a hálózati adaptert szeretne társítani a nyilvános IP-címet.

4. Válassza ki **IP-konfigurációk** , majd egy IP-konfigurációt, az alábbi ábrán látható módon:

   ![Válassza ki az IP-konfiguráció](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Nyilvános IP-címek társítva a hálózati illesztő IP-konfigurációk. Az előző képen a hálózati adapter egy IP-konfigurációval rendelkezik. Ha a hálózati adapter több IP-konfiguráció, mindegyik megjelenik a listában, és a kiválasztott IP-konfigurációja, amely a nyilvános IP-címet társítani szeretné.

5. Válassza ki **engedélyezve**, majd **IP-cím (*kötelező beállítások konfigurálása*)**. Válasszon egy meglévő nyilvános IP-címet, amely automatikusan lezárja a **nyilvános IP-cím választása** mezőbe. Ha nem rendelkezik minden elérhető nyilvános IP-címek felsorolt, létre kell hoznia egyet. További információ [hozzon létre egy nyilvános IP-cím](virtual-network-public-ip-address.md#create-a-public-ip-address). Válassza ki **mentése**, ahogyan az a képen látható, amely követi, és zárja be az IP-konfigurációja.

   ![Nyilvános IP-cím engedélyezése](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Nyilvános IP-címek jelennek meg azok a virtuális géppel azonos régióban található. Ha több nyilvános IP-címek a régióban létrehozott, az összes itt jelennek meg. Ha bármelyik szürkén jelennek meg, mert a címet már egy másik erőforrás társítva.

6. Az IP-konfigurációhoz rendelt nyilvános IP-cím megtekintéséhez, a következő képen látható módon. Jelennek meg az IP-cím néhány másodpercet vehet igénybe.

   ![Hozzárendelt nyilvános IP-cím megtekintése](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > A cím van hozzárendelve az egyes Azure-régiókban használt címek készletéből. Az egyes régiókban használt címkészletek listájának megtekintéséhez lásd: [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím lehet bármilyen címet a készleteket, a régiót használni. Ha a címet a régióban egy adott készletből van szüksége, használja a [nyilvános IP-címelőtag](public-ip-address-prefix.md).

7. [A virtuális gép hálózati forgalom](#allow-network-traffic-to-the-vm) a hálózati biztonsági csoport biztonsági szabályai.

## <a name="azure-cli"></a>Azure CLI

Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy az Azure Cloud Shellt használja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki a **kipróbálás** gombot a parancssori felület parancsai olvashat. Kiválasztásával **kipróbálás** hív meg, amely az Azure-fiókjával bejelentkezhet a Cloud Shellben.

1. Ha a parancssori felület helyileg használja a Bash, jelentkezzen be az Azure-bA `az login`.
2. Nyilvános IP-címmel társítva egy virtuális Géphez csatolt hálózati adapter IP-konfigurációt. Használja a [az network nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) parancs egy IP-konfigurációt egy nyilvános IP-címet hozzárendelni. Az alábbi példa egy meglévő nyilvános IP-címet hozzárendeli *myVMPublicIP* nevű IP-konfigurációhoz *ipconfigmyVM* egy meglévő hálózati illesztő nevű *myVMVMNic* nevű erőforráscsoportot, amely létezik *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

  - Ha nem rendelkezik egy meglévő nyilvános IP-címet, a [az network public-ip létrehozása](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) parancs használatával hozzon létre egyet. Például az a következő parancs létrehoz egy nyilvános IP-címet *myVMPublicIP* nevű erőforráscsoportból *myResourceGroup*.
  
    ```azurecli-interactive
    az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
    ```

    > [!NOTE]
    > Az előző parancs alapértelmezett értékeinek számos beállítás, amelyeket érdemes testre egy nyilvános IP-címet hoz létre. Az összes nyilvános IP-címbeállítások kapcsolatos további információkért lásd: [hozzon létre egy nyilvános IP-cím](virtual-network-public-ip-address.md#create-a-public-ip-address). A cím van hozzárendelve az egyes Azure-régiókban használt nyilvános IP-címek készletéből. Az egyes régiókban használt címkészletek listájának megtekintéséhez lásd: [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

  - Ha nem ismeri a virtuális Géphez csatolt hálózati adapter nevére, használja a [az vm nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) paranccsal tekintheti meg őket. Például a következő parancsot a nevű virtuális Géphez csatolt hálózati adapterek neveinek listája *myVM* nevű erőforráscsoportból *myResourceGroup*:

    ```azurecli-interactive
    az vm nic list --vm-name myVM --resource-group myResourceGroup
    ```

     A parancs kimenete egy vagy több sort a következő példához hasonló:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában *myVMVMNic* a hálózati adapter neve.

  - Ha nem tudja, hogy egy hálózati adapter IP-konfiguráció nevét, a [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) paranccsal kérheti le azokat. Ha például a következő parancs felsorolja a nevű hálózati adapter IP-konfigurációk *myVMVMNic* nevű erőforráscsoportból *myResourceGroup*:

    ```azurecli-interactive
    az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
    ```

3. Az IP-konfigurációhoz rendelt nyilvános IP-cím megjelenítése a [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) parancsot. Az alábbi példa bemutatja, hogy a meglévő virtuális géphez rendelt IP-címek nevű *myVM* nevű erőforráscsoportból *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > A cím van hozzárendelve az egyes Azure-régiókban használt címek készletéből. Az egyes régiókban használt címkészletek listájának megtekintéséhez lásd: [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím lehet bármilyen címet a készleteket, a régiót használni. Ha a címet a régióban egy adott készletből van szüksége, használja a [nyilvános IP-címelőtag](public-ip-address-prefix.md).

4. [A virtuális gép hálózati forgalom](#allow-network-traffic-to-the-vm) a hálózati biztonsági csoport biztonsági szabályai.

## <a name="powershell"></a>PowerShell

Telepítés [PowerShell](/powershell/azure/install-az-ps), vagy az Azure Cloud Shellt használja. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. PowerShell előre telepítve és konfigurálva a fiókjával való használat rendelkezik. Válassza ki a **kipróbálás** gombot a PowerShell-parancsokat olvashat. Kiválasztásával **kipróbálás** hív meg, amely az Azure-fiókjával bejelentkezhet a Cloud Shellben.

1. Ha helyileg használja a PowerShell, jelentkezzen be az Azure-bA `Connect-AzAccount`.
2. Nyilvános IP-címmel társítva egy virtuális Géphez csatolt hálózati adapter IP-konfigurációt. Használja a [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) és [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) parancsok beolvasni a virtuális hálózatot és alhálózatot, amelyet a hálózati adaptert. Ezután a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) parancs használatával beszerezheti a hálózati adapter és a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) parancsot egy meglévő nyilvános IP-cím lekéréséhez. Ezután a [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) társítsa a nyilvános IP-címet az IP-konfiguráció parancsot, és a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) parancsot az új IP-konfiguráció írása a a hálózati adaptert.

   Az alábbi példa egy meglévő nyilvános IP-címet hozzárendeli *myVMPublicIP* nevű IP-konfigurációhoz *ipconfigmyVM* egy meglévő hálózati illesztő nevű *myVMVMNic* nevű alhálózattal, amely létezik *myVMSubnet* nevű virtuális hálózat *myVMVNet*. Minden erőforrás egy nevű erőforráscsoport van *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

  - Ha nem rendelkezik egy meglévő nyilvános IP-címet, használja a [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) parancs használatával hozzon létre egyet. Ha például a következő parancs létrehoz egy *dinamikus* nyilvános IP-címet *myVMPublicIP* nevű erőforráscsoportból *myResourceGroup* a a  *USA keleti régiója* régióban.
  
    ```azurepowershell-interactive
    New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
    ```

    > [!NOTE]
    > Az előző parancs alapértelmezett értékeinek számos beállítás, amelyeket érdemes testre egy nyilvános IP-címet hoz létre. Az összes nyilvános IP-címbeállítások kapcsolatos további információkért lásd: [hozzon létre egy nyilvános IP-cím](virtual-network-public-ip-address.md#create-a-public-ip-address). A cím van hozzárendelve az egyes Azure-régiókban használt nyilvános IP-címek készletéből. Az egyes régiókban használt címkészletek listájának megtekintéséhez lásd: [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

  - Ha nem ismeri a virtuális Géphez csatolt hálózati adapter nevére, használja a [Get-azvm parancsmag](/powershell/module/Az.Compute/Get-AzVM) paranccsal tekintheti meg őket. Például a következő parancsot a nevű virtuális Géphez csatolt hálózati adapterek neveinek listája *myVM* nevű erőforráscsoportból *myResourceGroup*:

    ```azurepowershell-interactive
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

    A kimenet hasonlóak a következő példában egy vagy több sort tartalmaz. A példa kimenetben *myVMVMNic* a hálózati adapter neve.
  
    ```
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
    ```

  - Ha nem ismeri a virtuális hálózathoz vagy alhálózathoz, amelyhez az adapter neve, használja a `Get-AzNetworkInterface` parancsot az információk megtekintéséhez. Például a következő parancsot a virtuális hálózatot és alhálózatot nevű hálózati adapter információkat kér le *myVMVMNic* nevű erőforráscsoportból *myResourceGroup*:

    ```azurepowershell-interactive
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
    $ipConfigs = $nic.IpConfigurations
    $ipConfigs.Subnet | Select Id
    ```

     A kimenet hasonlóak a következő példában egy vagy több sort tartalmaz. A példa kimenetben *myVMVNET* a virtuális hálózat neve és *myVMSubnet* az alhálózat neve.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

  - Ha nem tudja, hogy egy hálózati adapter IP-konfiguráció nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal kérheti le azokat. Ha például a következő parancs felsorolja a nevű hálózati adapter IP-konfigurációk *myVMVMNic* nevű erőforráscsoportból *myResourceGroup*:

    ```azurepowershell-interactive
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
    $nic.IPConfigurations
    ```

    A kimenet hasonlóak a következő példában egy vagy több sort tartalmaz. A példa kimenetben *ipconfigmyVM* egy IP-konfiguráció neve.
  
    ```
    Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
    ```

3. Az IP-konfigurációhoz rendelt nyilvános IP-cím megjelenítése a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) parancsot. Az alábbi példa bemutatja a cím rendelve egy nyilvános IP-címet *myVMPublicIP* nevű erőforráscsoportból *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Ha nem ismeri egy IP-konfigurációhoz rendelt nyilvános IP-cím nevére, futtassa a következő parancsok használatával szerezze be:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A kimenet hasonlóak a következő példában egy vagy több sort tartalmaz. A példa kimenetben *myVMPublicIP* az IP-konfigurációhoz rendelt nyilvános IP-cím neve.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > A cím van hozzárendelve az egyes Azure-régiókban használt címek készletéből. Az egyes régiókban használt címkészletek listájának megtekintéséhez lásd: [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím lehet bármilyen címet a készleteket, a régiót használni. Ha a címet a régióban egy adott készletből van szüksége, használja a [nyilvános IP-címelőtag](public-ip-address-prefix.md).

4. [A virtuális gép hálózati forgalom](#allow-network-traffic-to-the-vm) a hálózati biztonsági csoport biztonsági szabályai.

## <a name="allow-network-traffic-to-the-vm"></a>A virtuális Gépre irányuló hálózati forgalom engedélyezése

Mielőtt a nyilvános IP-címével csatlakozhat az internetről, győződjön meg arról, hogy rendelkezik-e a szükséges portokat nyissa meg a hálózati biztonsági csoport, előfordulhat, hogy társított hálózati adapter vagy az alhálózatot a hálózati adaptert. Bár a biztonsági csoportok felé irányuló forgalom szűrésére a hálózati adaptert, ha a bejövő internetes forgalom érkezik a nyilvános IP-cím, az Azure privát IP-címét a rendszer lefordítja a nyilvános magánhálózati IP-cím cím, így ha egy hálózati biztonsági csoport megakadályozza, hogy a forgalom áramlását – a nyilvános IP-címmel a kommunikáció sikertelen lesz. Az érvényben lévő biztonsági szabályokat egy hálózati adaptert, és az alhálózat használatával is megtekintheti a [portál](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), vagy [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>További lépések

Lehetővé teszi a bejövő internetes forgalmat a virtuális gép egy hálózati biztonsági csoporttal. A hálózati biztonsági csoport létrehozásával kapcsolatban lásd: [hálózati biztonsági csoportok használata](manage-network-security-group.md#work-with-network-security-groups). Hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért lásd: [biztonsági csoportok](security-overview.md).