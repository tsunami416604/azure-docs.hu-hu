---
title: Hozzon létre egy Azure virtuális hálózatok közötti társviszony - különböző üzemi modellek – ugyanabban az előfizetésben |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, amelyek szerepelnek az Azure-előfizetéshez az Azure különböző üzemi modellel létrehozott virtuális hálózatok közötti társviszony virtuális hálózatot.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: jdial;anavin
ms.openlocfilehash: 1c3a98e6c1aebb497514c074eb66f8cf30e91228
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819569"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Hozzon létre egy virtuális hálózati társviszony - másik üzembe helyezési modellel, ugyanahhoz az előfizetéshez

Ebben az oktatóanyagban elsajátíthatja egy virtuális hálózat, különböző üzembehelyezési modellel létrehozott virtuális hálózatok közötti társviszony létrehozásához. Mindkét virtuális hálózat ugyanabban az előfizetésben található. Társviszony-létesítés virtuális hálózatok különböző virtuális hálózatokban kommunikálni egymással azonos sávszélesség és késés, mintha az erőforrásokat is ugyanabban a virtuális hálózatban lévő két lehetővé teszi, hogy erőforrásokat. Tudjon meg többet [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md).

A virtuális hálózatok közötti társviszony létrehozásához szükséges lépéseket, attól függően különböznek egymástól vannak-e a virtuális hálózatok a azonos vagy eltérő előfizetések és amely [Azure üzembehelyezési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) jönnek létre a virtuális hálózatokon keresztül. Ismerje meg, hogyan hozhat létre virtuális hálózati társviszonyt más forgatókönyvek esetében a következő táblázat a forgatókönyv kattintva:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Azonos|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Eltérő|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Eltérő|

A virtuális hálózatok közötti társviszony nem hozható létre, a klasszikus üzemi modellel üzembe helyezett két virtuális hálózat között. Ha a klasszikus üzemi modellel létrehozott, mindkét virtuális hálózatok csatlakoztatása van szüksége, használhatja az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) össze a virtuális hálózatok.

