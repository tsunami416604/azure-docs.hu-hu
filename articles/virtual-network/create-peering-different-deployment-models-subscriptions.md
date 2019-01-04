---
title: Hozzon létre egy Azure virtuális hálózatok közötti társviszony - különböző üzemi modellek – különböző előfizetések
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre, amelyek szerepelnek az Azure-előfizetések különböző Azure üzembehelyezési modellekkel létrehozott virtuális hálózatok közötti társviszony virtuális hálózatot.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 45dd31ecca4f09a595fbf1dae5fe61623fb2be3c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025861"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Hozzon létre egy virtuális hálózati társviszony - másik üzembe helyezési modellek és -előfizetések

Ebben az oktatóanyagban elsajátíthatja egy virtuális hálózat, különböző üzembehelyezési modellel létrehozott virtuális hálózatok közötti társviszony létrehozásához. A virtuális hálózatok különböző előfizetésekben található. Társviszony-létesítés virtuális hálózatok különböző virtuális hálózatokban kommunikálni egymással azonos sávszélesség és késés, mintha az erőforrásokat is ugyanabban a virtuális hálózatban lévő két lehetővé teszi, hogy erőforrásokat. Tudjon meg többet [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md).

A virtuális hálózatok közötti társviszony létrehozásához szükséges lépéseket, attól függően különböznek egymástól vannak-e a virtuális hálózatok a azonos vagy eltérő előfizetések és amely [Azure üzembehelyezési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) jönnek létre a virtuális hálózatokon keresztül. Ismerje meg, hogyan hozhat létre virtuális hálózati társviszonyt más forgatókönyvek esetében a következő táblázat a forgatókönyv kattintva:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Azonos|
|[Mindkét Resource Manager](create-peering-different-subscriptions.md) |Eltérő|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Azonos|

