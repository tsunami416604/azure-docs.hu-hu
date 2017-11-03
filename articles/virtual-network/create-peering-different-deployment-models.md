---
title: "Hozzon létre egy Azure virtuális hálózati társviszony - különböző üzembe helyezési modellel - ugyanahhoz az előfizetéshez |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy virtuális hálózati társviszony-létesítés létre különböző Azure üzembe helyezési modellel, az azonos Azure-előfizetéshez létező virtuális hálózatok között."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 1adf219dc4ca9ba91dc1ffc1ae98b764c9ef61b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Hozzon létre egy virtuális hálózati társviszony - különböző üzembe helyezési modellel, ugyanahhoz az előfizetéshez 

Ebben az oktatóanyagban elsajátíthatja társviszony-létesítés különböző üzembe helyezési modellel létrehozott virtuális hálózatok közötti virtuális hálózat létrehozása. Mindkét virtuális hálózat szerepel a ugyanahhoz az előfizetéshez. A különböző virtuális hálózatokon kommunikálni egymással sávszélesség és a késés, mintha az erőforrások volt az azonos virtuális hálózatban lévő két virtuális hálózatok lehetővé teszi, hogy erőforrásokat társviszony-létesítés. További információ [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md). 

Virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok ugyanazon, vagy másik, előfizetések, és amely [Azure telepítési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok használatával jönnek létre. Megtudhatja, hogyan hozhat létre virtuális hálózatot gombra kattintva a következő táblázat a forgatókönyv más esetekben társviszony:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét erőforrás-kezelő](virtual-network-create-peering.md) |Azonos|
|[Mindkét erőforrás-kezelő](create-peering-different-subscriptions.md) |Különböző|
|[Egy erőforrás-kezelő egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Különböző|

Virtuális hálózati társviszony-létesítés csak létrehozhatók, amelyek azonos Azure-régióban található virtuális hálózatok között.

  > [!WARNING]
  > A virtuális hálózati társviszony-létesítés különböző régiókban virtuális hálózatok közötti létrehozásának jelenleg előzetes verzió. Az alábbi mintában előfizetés tud regisztrálni. Ebben a forgatókönyvben létrehozott virtuális hálózati társviszony nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, mint a társviszony-létesítés forgatókönyvekben általános rendelkezésre állási kiadás virtuális hálózat létrehozása. Ebben a forgatókönyvben létrehozott virtuális hálózati társviszony nem támogatottak, van, korlátozott képességeit, és előfordulhat, hogy nem érhető el az összes Azure-régiók. A szolgáltatás rendelkezésre állásával és állapotával kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

Virtuális hálózati társviszony-létesítés nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózatok között. Ha mindkét létrehozott a klasszikus üzembe helyezési modell használatával, és különböző Azure-régiók meglévő virtuális hálózatok csatlakoztatni kell, használhatja az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális hálózathoz csatlakozni. 

Használhatja a [Azure-portálon](#portal), az Azure [parancssori felület](#cli) (CLI), Azure [PowerShell](#powershell), vagy egy [Azure Resource Manager sablon](#template)létrehozni a virtuális hálózati társviszony-létesítés. Kattintson az előző eszköz hivatkozásokra kattintva közvetlenül Ugrás a virtuális hálózati társviszony-létesítés a eszközzel választott létrehozásához szükséges lépésekről.

## <a name="register"></a>A globális Vnetben társviszony-létesítés Preview regisztrálása

Virtuális hálózatok egyenrangú régiók között, regisztráljon az előzetes, hajtsa végre a lépéseket, hogy kövesse a mindkét előfizetések, amelyek tartalmazzák a virtuális hálózatokat partnert szeretné. Az egyetlen eszköz segítségével regisztrálja az előzetes PowerShell.

1. Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítson el egy PowerShell-munkamenetet, és jelentkezzen be az Azure használatával a `Login-AzureRmAccount` parancsot.
3. Az előfizetés regisztrálása a az előzetes kiadásban a következő parancsok beírásával:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    Ne hajtsa végre a portál, az Azure parancssori felület vagy a PowerShell szakaszok, amíg ez a cikk lépéseit a **RegistrationState** kap, miután a rendszer az alábbi parancs beírásával kimeneti **regisztrált** mindkét elő:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > A virtuális hálózati társviszony-létesítés különböző régiókban virtuális hálózatok közötti létrehozásának jelenleg előzetes verzió. Ebben a forgatókönyvben létrehozott virtuális hálózati társviszony van, korlátozott képességeit, és előfordulhat, hogy nem érhető el az összes Azure-régiók. A szolgáltatás rendelkezésre állásával és állapotával kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

## <a name="cli"></a>Hozzon létre a társviszony - portál

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
2. Kattintson a **+ új**, kattintson a **hálózati**, majd kattintson a **virtuális hálózati**.
3. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson **létrehozása**:
    - **Név**: *myVnet1*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.0.0.0/24*
    - **Előfizetés**: Jelölje ki az előfizetését
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroup*
    - **Hely**: *USA keleti régiója*
4. Kattintson a **+ új**. Az a **keresése a piactéren** mezőbe írja be *virtuális hálózati*. Kattintson a **virtuális hálózati** amikor megjelenik a keresési eredmények között. 
5. Az a **virtuális hálózati** panelen válassza **klasszikus** a a **telepítési modell kiválasztása** gombra, majd **létrehozása**.
6. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson **létrehozása**:
    - **Név**: *myVnet2*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.1.0.0/24*
    - **Előfizetés**: Jelölje ki az előfizetését
    - **Erőforráscsoport**: válasszon **meglévő** válassza *myResourceGroup*
    - **Hely**: *USA keleti régiója*
7. Az a **keresési erőforrások** típus a portál felső részén *myResourceGroup*. Kattintson a **myResourceGroup** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myresourcegroup** erőforráscsoportot. Az erőforráscsoport az előző lépésekben létrehozott két virtuális hálózatot tartalmaz.
8. Kattintson a **myVNet1**.
9. Az a **myVnet1** panel, amelyen megjelenik, kattintson a **Társviszony** a függőleges a panel bal oldalán lehetőségek közül.
10. Az a **myVnet1 - esetében** panel, amelyen jelent meg, kattintson a **+ Hozzáadás**
11. Az a **Hozzáadás társviszony-létesítés** panelen megjelenő, adja meg, vagy válassza a következő beállításokat, majd kattintson **OK**:
     - **Név**: *myVnet1ToMyVnet2*
     - **Virtuális hálózat telepítési modell**: válasszon **klasszikus**. 
     - **Előfizetés**: Jelölje ki az előfizetését
     - **Virtuális hálózati**: kattintson a **virtuális hálózatot választ**, majd kattintson a **myVnet2**.
     - **Virtuális hálózati hozzáférés engedélyezése:** ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállítások használhatók. Minden társviszony-létesítési beállításaival kapcsolatos további tudnivalókért olvassa el a [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md#create-a-peering).
12. Kattintás után **OK** az előző lépésben a **Hozzáadás társviszony-létesítés** panel bezárása után, és megjelenik a **myVnet1 - Társviszony** újra a panelt. Néhány másodpercen belül a társviszony-létesítés létrehozott jelenik meg a panelt. **Csatlakoztatott** szerepel a **társviszony-LÉTESÍTÉS állapot** oszlopában a **myVnet1ToMyVnet2** társviszony-létesítést, létre.

    A társviszony-létesítést most létrejön. Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
14. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure parancssori felület

1. [Telepítés](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) az Azure CLI 1.0 a virtuális hálózat (klasszikus) létrehozásához.
2. Nyisson meg egy parancsablakot, és jelentkezzen be az Azure használatával a `azure login` parancsot.
3. A parancssori felület szolgáltatásfelügyelet módban fusson írja be a `azure config mode asm` parancsot.
4. Adja meg a virtuális hálózat (klasszikus) létrehozása a következő parancsot:
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Resource Manager). Használhatja a parancssori felület 1.0 vagy 2.0-s ([telepítése](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). Ebben az oktatóanyagban a CLI 2.0 létrehozni a virtuális hálózat (erőforrás-kezelő), mert 2.0 kell használni a társviszony-létesítést létrehozásához használt. Hajtsa végre a következő bash parancssori felület a helyi gép a CLI 2.0.4 parancsfájl vagy újabb verziója. Fut a beállítások a Windows-ügyfelén CLI parancsfájlok bash, lásd: [futtatása az Azure parancssori felület a Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A parancsfájl az Azure-felhő rendszerhéj segítségével is futtathatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Kattintson a **kipróbálás** gombra a parancsfájl, amely a következő, a felhő rendszerhéjat, amelyre bejelentkezik, amellyel bejelentkezhet az Azure-fiókjába. A parancsfájl végrehajtására, kattintson a **másolási** gombra és a beillesztési műveleteket, a tartalmát a felhő rendszerhéjat, majd nyomja le az `Enter`.

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

6. Hozzon létre egy virtuális hálózati társviszony-létesítés keresztül a különböző üzembe helyezési modellel létrehozott két virtuális hálózatok között. Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen. Cserélje le `<subscription id>` az előfizetés azonosítóját. Ha az előfizetés-azonosítója nem ismeri, adja meg a `az account show` parancsot. A következő **azonosító** kimenet az előfizetés azonosítóját. Illessze be a módosított parancsfájlt a CLI-munkamenethez, és nyomja le az `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
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
7. Miután a parancsfájl végrehajtása során, tekintse át a társviszony-létesítést a virtuális hálózat (Resource Manager). Másolja a következő parancsot, illessze be a CLI-munkamenetben, és nyomja le az `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop. 

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
9. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-cli) ebben a cikkben.

## <a name="powershell"></a>Hozzon létre a társviszony - PowerShell

1. Telepítse a legújabb verzióját a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulok. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítson el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-ba a `Add-AzureAccount` paranccsal.
4. Virtuális hálózat (klasszikus) létrehozása a PowerShell segítségével, hozzon létre egy új, vagy módosítsa egy meglévő, hálózati konfigurációs fájlt. Megtudhatja, hogyan [exportálása, frissítése és a hálózati konfigurációs fájlok importálása a](virtual-networks-using-network-configuration-file.md). A fájl tartalmaznia kell a következő **VirtualNetworkSite** elem ebben az oktatóanyagban használt virtuális hálózat:

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
    > Megváltozott hálózati konfigurációs fájlok importálása (klasszikus) az előfizetéshez létező virtuális hálózatok módosításai okozhatják. Csak adja hozzá a korábbi virtuális hálózatot, és módosítsa vagy távolítsa el a meglévő virtuális hálózatok az előfizetésből nem biztosítására. 
5. Jelentkezzen be az Azure a virtuális hálózat (erőforrás-kezelő) létrehozásához írja be a `login-azurermaccount` parancsot. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
6. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Resource Manager). Másolja a parancsfájlt, illessze be a PowerShell és nyomja le az `Enter`.

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

7. Hozzon létre egy virtuális hálózati társviszony-létesítés keresztül a különböző üzembe helyezési modellel létrehozott két virtuális hálózatok között. Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen. Cserélje le `<subscription id>` az előfizetés azonosítóját. Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` parancs a megtekintéséhez. A következő **azonosító** visszaadott kimenet van az előfizetés-azonosító. A parancsfájl végrehajtása a módosított parancsfájl a szövegszerkesztőben, majd kattintson a jobb gombbal a PowerShell-munkamenetben másolja, és nyomja le az `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Miután a parancsfájl végrehajtása során, tekintse át a társviszony-létesítést a virtuális hálózat (Resource Manager). Másolja a következő parancsot, illessze be a PowerShell-munkamenetet, és nyomja le az `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop.

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
10. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-powershell) ebben a cikkben.
 
## <a name="permissions"></a>Engedélyek

A fiókokat hozhat létre a virtuális hálózati társviszony-létesítés a szükséges szerepkör vagy az engedélyekkel kell rendelkeznie. Például, ha két virtuális hálózatok myVnet1 és myVnet2 nevű volt társviszony, a fiókjához társítva kell lenni a következő minimális szerepkör vagy az engedélyekkel minden virtuális hálózathoz:
    
|Virtuális hálózat|Üzemi modell|Szerepkör|Engedélyek|
|---|---|---|---|
|myVnet1|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myVnet2|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

További információ [beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és rendelje hozzá adott engedélyeket [egyéni szerepkörök](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (csak Resource Manager).

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után, előfordulhat, hogy törölni kívánja az erőforrások létrehozta az oktatóanyagban, használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. A portál keresési mezőbe, írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroup**, és kattintson a **törlése**.

### <a name="delete-cli"></a>Az Azure parancssori felület

1. Az Azure CLI 2.0 segítségével törölje a virtuális hálózatot (erőforrás-kezelő) a következő paranccsal:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Az Azure CLI 1.0 segítségével törölheti a virtuális hálózat (klasszikus) az alábbi parancsokkal:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Adja meg a virtuális hálózat (erőforrás-kezelő) törlése a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. A virtuális hálózat törlése (klasszikus), a PowerShell-lel, módosítania kell egy meglévő hálózati konfigurációs fájlt. Megtudhatja, hogyan [exportálása, frissítése és a hálózati konfigurációs fájlok importálása a](virtual-networks-using-network-configuration-file.md). Távolítsa el a következő VirtualNetworkSite elem ebben az oktatóanyagban használt virtuális hálózat:

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
    > Megváltozott hálózati konfigurációs fájlok importálása (klasszikus) az előfizetéshez létező virtuális hálózatok módosításai okozhatják. Győződjön meg arról, csak távolítsa el a korábbi virtuális hálózatot, és nem módosítja, illetve bármely más meglévő virtuális hálózatot eltávolítása az előfizetésből. 

## <a name="next-steps"></a>Következő lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátozások és viselkedéshez](virtual-network-manage-peering.md#requirements-and-constraints) társviszony-létesítés üzemi virtuális hálózat létrehozása előtt használja.
- További tudnivalók az összes [virtuális hálózati társviszony-létesítési beállítások](virtual-network-manage-peering.md#create-a-peering).
- Megtudhatja, hogyan [létrehoz egy központot, és a hálózati topológia irány](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózati társviszony-létesítés.