Ebben az oktatóanyagban ugyanabban a régióban található virtuális hálózatok is társul. Emellett társviszonyt virtuális hálózatok különböző [támogatott régiók](virtual-network-manage-peering.md#cross-region). Javasoljuk, hogy Ön feltérképezése a [társviszony-létesítés követelményei és korlátozásai](virtual-network-manage-peering.md#requirements-and-constraints) előtt társviszony-létesítés virtuális hálózatok.

Használhatja az Azure Portalon, az Azure [parancssori felület](#cli) (CLI), Azure [PowerShell](#powershell), vagy egy Azure Resource Manager-sablont egy virtuális hálózati társviszony-létesítés. Kattintson az előző eszköz hivatkozásokra, hogy közvetlenül a virtuális hálózati társviszonyt a választott eszköz használatával létrehozásának menete.

## <a name="create-peering---azure-portal"></a>Társviszony - létrehozása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bejelentkezéshez fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#requirements-and-constraints).
2. Kattintson a **+ új**, kattintson a **hálózatkezelés**, majd kattintson a **virtuális hálózati**.
3. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson a **létrehozás**:
    - **Név**: *myVnet1*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: Válassza ki előfizetését.
    - **Erőforráscsoport**: Válassza ki **új létrehozása** , és adja meg *myResourceGroup*
    - **Hely**: *USA keleti RÉGIÓJA*
4. Kattintson az **+ Új** elemre. Az a **keresés a piactéren** mezőbe írja be *virtuális hálózati*. Kattintson a **virtuális hálózati** amikor megjelenik a keresési eredmények között.
5. Az a **virtuális hálózati** panelen válassza ki **klasszikus** a a **telepítési modell kiválasztása** mezőbe, majd kattintson a **létrehozás**.
6. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson a **létrehozás**:
    - **Név**: *myVnet2*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: Válassza ki előfizetését.
    - **Erőforráscsoport**: Válassza ki **meglévő** válassza *myResourceGroup*
    - **Hely**: *USA keleti RÉGIÓJA*
7. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myResourceGroup*. Kattintson a **myResourceGroup** amikor megjelenik a keresési eredmények között. Megjelenik egy panel az **myresourcegroup** erőforráscsoportot. Az erőforráscsoport tartalmazza az előző lépésekben létrehozott két virtuális hálózat.
8. Kattintson a **myVNet1**.
9. Az a **myVnet1** panel, amelyen megjelenik, kattintson a **Társviszonyok** a panel bal oldali beállítások függőleges listájából.
10. Az a **myVnet1 - Társviszonyok** panel, amelyen jelent meg, kattintson a **+ Hozzáadás**
11. Az a **Hozzáadás társviszony-létesítés** megjelenő panelen adja meg, vagy válassza ki a következő beállításokat, majd kattintson **OK**:
     - **Név**: *myVnet1ToMyVnet2*
     - **Virtuális hálózat üzemelőpéldány-modellje**:  Válassza ki **klasszikus**.
     - **Előfizetés**: Válassza ki előfizetését.
     - **Virtuális hálózat**:  Kattintson a **virtuális hálózat választása**, majd kattintson a **myVnet2**.
     - **Virtuális hálózati hozzáférés engedélyezése:** Ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállításokat használja. Minden társviszony beállításai kapcsolatos további információkért olvassa el a [kezelheti a virtuális társhálózatok](virtual-network-manage-peering.md#create-a-peering).
12. Kattintás után **OK** az előző lépésben a **Hozzáadás társviszony-létesítés** panel bezárul, és megjelenik a **myVnet1 - Társviszonyok** újra a panelt. Néhány másodperc elteltével a társviszony-létesítés létrehozott megjelenik a panelen. **Csatlakoztatott** szerepel-e a **társviszony-LÉTESÍTÉS állapota** oszlopában a **myVnet1ToMyVnet2** társviszony-létesítés, létrehozva.

    A most már létrejött a társviszony. Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Alapértelmezett Azure névfeloldási használja a virtual Network hálózatokhoz használható, ha a virtuális hálózatokban az erőforrások nem tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Nem kötelező**: Bár a virtuális gépek létrehozása nem a jelen oktatóanyagban tárgyalt, virtuális gép létrehozása az egyes virtuális hálózatok, és kapcsolódni egy virtuális gépről a másik, a kapcsolat ellenőrzéséhez.
14. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések a [törölje az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure CLI

A következő lépéseket a klasszikus Azure CLI és az Azure CLI használatával. Csak kiválasztásával kitöltheti a lépéseket követve az Azure Cloud Shell a **próbálja ki** gombot a következő lépésekből áll, vagy telepítse a [klasszikus parancssori felület](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) és [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) és a parancsok futtatása a helyi számítógépen.

1. A Cloud Shell használata esetén ugorjon a 2, mert a Cloud Shell automatikusan bejelentkezik, az Azure-bA. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be Azure-bA a `azure login` parancsot.
2. Futtassa a CLI szolgáltatásfelügyelet módban megadásával a `azure config mode asm` parancsot.
3. Adja meg a következő parancsot a virtuális hálózat (klasszikus) létrehozása:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Hajtsa végre az alábbi bash CLI-példaszkript a parancssori felület, nem a klasszikus parancssori felület használatával. A beállítások a futó bash-CLI-szkriptek Windows-számítógépen, tekintse meg a [az Azure CLI telepítése Windows](/cli/azure/install-azure-cli-windows).

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Hozzon létre egy virtuális hálózati társviszonyt a két, a parancssori felület használatával a különböző üzembehelyezési modellel létrehozott virtuális hálózat között. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen. Cserélje le `<subscription id>` az előfizetés-azonosítóval. Ha az előfizetés-azonosító nem ismeri, adja meg a `az account show` parancsot. Az érték **azonosító** kimenet van az előfizetés-azonosítójára. Illessze be a módosított szkriptet a CLI-munkamenethez, és nyomja le az `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. A szkript végrehajtása után tekintse át a társviszony-létesítés virtuális hálózatok (Resource Manager). Másolja a következő parancsot, és illessze be a CLI-munkamenetben, nyomja le az `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop.

   Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Alapértelmezett Azure névfeloldási használja a virtual Network hálózatokhoz használható, ha a virtuális hálózatokban az erőforrások nem tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Nem kötelező**: Bár a virtuális gépek létrehozása nem a jelen oktatóanyagban tárgyalt, virtuális gép létrehozása az egyes virtuális hálózatok, és kapcsolódni egy virtuális gépről a másik, a kapcsolat ellenőrzéséhez.
8. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-cli) ebben a cikkben.

## <a name="powershell"></a>-Társviszony-létesítés PowerShell

1. Telepítse a PowerShell legújabb verzióját [Azure](https://www.powershellgallery.com/packages/Azure) és [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulok. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-bA írja be a `Add-AzureAccount` parancsot. A bejelentkezéshez fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#requirements-and-constraints).
4. Virtuális hálózat (klasszikus) létrehozása a PowerShell használatával, hozzon létre egy új, vagy módosítsa egy meglévő, hálózati konfigurációs fájlt. Ismerje meg, hogyan [exportálása, frissítése és importálása a hálózati konfigurációs fájlokban](virtual-networks-using-network-configuration-file.md). A fájl tartalmaznia kell a következő **VirtualNetworkSite** elem a virtuális hálózat, a jelen oktatóanyagban használt:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Megváltozott hálózati konfigurációs fájl importálása okozhat a módosításokat a meglévő virtuális hálózatok (klasszikus) az előfizetésében. Győződjön meg arról, csak adja hozzá a korábbi virtuális hálózat és az, hogy ne módosítsa vagy bármely meglévő virtuális hálózatok eltávolítása az előfizetésből.
5. Jelentkezzen be az Azure-ban hozzuk létre a virtuális hálózat (Resource Manager) megadásával a `Connect-AzureRmAccount` parancsot. A bejelentkezéshez fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#requirements-and-constraints).
6. Hozzon létre egy erőforráscsoportot és a egy virtuális hálózatot (Resource Manager). Másolja a szkriptet, illessze be a PowerShell, és nyomja le az `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. A két, a különböző üzembehelyezési modellel létrehozott virtuális hálózatok közötti társviszony-létesítés virtuális hálózat létrehozása. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen. Cserélje le `<subscription id>` az előfizetés-azonosítóval. Ha az előfizetés-azonosító nem ismeri, adja meg a `Get-AzureRmSubscription` paranccsal megtekintheti azokat. Az érték **azonosító** visszaadott kimenet van az előfizetés-azonosítójára. A szkript végrehajtásához a módosított szkriptet a szöveges szerkesztő, majd kattintson a jobb gombbal a PowerShell-munkamenetben másolja, és nyomja le az `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. A szkript végrehajtása után tekintse át a társviszony-létesítés virtuális hálózatok (Resource Manager). Másolja a következő parancsot, illessze be a PowerShell-munkamenetet, és nyomja le az `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop.

    Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Alapértelmezett Azure névfeloldási használja a virtual Network hálózatokhoz használható, ha a virtuális hálózatokban az erőforrások nem tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Nem kötelező**: Bár a virtuális gépek létrehozása nem a jelen oktatóanyagban tárgyalt, virtuális gép létrehozása az egyes virtuális hálózatok, és kapcsolódni egy virtuális gépről a másik, a kapcsolat ellenőrzéséhez.
10. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-powershell) ebben a cikkben.

## <a name="delete"></a>Erőforrások törlése

Ez az oktatóanyag befejezése után érdemes törölni, így nem használati díjak merülhetnek fel az oktatóanyagban létrehozott erőforrásokat. Egy erőforráscsoport törlésekor a, amelyek az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="delete-portal"></a>Azure Portal

1. A portál keresőmezőbe írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **írja be az ERŐFORRÁSCSOPORT nevét** mezőbe írja be **myResourceGroup**, és kattintson a **törlése**.

### <a name="delete-cli"></a>Azure CLI

1. Az Azure CLI használatával törölni a virtuális hálózatot (Resource Manager) a következő paranccsal:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. A klasszikus parancssori felület használatával törölheti a virtuális hálózat (klasszikus) a következő parancsokkal:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Adja meg törölni a virtuális hálózatot (Resource Manager) a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. A virtuális hálózat törlése a PowerShell (klasszikus modell) kell módosítani egy meglévő hálózati konfigurációs fájlt. Ismerje meg, hogyan [exportálása, frissítése és importálása a hálózati konfigurációs fájlokban](virtual-networks-using-network-configuration-file.md). Távolítsa el a virtuális hálózat, a jelen oktatóanyagban használt VirtualNetworkSite a következő elemet:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Megváltozott hálózati konfigurációs fájl importálása okozhat a módosításokat a meglévő virtuális hálózatok (klasszikus) az előfizetésében. Győződjön meg arról, csak távolítsa el a korábbi virtuális hálózatot, és nem módosítja a, illetve bármely más meglévő virtuális hálózatok eltávolítása az előfizetésből.

## <a name="next-steps"></a>További lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátok és működési módokkal](virtual-network-manage-peering.md#requirements-and-constraints) társviszonyt az éles virtuális hálózat létrehozása előtt használja.
- További információ az összes [virtuális hálózati társviszony beállításról](virtual-network-manage-peering.md#create-a-peering).
- Ismerje meg, hogyan [eseményközpont létrehozásához és a hálózati topológia küllő](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózatok közötti társviszony.