A virtuális hálózatok közötti társviszony nem hozható létre, a klasszikus üzemi modellel üzembe helyezett két virtuális hálózat között. Ebben az oktatóanyagban található virtuális hálózat között ugyanabban a régióban. Ebben az oktatóanyagban ugyanabban a régióban található virtuális hálózatok is társul. Emellett társviszonyt virtuális hálózatok különböző [támogatott régiók](virtual-network-manage-peering.md#cross-region). Javasoljuk, hogy Ön feltérképezése a [társviszony-létesítés követelményei és korlátozásai](virtual-network-manage-peering.md#requirements-and-constraints) előtt társviszony-létesítés virtuális hálózatok.

Egy virtuális hálózatot, amely különböző előfizetésekben található virtuális hálózatok közötti társviszony létrehozása esetén az előfizetések is kell rendelni ugyanahhoz az Azure Active Directory-bérlőhöz. Ha még nem rendelkezik Azure Active Directory-bérlővel, akkor gyorsan [hozzon létre egyet](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Különböző előfizetésben található virtuális hálózatok és a különböző Azure Active Directory-bérlők használatával egy Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Használhatja a [az Azure portal](#portal), az Azure [parancssori felület](#cli) (CLI), vagy az Azure [PowerShell](#powershell) egy virtuális hálózati társviszony-létesítés létrehozása. Kattintson az előző eszköz hivatkozásokra, hogy közvetlenül a virtuális hálózati társviszonyt a választott eszköz használatával létrehozásának menete.

## <a name="portal"></a>Társviszony - létrehozása az Azure Portalon

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, a naplózáshoz a portálról a lépés kihagyható és egy másik felhasználói engedélyek hozzárendelése a virtuális hálózatok számára a lépés kihagyható.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) Felhasználóa felhasználóként. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
2. Kattintson a **+ új**, kattintson a **hálózatkezelés**, majd kattintson a **virtuális hálózati**.
3. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson a **létrehozás**:
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: Válassza ki előfizetését rögzíti.
    - **Erőforráscsoport**: Válassza ki **új létrehozása** , és adja meg *myResourceGroupA*
    - **Hely**: *USA keleti RÉGIÓJA*
4. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetA*. Kattintson a **myVnetA** amikor megjelenik a keresési eredmények között. Megjelenik egy panel az **myVnetA** virtuális hálózatot.
5. Az a **myVnetA** panel, amelyen megjelenik, kattintson a **hozzáférés-vezérlés (IAM)** a panel bal oldali beállítások függőleges listájából.
6. Az a **myVnetA - hozzáférés-vezérlés (IAM)** panel, amelyen megjelenik, kattintson a **+ szerepkör-hozzárendelés hozzáadása**.
7. Az a **szerepkör-hozzárendelés hozzáadása** panel, amelyen megjelenik, válassza ki **hálózati közreműködő** a a **szerepkör** mezőbe.
8. Az a **kiválasztása** mezőben válassza ki a Felhasználób, vagy írja be a Felhasználób e-mail-címet a keresési funkciót. A társviszony-létesítés beállítása a virtuális hálózatnak ugyanahhoz az Azure Active Directory bérlőhöz van a kiválasztott felhasználók látható. Amikor megjelenik a listában, kattintson a Felhasználób.
9. Kattintson a **Save** (Mentés) gombra.
10. Jelentkezzen ki a portálra a Felhasználóa felhasználóként, majd jelentkezzen be Felhasználób felhasználóként.
11. Kattintson a **+ új**, típus *virtuális hálózati* a a **keresés a piactéren** mezőbe, majd kattintson a **virtuális hálózati** a keresési eredmények között.
12. Az a **virtuális hálózat** panel, amelyen megjelenik, válassza ki **klasszikus** a a **telepítési modell kiválasztása** mezőbe, majd kattintson a **létrehozás**.
13.   A létrehozás virtuális hálózat (klasszikus) mezőben megjelenő adja meg a következő értékeket:

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: B előfizetés kiválasztása
    - **Erőforráscsoport**: Válassza ki **új létrehozása** , és adja meg *myResourceGroupB*
    - **Hely**: *USA keleti RÉGIÓJA*

14. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetB*. Kattintson a **myVnetB** amikor megjelenik a keresési eredmények között. Megjelenik egy panel az **myVnetB** virtuális hálózatot.
15. Az a **myVnetB** panel, amelyen megjelenik, kattintson a **tulajdonságok** a panel bal oldali beállítások függőleges listájából. Másolás a **erőforrás-azonosító**, amely egy későbbi lépésben szolgál. Az erőforrás-azonosítója a következő példához hasonlít: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Végezze el az 5 – 9 lépéseket myVnetB, írja be a **Felhasználóa** 8. lépés.
17. Jelentkezzen ki a Felhasználób portált, és jelentkezzen be a Felhasználóa felhasználóként.
18. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetA*. Kattintson a **myVnetA** amikor megjelenik a keresési eredmények között. Megjelenik egy panel az **myVnet** virtuális hálózatot.
19. Kattintson a **myVnetA**.
20. Az a **myVnetA** panel, amelyen megjelenik, kattintson a **Társviszonyok** a panel bal oldali beállítások függőleges listájából.
21. Az a **myVnetA - Társviszonyok** panel, amelyen jelent meg, kattintson a **+ Hozzáadás**
22. Az a **Hozzáadás társviszony-létesítés** megjelenő panelen adja meg, vagy válassza ki a következő beállításokat, majd kattintson **OK**:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat üzemelőpéldány-modellje**:  Válassza ki **klasszikus**.
     - **Tudom, hogy az erőforrás-Azonosítómat**: Ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: Adja meg az erőforrás-Azonosítójára myVnetB 15. lépés.
     - **Virtuális hálózati hozzáférés engedélyezése:** Ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállításokat használja. Minden társviszony beállításai kapcsolatos további információkért olvassa el a [kezelheti a virtuális társhálózatok](virtual-network-manage-peering.md#create-a-peering).
23. Kattintás után **OK** az előző lépésben a **Hozzáadás társviszony-létesítés** panel bezárul, és megjelenik a **myVnetA - Társviszonyok** újra a panelt. Néhány másodperc elteltével a társviszony-létesítés létrehozott megjelenik a panelen. **Csatlakoztatott** szerepel-e a **társviszony-LÉTESÍTÉS állapota** oszlopában a **myVnetAToMyVnetB** társviszony-létesítés, létrehozva. A most már létrejött a társviszony. Hiba esetén nem kell a virtuális hálózat (klasszikus) a virtuális hálózathoz (Resource Manager).

    Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
25. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések a [törölje az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure CLI

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás az Azure-ból és a sorok szkript által létrehozott felhasználói szerepkör-hozzárendelések eltávolításához. Cserélje le UserA@azure.com és UserB@azure.com az alábbi parancsfájlok a Felhasználóa és Felhasználób használata a felhasználónevek mindegyikét. A következő lépéseket a klasszikus Azure CLI és az Azure CLI használatával. Csak kiválasztásával kitöltheti a lépéseket követve az Azure Cloud Shell a **próbálja ki** gombot a következő lépésekből áll, vagy telepítse a [klasszikus parancssori felület](/cli/azure/install-classic-cli) és [CLI](/cli/azure/install-azure-cli) és a parancsok futtatása a helyi számítógépen.

1. A Cloud Shell használata esetén ugorjon a 2, mert a Cloud Shell automatikusan bejelentkezik, az Azure-bA. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be Azure-bA a `azure login` parancsot.
2. A klasszikus parancssori felület futtatása szolgáltatásfelügyelet módban megadásával a `azure config mode asm` parancsot.
3. Adja meg a következő klasszikus parancssori felület parancsot a virtuális hálózat létrehozása (klasszikus modell):

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. A további lépéseket kell elvégezni a bash rendszerhéj használatával az Azure CLI-vel (nem a klasszikus parancssori felület).
5. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen. Cserélje le `<SubscriptionB-Id>` az előfizetés-azonosítóval. Ha az előfizetés-azonosító nem ismeri, adja meg a `az account show` parancsot. Az érték **azonosító** kimenet az előfizetés azonosítóját. Másolja a módosított szkripttel, illessze be a CLI-munkamenetet, és nyomja le az `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Ha a virtuális hálózat (klasszikus), a 4. lépésben létrehozott, Azure létrehozott-e a virtuális hálózatot a a *alapértelmezett hálózati* erőforráscsoportot.
6. Azure, és jelentkezzen be a Felhasználób jelentkezzen a parancssori felületen Felhasználóa felhasználóként.
7. Hozzon létre egy erőforráscsoportot és a egy virtuális hálózatot (Resource Manager). Másolja a következő, illessze be a CLI-munkamenetet, és nyomja le az `Enter`.

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

8. A két, a különböző üzembehelyezési modellel létrehozott virtuális hálózatok közötti társviszony-létesítés virtuális hálózat létrehozása. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen. Cserélje le `<SubscriptionB-id>` -előfizetése azonosítóját. Ha az előfizetés-azonosító nem ismeri, adja meg a `az account show` parancsot. Az érték **azonosító** kimenet az előfizetés azonosítóját. Azure a virtuális hálózat (klasszikus), a 4. lépésben az erőforráscsoport neve hozott létre *alapértelmezett hálózati*. Illessze be a módosított szkriptet a CLI-munkamenetben, és nyomja le az `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. A szkript végrehajtása után tekintse át a társviszony-létesítés virtuális hálózatok (Resource Manager). Másolja a következő szkriptet, és illessze a CLI-munkamenetben:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop.

    Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
11. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-cli) ebben a cikkben.

## <a name="powershell"></a>-Társviszony-létesítés PowerShell

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás az Azure-ból és a sorok szkript által létrehozott felhasználói szerepkör-hozzárendelések eltávolításához. Cserélje le UserA@azure.com és UserB@azure.com az alábbi parancsfájlok a Felhasználóa és Felhasználób használata a felhasználónevek mindegyikét. 

1. Telepítse a PowerShell legújabb verzióját [Azure](https://www.powershellgallery.com/packages/Azure) és [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulok. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítsa el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be a Felhasználób előfizetésbe Felhasználób, írja be a `Add-AzureAccount` parancsot. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
4. Virtuális hálózat (klasszikus) létrehozása a PowerShell használatával, hozzon létre egy új, vagy módosítsa egy meglévő, hálózati konfigurációs fájlt. Ismerje meg, hogyan [exportálása, frissítése és importálása a hálózati konfigurációs fájlokban](virtual-networks-using-network-configuration-file.md). A fájl tartalmaznia kell a következő **VirtualNetworkSite** elem a virtuális hálózat, a jelen oktatóanyagban használt:

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
    > Megváltozott hálózati konfigurációs fájl importálása okozhat a módosításokat a meglévő virtuális hálózatok (klasszikus) az előfizetésében. Győződjön meg arról, csak adja hozzá a korábbi virtuális hálózat és az, hogy ne módosítsa vagy bármely meglévő virtuális hálózatok eltávolítása az előfizetésből. 

5. Jelentkezzen be a Felhasználób előfizetésen Felhasználób használata a Resource Manager-parancsok beírásával a `Connect-AzureRmAccount` parancsot.
6. "A" felhasználó engedélyek hozzárendelése a virtuális hálózat b másolása a következő parancsfájl egy szövegszerkesztőbe, a Számítógépről, majd cserélje le a `<SubscriptionB-id>` a b előfizetés azonosítója Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` paranccsal megtekintheti azokat. Az érték **azonosító** visszaadott kimenet van az előfizetés-azonosítójára. Azure a virtuális hálózat (klasszikus), a 4. lépésben az erőforráscsoport neve hozott létre *alapértelmezett hálózati*. A szkript végrehajtásához másolja a módosított szkripttel, illessze be a powershellt, majd nyomja le `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Jelentkezzen ki az Azure-t a Felhasználób, és írja be "a" felhasználó az előfizetés Felhasználóa felhasználóként jelentkezzen be a `Connect-AzureRmAccount` parancsot. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
8. A virtuális hálózat (Resource Manager) létrehozása a következő parancsfájl másolása, beillesztése a powershellt, és nyomja `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Rendeljen myVnetA Felhasználób engedélyeket. Másolja a következő parancsfájl egy szövegszerkesztőbe, a Számítógépről, majd cserélje le a `<SubscriptionA-Id>` az a oszlopba. előfizetés azonosítója Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` paranccsal megtekintheti azokat. Az érték **azonosító** visszaadott kimenet van az előfizetés-azonosítójára. Illessze be a PowerShell a parancsfájl módosított változatát, majd nyomja le `Enter` végrehajtásra.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen, és cserélje le `<SubscriptionB-id>` a b előfizetés azonosítója MyVnetA myVNetB a társviszonyt, másolja a módosított szkripttel, illessze be a PowerShell, és nyomja le az `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. MyVnetA társviszony állapotának megtekintéséhez a következő parancsfájl másolása, illessze be azt a PowerShell rendszerbe, majd nyomja le `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **csatlakoztatva**. Állapotúra változik **csatlakoztatva** a társviszonyt a myVnetA myVnetB beállítása után.

    Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
13. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-powershell) ebben a cikkben.

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után érdemes törölni, így nem használati díjak merülhetnek fel az oktatóanyagban létrehozott erőforrásokat. Egy erőforráscsoport törlésekor a, amelyek az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="delete-portal"></a>Azure Portal

1. A portál keresőmezőbe írja be a **myResourceGroupA**. A keresési eredmények között kattintson **myResourceGroupA**.
2. Az a **myResourceGroupA** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **írja be az ERŐFORRÁSCSOPORT nevét** mezőbe írja be **myResourceGroupA**, és kattintson a **törlése**.
4. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetB*. Kattintson a **myVnetB** amikor megjelenik a keresési eredmények között. Megjelenik egy panel az **myVnetB** virtuális hálózatot.
5. Az a **myVnetB** panelen kattintson a **törlése**.
6. Kattintson a törlés megerősítéséhez **Igen** a a **virtuális hálózat törlése** mezőbe.

### <a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-ban a parancssori felületről törölni a virtuális hálózatot (Resource Manager) a következő paranccsal:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Jelentkezzen be Azure-bA a klasszikus parancssori felület használatával törli a virtuális hálózat (klasszikus) a következő parancsokkal:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. A PowerShell parancssorába írja be a törölni a virtuális hálózatot (Resource Manager) a következő parancsot:

   ```powershell
   Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
   ```

2. A virtuális hálózat törlése a PowerShell (klasszikus modell) kell módosítani egy meglévő hálózati konfigurációs fájlt. Ismerje meg, hogyan [exportálása, frissítése és importálása a hálózati konfigurációs fájlokban](virtual-networks-using-network-configuration-file.md). Távolítsa el a virtuális hálózat, a jelen oktatóanyagban használt VirtualNetworkSite a következő elemet:

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
   > Megváltozott hálózati konfigurációs fájl importálása okozhat a módosításokat a meglévő virtuális hálózatok (klasszikus) az előfizetésében. Győződjön meg arról, csak távolítsa el a korábbi virtuális hálózatot, és nem módosítja a, illetve bármely más meglévő virtuális hálózatok eltávolítása az előfizetésből. 

## <a name="next-steps"></a>További lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátok és működési módokkal](virtual-network-manage-peering.md#requirements-and-constraints) társviszonyt az éles virtuális hálózat létrehozása előtt használja.
- További információ az összes [virtuális hálózati társviszony beállításról](virtual-network-manage-peering.md#create-a-peering).
- Ismerje meg, hogyan [eseményközpont létrehozásához és a hálózati topológia küllő](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózatok közötti társviszony.