---
title: "Hozzon létre egy Azure virtuális hálózati társviszony - Resource Manager - ugyanahhoz az előfizetéshez |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy virtuális hálózati társviszony-létesítés erőforrás-kezelőt hozott létre, amely szerepel az azonos Azure-előfizetés virtuális hálózatok között."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: ebe418f03c2edf176790f654f3f9f4d7eec09165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Hozzon létre egy virtuális hálózati társviszony - erőforrás-kezelő ugyanahhoz az előfizetéshez

Ebben az oktatóanyagban elsajátíthatja társviszony-létesítés Resource Manager használatával létrehozott virtuális hálózatok közötti virtuális hálózat létrehozása. Mindkét virtuális hálózat szerepel a ugyanahhoz az előfizetéshez. A különböző virtuális hálózatokon kommunikálni egymással sávszélesség és a késés, mintha az erőforrások volt az azonos virtuális hálózatban lévő két virtuális hálózatok lehetővé teszi, hogy erőforrásokat társviszony-létesítés. További információ [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md). 

Virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok ugyanazon, vagy másik, előfizetések, és amely [Azure telepítési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok használatával jönnek létre. Megtudhatja, hogyan hozhat létre virtuális hálózatot gombra kattintva a következő táblázat a forgatókönyv más esetekben társviszony:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét erőforrás-kezelő](create-peering-different-subscriptions.md) |Különböző|
|[Egy erőforrás-kezelő egy klasszikus](create-peering-different-deployment-models.md) |Azonos|
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
  
## <a name="portal"></a>Hozzon létre a társviszony - Azure-portálon

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
4. Végezze el újra a következő értékek megadása 3. lépés 2-3 lépéseket:
    - **Név**: *myVnet2*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.1.0.0/24*
    - **Előfizetés**: Jelölje ki az előfizetését
    - **Erőforráscsoport**: válasszon **meglévő** válassza *myResourceGroup*
    - **Hely**: *USA keleti régiója*
5. Az a **keresési erőforrások** típus a portál felső részén *myResourceGroup*. Kattintson a **myResourceGroup** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myresourcegroup** erőforráscsoportot. Az erőforráscsoport az előző lépésekben létrehozott két virtuális hálózatot tartalmaz.
6. Kattintson a **myVNet1**.
7. Az a **myVnet1** panel, amelyen megjelenik, kattintson a **Társviszony** a függőleges a panel bal oldalán lehetőségek közül.
8. Az a **myVnet1 - esetében** panel, amelyen jelent meg, kattintson a **+ Hozzáadás**
9. Az a **Hozzáadás társviszony-létesítés** panelen megjelenő, adja meg, vagy válassza a következő beállításokat, majd kattintson **OK**:
     - **Név**: *myVnet1ToMyVnet2*
     - **Virtuális hálózat telepítési modell**: válasszon **erőforrás-kezelő**. 
     - **Előfizetés**: Jelölje ki az előfizetését
     - **Virtuális hálózati**: kattintson a **virtuális hálózatot választ**, majd kattintson a **myVnet2**.
     - **Virtuális hálózati hozzáférés engedélyezése:** ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállítások használhatók. Minden társviszony-létesítési beállításaival kapcsolatos további tudnivalókért olvassa el a [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md#create-a-peering).
10. Kattintás után **OK** az előző lépésben a **Hozzáadás társviszony-létesítés** panel bezárása után, és megjelenik a **myVnet1 - Társviszony** újra a panelt. Néhány másodpercen belül a társviszony-létesítés létrehozott jelenik meg a panelt. **Kezdeményezett** szerepel a **társviszony-LÉTESÍTÉS állapot** oszlopában a **myVnet1ToMyVnet2** társviszony-létesítés meg létrehozni. Már nincsenek társviszonyban Vnet1 Vnet2 számára, de most a myVnet1 myVnet2 kell partnert. A társviszony-létesítést ahhoz, hogy a virtuális hálózatok kommunikálnak egymással erőforrások mindkét irányban kell létrehozni.
11. Végezze el újra az myVnet2 5-10 lépéseket.  A társviszony-létesítést name *myVnet2ToMyVnet1*.
12. Néhány másodpercen belül kattintás után **OK** MyVnet2, a társviszony létrehozásához a **myVnet2ToMyVnet1** társviszony-létesítést az imént létrehozott szerepel **csatlakoztatva** a a  **Társviszony-LÉTESÍTÉS állapot** oszlop.
13. Újra 5-7 lépéseinek elvégzését MyVnet1. A **társviszony-LÉTESÍTÉS állapot** a a **myVnet1ToVNet2** társviszony-létesítés már is **csatlakoztatva**. A társviszony-létesítést sikeresen létrejött, miután látta, **csatlakoztatva** a a **társviszony-LÉTESÍTÉS állapot** oszlop társviszony-létesítés mindkét virtuális hálózat számára.
14. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
15. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete-portal) című szakaszát.

Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Társviszony - létrehozása az Azure parancssori felület

