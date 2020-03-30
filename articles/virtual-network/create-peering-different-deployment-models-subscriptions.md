---
title: Hozzon létre egy Azure virtuális hálózati társviszony-létesítés - különböző üzembe helyezési modellek -különböző előfizetések
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre virtuális hálózati társviszony-létesítést a különböző Azure-előfizetésekben létező különböző Azure-telepítési modelleken keresztül létrehozott virtuális hálózatok között.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239844"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Virtuális hálózati társviszony-létesítés létrehozása – különböző telepítési modellek és előfizetések

Ebben az oktatóanyagban megtudhatja, hogy hozzon létre egy virtuális hálózat társviszony-létesítés a különböző telepítési modelleken keresztül létrehozott virtuális hálózatok között. A virtuális hálózatok különböző előfizetésekben léteznek. Két virtuális hálózat társviszony-létesítése lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai ugyanolyan sávszélességgel és késleltetéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózatban lennének. További információ a [virtuális hálózati társviszony-létesítésről.](virtual-network-peering-overview.md)

A virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetések, és hogy a virtuális hálózatok milyen [Azure-telepítési modellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül jönnek létre. Megtudhatja, hogy miként hozhat létre virtuális hálózati társviszony-létesítést más esetekben az alábbi táblázat forgatókönyvére kattintva:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Különböző|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Ugyanaz|

Virtuális hálózati társviszony-létesítés nem hozható létre a klasszikus üzembe helyezési modellen keresztül üzembe helyezett két virtuális hálózat között. Ez az oktatóanyag az ugyanabban a régióban létező virtuális hálózatokat használja. Ez az oktatóanyag az ugyanabban a régióban lévő virtuális hálózatokat is lekell társának. A virtuális hálózatokat különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)is társviszonyban lévő konálhatja. Javasoljuk, hogy a virtuális hálózatok társviszony-létesítése előtt ismerkedjen meg a [társviszony-létesítési követelményekkel és korlátozásokkal.](virtual-network-manage-peering.md#requirements-and-constraints)

A különböző előfizetésekben létező virtuális hálózatok közötti virtuális hálózati társviszony-létesítés létrehozásakor az előfizetéseket ugyanahhoz az Azure Active Directory-bérlőhöz kell társozni. Ha még nem rendelkezik Azure Active Directory-bérlővel, gyorsan [létrehozhat egyet.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant) Az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)használatával különböző előfizetésekben és különböző Azure Active Directory-bérlőkben lévő virtuális hálózatokat csatlakoztathat.

Használhatja az [Azure Portalon,](#portal)az Azure [parancssori felület](#cli) (CLI) vagy az Azure [PowerShell](#powershell) virtuális hálózati társviszony-létesítés. Kattintson az előző eszközhivatkozások bármelyikére, ha közvetlenül a virtuális hálózati társviszony-létesítés lépéseit szeretné megkeresni a választott eszközzel.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Társviszony-létesítés létrehozása – Azure portal

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, ugyanazt a fiókot használhatja az összes lépéshez, kihagyhatja a portálból való kijelentkezés lépéseit, és kihagyhatja a lépéseket egy másik felhasználói engedély hozzárendeléséhez a virtuális hálózatokhoz.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) UserA néven. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
2. Kattintson **a + Új**, majd a Hálózat , majd a Virtuális **hálózat** **parancsra.**
3. A **Virtuális hálózat létrehozása** panelen adja meg vagy jelölje ki az értékeket a következő beállításokhoz, majd kattintson a **Létrehozás gombra:**
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.0.0.0/24*
    - **Előfizetés**: Válassza ki az A előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupA értéket*
    - **Helyszín**: *USA keleti része*
4. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetA*kifejezést. Kattintson **a myVnetA** elemre, amikor megjelenik a keresési eredmények között. Egy panel jelenik meg a **myVnetA** virtuális hálózathoz.
5. A megjelenő **myVnetA** panelen kattintson a panel bal oldalán található beállítások függőleges listájának **Hozzáférés-vezérlési (IAM)** elemére.
6. A megjelenő **myVnetA – hozzáférés-vezérlési (IAM)** panelen kattintson a **+ Szerepkör-hozzárendelés hozzáadása gombra.**
7. A megjelenő **Szerepkör-hozzárendelés hozzáadása** panelen válassza a **Hálózati közreműködő** lehetőséget a **Szerepkör** mezőben.
8. A **Kijelölés** mezőbe válassza a Felhasználó B lehetőséget, vagy írja be a Felhasználó e-mail címét a kereséshez. A megjelenített felhasználók listája ugyanabból az Azure Active Directory-bérlőből származik, mint a virtuális hálózat, amelyhez a társviszony-létesítést állítja be. Kattintson a Felhasználó B gombra, amikor megjelenik a listában.
9. Kattintson a **Mentés** gombra.
10. Jelentkezzen ki a portálról UserA néven, majd jelentkezzen be B felhasználóként.
11. Kattintson **a + Új** *gombra,* írja be a Virtuális hálózat kifejezést **a Keresés a Piactéren** mezőbe, majd kattintson a **Virtuális hálózat** elemre a keresési eredmények között.
12. A megjelenő **Virtuális hálózat** panelen válassza a **Klasszikus** lehetőséget a Telepítési **modell kiválasztása** mezőben, majd kattintson a **Létrehozás gombra.**
13. A megjelenő Virtuális hálózat létrehozása (klasszikus) mezőbe írja be a következő értékeket:

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.1.0.0/24*
    - **Előfizetés**: Válassza a B előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupB értéket*
    - **Helyszín**: *USA keleti része*

14. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetB kifejezést.* Kattintson **a myVnetB elemre,** amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnetB** virtuális hálózathoz.
15. A megjelenő **myVnetB** panelen kattintson a panel bal oldalán található beállítások függőleges listájának **Tulajdonságok** elemére. Másolja a **forrásazonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító hasonló a következő példához:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Hajtsa végre az 5-9. **UserA**
17. Jelentkezzen ki a portálról B felhasználóként, és jelentkezzen be A felhasználóként.
18. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetA*kifejezést. Kattintson **a myVnetA** elemre, amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnet** virtuális hálózathoz.
19. Kattintson **a myVnetA gombra.**
20. A megjelenő **myVnetA** panelen kattintson a panel bal oldalán található beállítások függőleges listájának **Társviszonyba kerülése** elemre.
21. A **myVnetA - Peerings** panelen kattintson a **+ Add**
22. A megjelenő **Társviszony-létesítés hozzáadása** panelen adja meg vagy adja meg a következő beállításokat, majd kattintson az **OK**gombra:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modellje**: Válassza a **Klasszikus**lehetőséget.
     - **Ismerem az erőforrás-azonosítómat:** Jelölje be ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító:** Adja meg a myVnetB erőforrásazonosítóját a 15.
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **Engedélyezve** jelölőnégyzet be van jelölve.
    Az oktatóanyagban nincs más beállítás. Az összes társviszony-létesítési beállításról a [Virtuális hálózati társviszony-létesítések kezelése (Manage virtual networkings) (Virtuális hálózati társviszony-létesítések kezelése) (Virtuális hálózati társviszony-létesítések kezelése) (](virtual-network-manage-peering.md#create-a-peering)
23. Miután az előző lépésben az **OK** gombra kattintott, a **Társviszony-létesítés hozzáadása** panel bezárul, és ismét megjelenik a **myVnetA - Peerings** panel. Néhány másodperc múlva megjelenik a létrehozott társviszony-létesítés a panelen. **A Connected** a létrehozott **myVnetAToMyVnetB** társviszony-létesítés **társviszony-létesítésének TÁRSVISZONY-LÉTESÍTÉSe** oszlopában található. A társviszony-létesítés létrejött. Nincs szükség a virtuális hálózat (klasszikus) társviszony-leépítésére a virtuális hálózatra (Resource Manager).

    A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

24. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
25. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-portal) végre a cikk Erőforrások törlése című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Társviszony-létesítés létrehozása - Azure CLI

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, használhatja ugyanazt a fiókot az összes lépéshez, kihagyhatja az Azure-ból való kijelentkezés lépéseit, és eltávolíthatja a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájlsorokat. Cserélje UserA@azure.com UserB@azure.com le az alábbi parancsfájlok mindegyikét a UserA és A UserB használt felhasználónevekre. Hajtsa végre a következő lépéseket az Azure klasszikus CLI és az Azure CLI használatával. Az Azure Cloud Shell lépéseit az alábbi lépések bármelyikében a **Try it** gombra kattintva, a [klasszikus CLI](/cli/azure/install-classic-cli) és [CLI](/cli/azure/install-azure-cli) telepítésével és a parancsok helyi számítógépen való futtatásával végezheti el.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancsmunkamenetet, `azure login` és jelentkezzen be az Azure-ba a paranccsal.
2. Futtassa a klasszikus CLI-t `azure config mode asm` szolgáltatáskezelési módban a parancs beírásával.
3. Adja meg a következő klasszikus CLI parancsot a virtuális hálózat létrehozásához (klasszikus):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. A fennmaradó lépéseket el kell végezni egy bash shell az Azure CLI (nem a klasszikus CLI) használatával kell végrehajtani.
5. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<SubscriptionB-Id>` le az előfizetés-azonosítójára. Ha nem ismeri az előfizetésazonosítóját, `az account show` írja be a parancsot. Az **azonosító** értéke a kimenetben az előfizetés-azonosító. Másolja a módosított parancsfájlt, illessze `Enter`be a CLI-munkamenetbe, majd nyomja meg a billentyűt.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Amikor létrehozta a virtuális hálózat (klasszikus) a 4. *Default-Networking*
6. Jelentkezzen ki a UserB-t az Azure-ból, és jelentkezzen be A felhasználóként a CLI-ben.
7. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Erőforrás-kezelő). Másolja a következő parancsfájlt, illessze be a `Enter`CLI-munkamenetbe, majd nyomja le a gombot.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Hozzon létre egy virtuális hálózati társviszony-létesítés a két virtuális hálózatok között létrehozott különböző telepítési modellek. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<SubscriptionB-id>` le az előfizetés-azonosítójára. Ha nem ismeri az előfizetésazonosítóját, `az account show` írja be a parancsot. Az **id** értéke a kimenetben az előfizetés-azonosító. Az Azure létrehozta a virtuális hálózatot (klasszikus) a *4.* Illessze be a módosított parancsfájlt a `Enter`CLI-munkamenetbe, majd nyomja le a gombot.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. A parancsfájl végrehajtása után tekintse át a virtuális hálózat (Resource Manager) társviszony-létesítését. Másolja a következő parancsfájlt, majd illessze be a CLI-munkamenetbe:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    A kimenet a **Társviszony-létesítési állapot** oszlopban a **Csatlakoztatva** látható.

    A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

10. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
11. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-cli) végre a cikk Erőforrások törlése című területén található lépéseket.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Társviszony-létesítés létrehozása – PowerShell

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, használhatja ugyanazt a fiókot az összes lépéshez, kihagyhatja az Azure-ból való kijelentkezés lépéseit, és eltávolíthatja a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájlsorokat. Cserélje UserA@azure.com UserB@azure.com le az alábbi parancsfájlok mindegyikét a UserA és A UserB használt felhasználónevekre. 

