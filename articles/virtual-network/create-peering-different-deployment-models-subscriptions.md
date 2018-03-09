---
title: "Hozzon létre egy Azure virtuális hálózati társviszony - különböző üzembe helyezési modellek - különböző előfizetések |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy virtuális hálózati társviszony-létesítés különböző Azure üzembe helyezési modellel, az Azure-előfizetések létező segítségével létrehozott virtuális hálózatok között."
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: a65e49c465e83d2e76f33019984eec16331ed6ea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Hozzon létre egy virtuális hálózati társviszony - különböző üzembe helyezési modellek és előfizetések

Ebben az oktatóanyagban elsajátíthatja társviszony-létesítés különböző üzembe helyezési modellel létrehozott virtuális hálózatok közötti virtuális hálózat létrehozása. A virtuális hálózatok léteznek a különböző előfizetésekhez. A különböző virtuális hálózatokon kommunikálni egymással sávszélesség és a késés, mintha az erőforrások volt az azonos virtuális hálózatban lévő két virtuális hálózatok lehetővé teszi, hogy erőforrásokat társviszony-létesítés. További információ [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md).

Virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok ugyanazon, vagy másik, előfizetések, és amely [Azure telepítési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok használatával jönnek létre. Megtudhatja, hogyan hozhat létre virtuális hálózatot gombra kattintva a következő táblázat a forgatókönyv más esetekben társviszony:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét erőforrás-kezelő](tutorial-connect-virtual-networks-portal.md) |Azonos|
|[Mindkét erőforrás-kezelő](create-peering-different-subscriptions.md) |Eltérő|
|[Egy erőforrás-kezelő egy klasszikus](create-peering-different-deployment-models.md) |Azonos|