A következő parancsfájlt:

- Van szükség az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió található, futtassa a `az --version` parancsot. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- A rendszerhéjakba működik. Az Azure parancssori felület parancsfájlok futtatásához a Windows-ügyfelén beállítások, lásd: [futtatása az Azure parancssori felület a Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

A parancssori felület és függőségeinek telepítése, helyett az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Kattintson a **kipróbálás** gombra a parancsfájl, amely a következő, a felhő rendszerhéjat, amelyre bejelentkezik, amellyel bejelentkezhet az Azure-fiókjába. A parancsfájl végrehajtására, kattintson a **másolási** gombra, majd a tartalom illessze be a felhő rendszerhéj.

1. Hozzon létre egy erőforráscsoportot és két virtuális hálózatot.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Hozzon létre egy virtuális hálózati társviszony-létesítés a két virtuális hálózatok között.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Miután a parancsfájl végrehajtása során, tekintse át az egyes virtuális hálózati társviszony. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Futtassa az előző parancsot újból, cseréje *myVnet1* rendelkező *myVnet2*. Kimenetének mindkét parancsok látható **csatlakoztatva** a a **PeeringState** oszlop.

     Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
5. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-cli) ebben a cikkben.


## <a name="powershell"></a>Hozzon létre a társviszony - PowerShell

1. Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Egy PowerShell-munkamenet megkezdéséhez lépjen a **Start** menüre, írja be a **powershell** kifejezést, majd kattintson a **PowerShell** elemre.
3. A PowerShellben jelentkezzen be az Azure-ba a `login-azurermaccount` paranccsal. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
4. Hozzon létre egy erőforráscsoportot és két virtuális hálózatot. A parancsfájl végrehajtása, másolja a következő, illessze be a PowerShell, és nyomja le az `Enter` után utolsó sora a képernyőn jelenik meg:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Hozzon létre egy virtuális hálózati társviszony-létesítés a két virtuális hálózatok között. Másolja a következő, illessze be a PowerShell és nyomja le az `Enter` után utolsó sora a képernyőn jelenik meg:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Tekintse át a virtuális hálózati alhálózat, másolja be a következő parancsot, illessze be a PowerShell, és nyomja le az `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Futtassa az előző parancsot újból, cseréje *myVnet1* rendelkező *myVnet2*. Kimenetének mindkét parancsok látható **csatlakoztatva** a a **PeeringState** oszlop.
7. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
8. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-powershell) ebben a cikkben.

Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Hozzon létre a társviszony - Resource Manager-sablon

1. Hivatkozás [hozzon létre egy virtuális hálózati társviszony-létesítés](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager-sablon. A sablonhoz utasítások is járnak, amelyek alapján üzembe helyezheti a sablont az Azure Portal, a PowerShell vagy az Azure CLI használatával. Amelyik eszközzel való bejelentkezéshez válassza ki a sablon egy virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel rendelkező fiók használatával történő telepítéséhez. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
2. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
3. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete) című szakaszát, az Azure-portálon, a PowerShell vagy az Azure parancssori felület használatával.

## <a name="permissions"></a>Engedélyek

A fiókokat hozhat létre a virtuális hálózati társviszony-létesítés a szükséges szerepkör vagy az engedélyekkel kell rendelkeznie. Például, ha két virtuális hálózatok VNet1 és VNet2 nevű volt társviszony, a fiókjához társítva kell lenni a következő minimális szerepkör vagy az engedélyekkel minden virtuális hálózathoz:
    
|Virtuális hálózat|Szerepkör|Engedélyek|
|---|---|---|
|VNet1|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

További információ [beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és rendelje hozzá adott engedélyeket [egyéni szerepkörök](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (csak Resource Manager).

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után, előfordulhat, hogy törölni kívánja az erőforrások létrehozta az oktatóanyagban, használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. A portál keresési mezőbe, írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroup**, és kattintson a **törlése**.

### <a name="delete-cli"></a>Az Azure parancssori felület

Írja be a következő parancsot:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Írja be a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="next-steps"></a>Következő lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátozások és viselkedéshez](virtual-network-manage-peering.md#requirements-and-constraints) társviszony-létesítés üzemi virtuális hálózat létrehozása előtt használja.
- További tudnivalók az összes [virtuális hálózati társviszony-létesítési beállítások](virtual-network-manage-peering.md#create-a-peering).
- Megtudhatja, hogyan [létrehoz egy központot, és a hálózati topológia irány](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózati társviszony-létesítés.