1. Telepítse a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és az [Az](https://www.powershellgallery.com/packages/Az) modulok legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. PowerShell-munkamenet indítása.
3. A PowerShellben jelentkezzen be a UserB felhasználó jap-előfizetésébe a `Add-AzureAccount` parancs beírásával. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
4. Virtuális hálózat (klasszikus) létrehozásához a PowerShell használatával létre kell hoznia egy új, vagy módosítania kell egy meglévő hálózati konfigurációs fájlt. További információ a [hálózati konfigurációs fájlok exportálásáról, frissítéséről és importálásáról.](virtual-networks-using-network-configuration-file.md) A fájlnak tartalmaznia kell a következő **VirtualNetworkSite** elemet az oktatóanyagban használt virtuális hálózathoz:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Jelentkezzen be a UserB előfizetésébe, mint Felhasználó, `Connect-AzAccount` hogy a parancs beírásával használja az Erőforrás-kezelő parancsokat.
6. Felhasználói engedélyek hozzárendelése a virtuális hálózathoz B. Másolja a következő `<SubscriptionB-id>` parancsfájlt egy szövegszerkesztőbe a számítógépen, és cserélje le a B előfizetés azonosítójára. Ha nem ismeri az előfizetésazonosítót, `Get-AzSubscription` adja meg a megtekintési parancsot. Az **azonosító** értéke a visszaadott kimenetaz előfizetés-azonosító. Az Azure létrehozta a *4.* A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze `Enter`be a PowerShellbe, majd nyomja le a billentyűt.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Jelentkezzen ki az Azure-ból B felhasználóként, és jelentkezzen be `Connect-AzAccount` a UserA felhasználói előfizetésébe a parancs beírásával. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
8. Hozza létre a virtuális hálózatot (Resource Manager) a következő parancsfájl másolásával, `Enter`a PowerShellbe való beillesztésével, majd a következő gomb lenyomásával:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Felhasználó engedélyek hozzárendelése a myVnetA-hoz. Másolja a következő parancsfájlt egy szövegszerkesztőbe a számítógépen, és cserélje le `<SubscriptionA-Id>` az A előfizetés azonosítójára. Ha nem ismeri az előfizetésazonosítót, `Get-AzSubscription` adja meg a megtekintési parancsot. Az **azonosító** értéke a visszaadott kimenetaz előfizetés-azonosító. Illessze be a parancsfájl módosított verzióját a `Enter` PowerShellbe, majd nyomja meg a parancsfájl végrehajtásához.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Másolja a következő parancsfájlt egy szövegszerkesztőbe `<SubscriptionB-id>` a számítógépen, és cserélje le a B előfizetés azonosítójára. Ha a myVnetA-t a myVNetB-hez szeretné társviszonyba adni, `Enter`másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja le a billentyűt.

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Tekintse meg a myVnetA társviszony-létesítési állapotát a következő parancsfájl `Enter`másolásával, a PowerShellbe való beillesztésével és a gomb lenyomásával.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **Csatlakoztatva**van. A myVnetB-ből a myVnetA-hoz való társviszony-létesítés beállítása után **a Csatlakoztatott** beállításra változik.

    A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

12. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
13. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-powershell) végre a cikk Erőforrások törlése című területén található lépéseket.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése
Ha befejezte az oktatóanyagot, érdemes törölni az oktatóanyagban létrehozott erőforrásokat, hogy ne kelljen használati díjat fizetnie. Az erőforráscsoport törlése az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portál

1. A portál keresőmezőjébe írja be a **myResourceGroupA**értéket. A keresési eredmények között kattintson a **myResourceGroupA**elemre.
2. A **myResourceGroupA** panelen kattintson a **Törlés** ikonra.
3. A törlés megerősítéséhez írja **be az ERŐFORRÁSCSOPORT NEVE mezőbe** a **myResourceGroupA**értéket, majd kattintson a **Törlés gombra.**
4. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetB kifejezést.* Kattintson **a myVnetB elemre,** amikor megjelenik a keresési eredmények között. Megjelenik egy panel a **myVnetB** virtuális hálózathoz.
5. A **myVnetB** panelen kattintson a **Törlés gombra.**
6. A törlés megerősítéséhez kattintson az **Igen** gombra a **Virtuális hálózat törlése** mezőben.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-ba a CLI segítségével a virtuális hálózat (Resource Manager) törléséhez a következő paranccsal:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Jelentkezzen be az Azure-ba a klasszikus CLI használatával a virtuális hálózat (klasszikus) törléséhez a következő parancsokkal:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. A PowerShell parancssorába írja be a következő parancsot a virtuális hálózat (Resource Manager) törléséhez:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. A virtuális hálózat (klasszikus) powershellnel történő törléséhez módosítania kell egy meglévő hálózati konfigurációs fájlt. További információ a [hálózati konfigurációs fájlok exportálásáról, frissítéséről és importálásáról.](virtual-networks-using-network-configuration-file.md) Távolítsa el az oktatóanyagban használt virtuális hálózat következő VirtualNetworkSite elemét:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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