Virtuális hálózati társviszony-létesítés nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózatok között. Ez az oktatóanyag használja a virtuális hálózatokat ugyanabban a régióban. Virtuális hálózatok különböző régiókban egyenrangú nem kép. Ezt a funkciót használja, le kell [regisztrálása](#register). 

A virtuális hálózati társviszony-létesítés különböző előfizetéshez létező virtuális hálózatok közötti létrehozásakor az előfizetések is kell társítani a azonos Azure Active Directory-bérlő. Ha még nem rendelkezik egy Azure Active Directory-bérlőt, akkor gyorsan [hozzon létre egyet](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). Virtuális hálózatok különböző előfizetésekhez is elérheti, és különböző Azure Active Directory bérlők egy Azure használatával [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Használhatja a [Azure-portálon](#portal), az Azure [parancssori felület](#cli) (CLI), vagy Azure [PowerShell](#powershell) létrehozni a virtuális hálózati társviszony-létesítés. Kattintson az előző eszköz hivatkozásokra kattintva közvetlenül Ugrás a virtuális hálózati társviszony-létesítés a eszközzel választott létrehozásához szükséges lépésekről.

## <a name="portal"></a>Hozzon létre a társviszony - Azure-portálon

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetéshez engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás a portálon kívül, és hagyja ki a lépéseket, a másik felhasználói engedélyek hozzárendelése a virtuális hálózatok.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , "a" felhasználó. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
2. Kattintson a **+ új**, kattintson a **hálózati**, majd kattintson a **virtuális hálózati**.
3. Az a **virtuális hálózat létrehozása** panelen adja meg, vagy válassza ki a következő beállítások értékeit, majd kattintson **létrehozása**:
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.0.0.0/24*
    - **Előfizetés**: válassza ki az előfizetést azonosítójához.
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroupA*
    - **Hely**: *USA keleti régiója*
4. Az a **keresési erőforrások** típus a portál felső részén *myVnetA*. Kattintson a **myVnetA** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myVnetA** virtuális hálózat.
5. Az a **myVnetA** panel, amelyen megjelenik, kattintson a **hozzáférés-vezérlés (IAM)** a függőleges a panel bal oldalán lehetőségek közül.
6. Az a **myVnetA - hozzáférés-vezérlés (IAM)** panel, amelyen megjelenik, kattintson a **+ Hozzáadás**.
7. Az a **engedélyek hozzáadása** panelen megjelenő, válassza ki **hálózat közreműködő** a a **szerepkör** mezőbe.
8. Az a **válasszon** mezőben válassza ki a "b" felhasználó, vagy írja be a "b" felhasználó tartozó e-mail cím kereséséhez. A megjelenített felhasználók listáját a azonos Azure Active Directory-bérlő és a virtuális hálózata hoz létre a társviszony származik. Amikor megjelenik a listában, kattintson a "b" felhasználó.
9. Kattintson a **Save** (Mentés) gombra.
10. "A" felhasználó, a portál kijelentkezés, majd jelentkezzen be "b" felhasználó.
11. Kattintson a **+ új**, típus *virtuális hálózati* a a **keresése a piactéren** mezőbe, majd kattintson az **virtuális hálózati** a keresési eredmények között.
12. Az a **virtuális hálózati** panelen megjelenő, válassza ki **klasszikus** a a **telepítési modell kiválasztása** mezőbe, majd kattintson az **létrehozása**.
13.   Hozzon létre virtuális hálózat (klasszikus) mezőbe adja meg a következő értékeket:

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.1.0.0/24*
    - **Előfizetés**: válassza ki az előfizetést a b kiszolgálóra.
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroupB*
    - **Hely**: *USA keleti régiója*

14. Az a **keresési erőforrások** típus a portál felső részén *myVnetB*. Kattintson a **myVnetB** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myVnetB** virtuális hálózat.
15. Az a **myVnetB** panel, amelyen megjelenik, kattintson a **tulajdonságok** a függőleges a panel bal oldalán lehetőségek közül. Másolás a **erőforrás-azonosító**, amellyel egy későbbi lépésben. Az erőforrás-azonosító a következőhöz hasonló: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Végezze el myVnetB, írja be 5 – 9 lépéseket **"a" felhasználó** a 8. lépés.
17. Jelentkezzen ki, "b" felhasználó a portálon, és jelentkezzen be "a" felhasználó.
18. Az a **keresési erőforrások** típus a portál felső részén *myVnetA*. Kattintson a **myVnetA** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myVnet** virtuális hálózat.
19. Kattintson a **myVnetA**.
20. Az a **myVnetA** panel, amelyen megjelenik, kattintson a **Társviszony** a függőleges a panel bal oldalán lehetőségek közül.
21. Az a **myVnetA - esetében** panel, amelyen jelent meg, kattintson a **+ Hozzáadás**
22. Az a **Hozzáadás társviszony-létesítés** panelen megjelenő, adja meg, vagy válassza a következő beállításokat, majd kattintson **OK**:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modell**: válasszon **klasszikus**.
     - **Tudható, hogy az erőforrás-azonosító**: ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: Adja meg az erőforrás-azonosítója, myVnetB a 15. lépéssel.
     - **Virtuális hálózati hozzáférés engedélyezése:** ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállítások használhatók. Minden társviszony-létesítési beállításaival kapcsolatos további tudnivalókért olvassa el a [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md#create-a-peering).
23. Kattintás után **OK** az előző lépésben a **Hozzáadás társviszony-létesítés** panel bezárása után, és megjelenik a **myVnetA - Társviszony** újra a panelt. Néhány másodpercen belül a társviszony-létesítés létrehozott jelenik meg a panelt. **Csatlakoztatott** szerepel a **társviszony-LÉTESÍTÉS állapot** oszlopában a **myVnetAToMyVnetB** társviszony-létesítést, létre. A társviszony-létesítést most létrejön. Nincs szükség az egyenrangú a virtuális hálózat (klasszikus) a virtuális hálózathoz (Resource Manager).

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

24. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
25. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure parancssori felület

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Ha mindkét előfizetéshez engedéllyel rendelkező fiók használata esetén használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket az Azure-ból a naplózás és eltávolítása a parancssor által létrehozott felhasználói szerepkör-hozzárendelések. Cserélje le UserA@azure.com és UserB@azure.com összes, az alábbi parancsfájlok az "a" felhasználó és a "b" felhasználó használata a felhasználónevek. 

1. [Telepítés](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) az Azure CLI 1.0 a virtuális hálózat (klasszikus) létrehozásához.
2. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-bA "b" felhasználó használja a `azure login` parancsot.
3. A parancssori felület szolgáltatásfelügyelet módban fusson írja be a `azure config mode asm` parancsot.
4. Adja meg a virtuális hálózat (klasszikus) létrehozása a következő parancsot:
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. A további lépéseket kell elvégezni az Azure parancssori felülettel 2.0.4 héjastól vagy későbbi a bash használatával [telepített](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), vagy az Azure-felhő rendszerhéj használatával. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Kattintson a **kipróbálás** a parancsfájlok olvashat, amely egy, az Azure-fiókjával jelentkezik a felhő rendszerhéj megnyitása gombra. Fut a beállítások bash parancssori felület parancsfájlok egy Windows-ügyfélen, a következő témakörben: [futtatása az Azure parancssori felület a Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen. Cserélje le `<SubscriptionB-Id>` az előfizetés-azonosítóval. Ha az előfizetés-azonosítója nem ismeri, adja meg a `az account show` parancsot. A következő **azonosító** kimenet az előfizetés azonosítóját. Másolja a módosított, illessze be a parancssori felület 2.0-munkamenethez, és nyomja le az `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Ha a virtuális hálózat (klasszikus), a 4. lépésben létrehozott, Azure létrehozott-e a virtuális hálózatot a a *alapértelmezett-hálózat* erőforráscsoportot.
7. "B" felhasználó Azure és a bejelentkezés jelentkezzen "a" felhasználó a CLI 2.0-s verziójában.
8. Hozzon létre egy erőforráscsoportot és egy virtuális hálózatot (Resource Manager). Másolja a következő, illessze be a CLI-munkamenethez, és nyomja le az `Enter`. 

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

9. Hozzon létre egy virtuális hálózati társviszony-létesítés keresztül a különböző üzembe helyezési modellel létrehozott két virtuális hálózatok között. Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen. Cserélje le `<SubscriptionB-id>` az előfizetés azonosítóját. Ha az előfizetés-azonosítója nem ismeri, adja meg a `az account show` parancsot. A következő **azonosító** kimenet az előfizetés azonosítóját. Azure létrehozni a virtuális hálózat (klasszikus), az első lépésben létrehozott 4 erőforráscsoportban nevű *alapértelmezett-hálózat*. Illessze be a módosított parancsfájlt a CLI-munkamenetben, és nyomja le az `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Miután a parancsfájl végrehajtása során, tekintse át a társviszony-létesítést a virtuális hálózat (Resource Manager). Másolja a következő, majd illessze be a CLI-munkamenetben:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    A kimenet mutatja **csatlakoztatva** a a **PeeringState** oszlop.

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

11. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
12. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-cli) ebben a cikkben.

## <a name="powershell"></a>Hozzon létre a társviszony - PowerShell

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Ha mindkét előfizetéshez engedéllyel rendelkező fiók használata esetén használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket az Azure-ból a naplózás és eltávolítása a parancssor által létrehozott felhasználói szerepkör-hozzárendelések. Cserélje le UserA@azure.com és UserB@azure.com összes, az alábbi parancsfájlok az "a" felhasználó és a "b" felhasználó használata a felhasználónevek. 

1. Telepítse a legújabb verzióját a PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) és [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulok. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítson el egy PowerShell-munkamenetet.
3. A PowerShellben bejelentkezni "b" felhasználó "b" felhasználó tartozó előfizetés írja be a `Add-AzureAccount` parancsot.
4. Virtuális hálózat (klasszikus) létrehozása a PowerShell segítségével, hozzon létre egy új, vagy módosítsa egy meglévő, hálózati konfigurációs fájlt. Megtudhatja, hogyan [exportálása, frissítése és a hálózati konfigurációs fájlok importálása a](virtual-networks-using-network-configuration-file.md). A fájl tartalmaznia kell a következő **VirtualNetworkSite** elem ebben az oktatóanyagban használt virtuális hálózat:

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
    > Megváltozott hálózati konfigurációs fájlok importálása (klasszikus) az előfizetéshez létező virtuális hálózatok módosításai okozhatják. Csak adja hozzá a korábbi virtuális hálózatot, és módosítsa vagy távolítsa el a meglévő virtuális hálózatok az előfizetésből nem biztosítására. 

5. Jelentkezzen be a "b" felhasználó tartozó előfizetés Resource Manager-parancsok használatával írja be a "b" felhasználó a `login-azurermaccount` parancsot.
6. "A" felhasználó engedélyek hozzárendelése virtuális hálózati b másolása a következő parancsfájl egy szövegszerkesztőben a Számítógépről, majd cserélje le a `<SubscriptionB-id>` előfizetés b azonosítójú Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` parancs a megtekintéséhez. A következő **azonosító** visszaadott kimenet van az előfizetés-azonosító. Azure létrehozni a virtuális hálózat (klasszikus), az első lépésben létrehozott 4 erőforráscsoportban nevű *alapértelmezett-hálózat*. A parancsfájl végrehajtása, másolja a módosított, illessze be a PowerShell, és nyomja le az `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Jelentkezzen ki az Azure-bA biztosít, és írja be a "a" felhasználó "a" felhasználó tartozó előfizetés bejelentkezni a `login-azurermaccount` parancsot. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Tekintse meg a [engedélyek](#permissions) jelen cikkben alább szakasza.
8. A virtuális hálózat (erőforrás-kezelő) létrehozása a következő parancsfájl másolása, a PowerShell a illeszti, és nyomja `Enter`:

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

9. "B" felhasználó engedélyek hozzárendelése a myVnetA. Másolja a következő parancsfájl egy szövegszerkesztőben a Számítógépről, majd cserélje le a `<SubscriptionA-Id>` azonosítójú előfizetés A., Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` parancs a megtekintéséhez. A következő **azonosító** visszaadott kimenet van az előfizetés-azonosító. Illessze be a parancsfájl módosított változatát PowerShell, és nyomja le az `Enter` végrehajtásra.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Másolja a következő parancsfájlt a számítógépen egy szövegszerkesztőben, és cserélje le `<SubscriptionB-id>` előfizetés b azonosítójú A myVNetB myVnetA egyenrangú, másolja a módosított, illessze be a PowerShell és nyomja le az `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. A következő parancsfájl másolása, illeszti be PowerShell, majd nyomja le az myVnetA társviszony-létesítési állapotának megtekintése `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **csatlakoztatva**. Módosítja **csatlakoztatva** a társviszony-létesítést úgy myVnetA myVnetB a beállítása után.

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

12. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
13. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-powershell) ebben a cikkben.

## <a name="permissions"></a>Engedélyek

A fiókokat hozhat létre a virtuális hálózati társviszony-létesítés a szükséges szerepkör vagy az engedélyekkel kell rendelkeznie. Például, ha két virtuális hálózatok myVnetA és myVnetB nevű volt társviszony, a fiókjához társítva kell lenni a következő minimális szerepkör vagy az engedélyekkel minden virtuális hálózathoz:
    
|Virtuális hálózat|Üzemi modell|Szerepkör|Engedélyek|
|---|---|---|---|
|myVnetA|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|–|
|myVnetB|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

További információ [beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és rendelje hozzá adott engedélyeket [egyéni szerepkörök](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (csak Resource Manager).

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után, előfordulhat, hogy törölni kívánja az erőforrások létrehozta az oktatóanyagban, használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. A portál keresési mezőbe, írja be a **myResourceGroupA**. A keresési eredmények között kattintson **myResourceGroupA**.
2. Az a **myResourceGroupA** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroupA**, és kattintson a **törlése**.
4. Az a **keresési erőforrások** típus a portál felső részén *myVnetB*. Kattintson a **myVnetB** amikor megjelenik a keresési eredmények között. A panel jelenik meg a **myVnetB** virtuális hálózat.
5. Az a **myVnetB** panelen kattintson a **törlése**.
6. A törlés megerősítéséhez kattintson **Igen** a a **virtuális hálózati Delete** mezőbe.

### <a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be Azure-ban a parancssori felület 2.0 törölni a virtuális hálózatot (erőforrás-kezelő) a következő paranccsal:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Jelentkezzen be Azure-ban az Azure CLI 1.0 törölni a virtuális hálózat (klasszikus) az alábbi parancsokkal:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. A PowerShell parancssorba írja be a következő parancs futtatásával törölje a virtuális hálózatot (erőforrás-kezelő):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. A virtuális hálózat törlése (klasszikus), a PowerShell-lel, módosítania kell egy meglévő hálózati konfigurációs fájlt. Megtudhatja, hogyan [exportálása, frissítése és a hálózati konfigurációs fájlok importálása a](virtual-networks-using-network-configuration-file.md). Távolítsa el a következő VirtualNetworkSite elem ebben az oktatóanyagban használt virtuális hálózat:

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
    > Megváltozott hálózati konfigurációs fájlok importálása (klasszikus) az előfizetéshez létező virtuális hálózatok módosításai okozhatják. Győződjön meg arról, csak távolítsa el a korábbi virtuális hálózatot, és nem módosítja, illetve bármely más meglévő virtuális hálózatot eltávolítása az előfizetésből. 

## <a name="next-steps"></a>További lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátozások és viselkedéshez](virtual-network-manage-peering.md#requirements-and-constraints) társviszony-létesítés üzemi virtuális hálózat létrehozása előtt használja.
- További tudnivalók az összes [virtuális hálózati társviszony-létesítési beállítások](virtual-network-manage-peering.md#create-a-peering).
- Megtudhatja, hogyan [létrehoz egy központot, és a hálózati topológia irány](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózati társviszony-létesítés.
