---
title: Hozzon létre egy Azure virtuális hálózati társviszony-létesítés - különböző telepítési modellek - azonos előfizetés | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre virtuális hálózati társviszony-létesítést az azonos Azure-előfizetésben létező különböző Azure-telepítési modelleken keresztül létrehozott virtuális hálózatok között.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023259"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Virtuális hálózati társviszony-létesítés létrehozása – különböző telepítési modellek, azonos előfizetés

Ebben az oktatóanyagban megtudhatja, hogy hozzon létre egy virtuális hálózat társviszony-létesítés a különböző telepítési modelleken keresztül létrehozott virtuális hálózatok között. Mindkét virtuális hálózat ugyanabban az előfizetésben létezik. Két virtuális hálózat társviszony-létesítése lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai ugyanolyan sávszélességgel és késleltetéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózatban lennének. További információ a [virtuális hálózati társviszony-létesítésről.](virtual-network-peering-overview.md)

A virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetések, és hogy a virtuális hálózatok milyen [Azure-telepítési modellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül jönnek létre. Megtudhatja, hogy miként hozhat létre virtuális hálózati társviszony-létesítést más esetekben az alábbi táblázat forgatókönyvére kattintva:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Különböző|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Különböző|

Virtuális hálózati társviszony-létesítés nem hozható létre a klasszikus üzembe helyezési modellen keresztül üzembe helyezett két virtuális hálózat között. Ha olyan virtuális hálózatokat kell csatlakoztatnia, amelyeket a klasszikus üzembe helyezési modellen keresztül hoztak létre, egy Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával csatlakoztathatja a virtuális hálózatokat.

Ez az oktatóanyag az ugyanabban a régióban lévő virtuális hálózatokat is lekell társának. A virtuális hálózatokat különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)is társviszonyban lévő konálhatja. Javasoljuk, hogy a virtuális hálózatok társviszony-létesítése előtt ismerkedjen meg a [társviszony-létesítési követelményekkel és korlátozásokkal.](virtual-network-manage-peering.md#requirements-and-constraints)

Az Azure Portal, az Azure [parancssori felület](#cli) (CLI), az Azure [PowerShell](#powershell)vagy az Azure Resource Manager-sablon használatával virtuális hálózati társviszony-létesítést hozhat létre. Kattintson az előző eszközhivatkozások bármelyikére, ha közvetlenül a virtuális hálózati társviszony-létesítés lépéseit szeretné megkeresni a választott eszközzel.

## <a name="create-peering---azure-portal"></a>Társviszony-létesítés létrehozása – Azure portal

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) A fiók, amelyen bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#requirements-and-constraints)
2. Kattintson **a + Új**, majd a Hálózat , majd a Virtuális **hálózat** **parancsra.**
3. A **Virtuális hálózat létrehozása** panelen adja meg vagy jelölje ki az értékeket a következő beállításokhoz, majd kattintson a **Létrehozás gombra:**
    - **Név**: *myVnet1*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.0.0.0/24*
    - **Előfizetés**: Válassza ki az előfizetést
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroup értéket*
    - **Helyszín**: *USA keleti része*
4. Kattintson az **+ Új** elemre. A **Keresés a Piactéren** mezőbe írja be a *Virtuális hálózat*kifejezést. Kattintson **a Virtuális hálózat** elemre, amikor megjelenik a keresési eredmények között.
5. A **Virtuális hálózat** panelen válassza a **Klasszikus** lehetőséget a **Telepítési modell kiválasztása** mezőben, majd kattintson a **Létrehozás gombra.**
6. A **Virtuális hálózat létrehozása** panelen adja meg vagy jelölje ki az értékeket a következő beállításokhoz, majd kattintson a **Létrehozás gombra:**
    - **Név**: *myVnet2*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.1.0.0/24*
    - **Előfizetés**: Válassza ki az előfizetést
    - **Erőforráscsoport**: Válassza **a Meglévő használata** lehetőséget, és válassza a *myResourceGroup* lehetőséget.
    - **Helyszín**: *USA keleti része*
7. A portál tetején található **Keresés erőforrások** mezőbe írja be a *myResourceGroup*kifejezést. Kattintson **a MyResourceGroup** elemre, amikor az megjelenik a keresési eredmények között. Megjelenik egy panel a **myresourcegroup erőforráscsoporthoz.** Az erőforráscsoport rendelkezik az előző lépésekben létrehozott két virtuális hálózattal.
8. Kattintson **a myVNet1 gombra.**
9. A megjelenő **myVnet1** panelen kattintson a panel bal oldalán található beállítások függőleges listájának **Társviszonyba való becsben** lehetőségre.
10. A **myVnet1 - Peerings** panelen kattintson a **+ Add +**
11. A megjelenő **Társviszony-létesítés hozzáadása** panelen adja meg vagy adja meg a következő beállításokat, majd kattintson az **OK**gombra:
     - **Név**: *myVnet1ToMyVnet2*
     - **Virtuális hálózat telepítési modellje**: Válassza a **Klasszikus**lehetőséget.
     - **Előfizetés**: Válassza ki az előfizetést
     - **Virtuális hálózat**: Kattintson **a Virtuális hálózat kiválasztása**elemre, majd a **myVnet2 parancsra.**
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **Engedélyezve** jelölőnégyzet be van jelölve.
    Az oktatóanyagban nincs más beállítás. Az összes társviszony-létesítési beállításról a [Virtuális hálózati társviszony-létesítések kezelése (Manage virtual networkings) (Virtuális hálózati társviszony-létesítések kezelése) (Virtuális hálózati társviszony-létesítések kezelése) (](virtual-network-manage-peering.md#create-a-peering)
12. Miután az előző lépésben az **OK** gombra kattintott, a **Társviszony-létesítés hozzáadása** panel bezárul, és ismét megjelenik a **myVnet1 – Peerings** panel. Néhány másodperc múlva megjelenik a létrehozott társviszony-létesítés a panelen. **A Connected** a létrehozott **myVnet1ToMyVnet2** társviszony-létesítés **társviszony-létesítése társviszony-létesítése társviszony-létesítése** oszlopában található.

    A társviszony-létesítés létrejött. A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
13. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra, a kapcsolat érvényesítéséhez.
14. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-portal) végre a cikk Erőforrások törlése című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Társviszony-létesítés létrehozása - Azure CLI

Hajtsa végre a következő lépéseket az Azure klasszikus CLI és az Azure CLI használatával. Az Azure Cloud Shell lépéseit az alábbi lépések bármelyikében a **Try it** gombra kattintva, a [klasszikus CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) és [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) telepítésével és a parancsok helyi számítógépen való futtatásával végezheti el.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancsmunkamenetet, `azure login` és jelentkezzen be az Azure-ba a paranccsal.
2. Futtassa a CLI-t `azure config mode asm` szolgáltatáskezelési módban a parancs beírásával.
3. Adja meg a következő parancsot, hogy megteremtse a virtuális hálózat (klasszikus):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Hajtsa végre a következő bash CLI parancsfájlt a CLI használatával, ne a klasszikus CLI használatával. A bash CLI parancsfájlok Windows rendszeren való futtatásának lehetőségeiről [az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)című témakörben található.

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

5. Hozzon létre egy virtuális hálózati társviszony-létesítés a két virtuális hálózatok révén létrehozott különböző telepítési modellek segítségével a CLI. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<subscription id>` le az előfizetés-azonosítójára. Ha nem ismeri az előfizetésazonosítóját, `az account show` írja be a parancsot. Az **azonosító** értéke a kimenetben az előfizetésazonosító. Illessze be a módosított parancsfájlt a `Enter`CLI-munkamenetbe, majd nyomja le a gombot.

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

6. A parancsfájl végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társviszony-létesítését. Másolja a következő parancsot, illessze be a `Enter`CLI-munkamenetbe, majd nyomja le a következő billentyűt:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   A kimenet a **Társviszony-létesítési állapot** oszlopban a **Csatlakoztatva** látható.

   A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
7. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra, a kapcsolat érvényesítéséhez.
8. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-cli) végre a cikk Erőforrások törlése című területén található lépéseket.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Társviszony-létesítés létrehozása – PowerShell

1. Telepítse a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és az [Az](https://www.powershellgallery.com/packages/Az/) modulok legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. PowerShell-munkamenet indítása.
3. A PowerShellben jelentkezzen be az `Add-AzureAccount` Azure-ba a parancs beírásával. A fiók, amelyen bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#requirements-and-constraints)
4. Virtuális hálózat (klasszikus) létrehozásához a PowerShell használatával létre kell hoznia egy új, vagy módosítania kell egy meglévő hálózati konfigurációs fájlt. További információ a [hálózati konfigurációs fájlok exportálásáról, frissítéséről és importálásáról.](virtual-networks-using-network-configuration-file.md) A fájlnak tartalmaznia kell a következő **VirtualNetworkSite** elemet az oktatóanyagban használt virtuális hálózathoz:

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
    > A módosított hálózati konfigurációs fájl importálása a meglévő (klasszikus) virtuális hálózatok módosítását okozhatja az előfizetésben. Győződjön meg arról, hogy csak az előző virtuális hálózatot adja hozzá, és hogy nem módosítja vagy távolítja el a meglévő virtuális hálózatokat az előfizetésből.
5. Jelentkezzen be az Azure-ba a virtuális hálózat `Connect-AzAccount` (Resource Manager) létrehozásához a parancs beírásával. A fiók, amelyen bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#requirements-and-constraints)
6. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Erőforrás-kezelő). Másolja a parancsfájlt, illessze be `Enter`a PowerShellbe, majd nyomja le a billentyűt.

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Hozzon létre egy virtuális hálózati társviszony-létesítés a két virtuális hálózatok között létrehozott különböző telepítési modellek. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<subscription id>` le az előfizetés-azonosítójára. Ha nem ismeri az előfizetés-azonosítóját, `Get-AzSubscription` írja be a megtekintéshez szükséges parancsot. Az **azonosító** értéke a visszaadott kimenetaz előfizetés-azonosító. A parancsfájl végrehajtásához másolja a módosított parancsfájlt a szövegszerkesztőből, majd kattintson `Enter`a jobb gombbal a PowerShell-munkamenetben, majd nyomja le a billentyűt.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. A parancsfájl végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társviszony-létesítését. Másolja a következő parancsot, illessze be a `Enter`PowerShell-munkamenetbe, majd nyomja le a billentyűt:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    A kimenet a **Társviszony-létesítési állapot** oszlopban a **Csatlakoztatva** látható.

    A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

9. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel ebben az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra, a kapcsolat érvényesítéséhez.
10. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-powershell) végre a cikk Erőforrások törlése című területén található lépéseket.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése

Ha befejezte az oktatóanyagot, érdemes törölni az oktatóanyagban létrehozott erőforrásokat, hogy ne kelljen használati díjat fizetnie. Az erőforráscsoport törlése az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portál

1. A portál keresőmezőjébe írja be a **myResourceGroup ( myResourceGroup**) értéket. A keresési eredmények között kattintson a **myResourceGroup**elemre.
2. A **myResourceGroup** panelen kattintson a **Törlés** ikonra.
3. A törlés megerősítéséhez írja **be az ERŐFORRÁSCSOPORT NEVE mezőbe** a **myResourceGroup**( Erőforráscsoport ) mezőbe, majd kattintson a **Törlés gombra.**

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Az Azure CLI segítségével törölje a virtuális hálózatot (Resource Manager) a következő paranccsal:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. A klasszikus CLI használatával törölheti a virtuális hálózatot (klasszikus) a következő parancsokkal:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. A virtuális hálózat (Erőforrás-kezelő) törléséhez írja be a következő parancsot:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. A virtuális hálózat (klasszikus) powershellnel történő törléséhez módosítania kell egy meglévő hálózati konfigurációs fájlt. További információ a [hálózati konfigurációs fájlok exportálásáról, frissítéséről és importálásáról.](virtual-networks-using-network-configuration-file.md) Távolítsa el az oktatóanyagban használt virtuális hálózat következő VirtualNetworkSite elemét:

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
    > A módosított hálózati konfigurációs fájl importálása a meglévő (klasszikus) virtuális hálózatok módosítását okozhatja az előfizetésben. Győződjön meg arról, hogy csak az előző virtuális hálózatot távolítja el, és nem módosítja vagy távolítja el a többi meglévő virtuális hálózatot az előfizetésből.

## <a name="next-steps"></a>További lépések

- Alaposan ismerkedjen meg a fontos [virtuális hálózati társviszony-létesítési korlátokkal és viselkedésekkel,](virtual-network-manage-peering.md#requirements-and-constraints) mielőtt éles környezetben létrehozna egy virtuális hálózati társviszony-létesítést.
- További információ a [virtuális hálózati társviszony-létesítési beállításokról.](virtual-network-manage-peering.md#create-a-peering)
- Ismerje meg, hogyan [hozhat létre központi és küllős hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) virtuális hálózati társviszony-létesítéssel.
