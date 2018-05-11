---
title: Hozzon létre egy Azure virtuális hálózatra különböző előfizetések társviszony - Resource Manager - |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy virtuális hálózati társviszony-létesítés Resource Manager használatával létrehozott virtuális hálózatok, meglévő Azure-előfizetések között.
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 7c512e0f54cbf8a99c9a1650d0c612333f199ce0
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Hozzon létre egy virtuális hálózati társviszony - erőforrás-kezelő különböző előfizetésekhez 

Ebben az oktatóanyagban elsajátíthatja társviszony-létesítés Resource Manager használatával létrehozott virtuális hálózatok közötti virtuális hálózat létrehozása. A virtuális hálózatok léteznek a különböző előfizetésekhez. A különböző virtuális hálózatokon kommunikálni egymással sávszélesség és a késés, mintha az erőforrások volt az azonos virtuális hálózatban lévő két virtuális hálózatok lehetővé teszi, hogy erőforrásokat társviszony-létesítés. További információ [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md). 

Virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok ugyanazon, vagy másik, előfizetések, és amely [Azure telepítési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a virtuális hálózatok használatával jönnek létre. Megtudhatja, hogyan hozzon létre egy virtuális hálózatot, más esetekben a következő táblázat a forgatókönyv kiválasztásával társviszony:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét erőforrás-kezelő](tutorial-connect-virtual-networks-portal.md) |Azonos|
|[Egy erőforrás-kezelő egy klasszikus](create-peering-different-deployment-models.md) |Azonos|
|[Egy erőforrás-kezelő egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Eltérő|

Virtuális hálózati társviszony-létesítés nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózatok között. Ha mindkét létrehozott virtuális hálózatok csatlakozhat a klasszikus üzembe helyezési modellben van szüksége, használhatja az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális hálózathoz csatlakozni.

Ez az oktatóanyag állomásokhoz virtuális hálózatok ugyanabban a régióban. Virtuális hálózatok, a másik partnert is meg is [támogató régiók](virtual-network-manage-peering.md#cross-region). Javasoljuk, hogy Ön feltérképezése a [társviszony-létesítési követelményeket és korlátokat](virtual-network-manage-peering.md#requirements-and-constraints) előtt a virtuális hálózatok társviszony.

Használhatja a [Azure-portálon](#portal), az Azure [parancssori felület](#cli) (CLI), Azure [PowerShell](#powershell), vagy egy [Azure Resource Manager sablon](#template)létrehozni a virtuális hálózati társviszony-létesítés. Válassza ki az előző eszköz hivatkozásokra kattintva közvetlenül Ugrás a virtuális hálózati társviszony-létesítés a eszközzel választott létrehozásához szükséges lépésekről.

## <a name="portal"></a>Hozzon létre a társviszony - Azure-portálon

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetéshez engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás a portálon kívül, és hagyja ki a lépéseket, a másik felhasználói engedélyek hozzárendelése a virtuális hálózatok.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , *"a" felhasználó*. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Az engedélyek listájáért lásd: [virtuális hálózati társviszony-létesítési engedélyek](virtual-network-manage-peering.md#permissions).
2. Válassza ki **+ hozzon létre egy erőforrást**, jelölje be **hálózati**, majd válassza ki **virtuális hálózati**.
3. Válasszon ki vagy adja meg a következő példában szereplő értékeket a következő beállításokat, majd válassza ki **létrehozása**:
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.0.0.0/24*
    - **Előfizetés**: válassza ki az előfizetést azonosítójához.
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroupA*
    - **Hely**: *USA keleti régiója*
4. Az a **keresési erőforrások** típus a portál felső részén *myVnetA*. Válassza ki **myVnetA** amikor megjelenik a keresési eredmények között. 
5. Válassza ki **hozzáférés-vezérlés (IAM)** listából a függőleges bal oldalán.
6. A **myVnetA - hozzáférés-vezérlés (IAM)**, jelölje be **+ Hozzáadás**.
7. Válassza ki **hálózat közreműködő** a a **szerepkör** mezőbe.
8. Az a **válasszon** jelölje ki *"b" felhasználó*, vagy írja be a "b" felhasználó tartozó e-mail cím kereséséhez. A megjelenített felhasználók listáját a azonos Azure Active Directory-bérlő és a virtuális hálózata hoz létre a társviszony származik. Ha a "b" felhasználó nem látható, valószínű, mert a "b" felhasználó "a" felhasználó mint egy másik Active Directory-bérlő. Ha szeretné csatlakoztatni a virtuális hálózatok, a másik Active Directory-bérlő, összekapcsolhatja őket egy [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), ahelyett, hogy a virtuális hálózati társviszony-létesítés.
9. Kattintson a **Mentés** gombra.
10. A **myVnetA - hozzáférés-vezérlés (IAM)**, jelölje be **tulajdonságok** listából a függőleges bal oldalán. Másolás a **erőforrás-azonosító**, amellyel egy későbbi lépésben. Az erőforrás-azonosító a következőhöz hasonló: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. "A" felhasználó, a portál kijelentkezés, majd jelentkezzen be "b" felhasználó.
12. 2-3, megadásával vagy a 3. lépésben a következő értékek kiválasztásával lépéseket:

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózati név**: *alapértelmezett*
    - **Alhálózati címtartományt**: *10.1.0.0/24*
    - **Előfizetés**: válassza ki az előfizetést a b kiszolgálóra.
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroupB*
    - **Hely**: *USA keleti régiója*

13. Az a **keresési erőforrások** típus a portál felső részén *myVnetB*. Válassza ki **myVnetB** amikor megjelenik a keresési eredmények között.
14. A **myVnetB**, jelölje be **tulajdonságok** listából a függőleges bal oldalán. Másolás a **erőforrás-azonosító**, amellyel egy későbbi lépésben. Az erőforrás-azonosító a következőhöz hasonló: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Válassza ki **hozzáférés-vezérlés (IAM)** alatt **myVnetB**, majd hajtsa végre a lépéseket 5-10 myVnetB, írja be a **"a" felhasználó** a 8. lépés.
16. Jelentkezzen ki, "b" felhasználó a portálon, és jelentkezzen be "a" felhasználó.
17. Az a **keresési erőforrások** típus a portál felső részén *myVnetA*. Válassza ki **myVnetA** amikor megjelenik a keresési eredmények között.
18. Válassza ki **myVnetA**.
19. A **beállítások**, jelölje be **Társviszony**.
20. A **myVnetA - esetében**, jelölje be **+ Hozzáadás**
21. A **Hozzáadás társviszony-létesítés**, adja meg, vagy válassza ki, a következő beállításokat, majd válassza ki **OK**:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modell**: válasszon **erőforrás-kezelő**.
     - **Tudható, hogy az erőforrás-azonosító**: ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: 14 lépésben adja meg az erőforrás-azonosítója.
     - **Virtuális hálózati hozzáférés engedélyezése:** ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállítások használhatók. Minden társviszony-létesítési beállításaival kapcsolatos további tudnivalókért olvassa el a [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md#create-a-peering).
22. A társviszony-létesítés létrehozott jelenik meg egy rövid várakozási kijelölése után **OK** az előző lépésben. **Kezdeményezett** szerepel a **társviszony-LÉTESÍTÉS állapot** oszlopában a **myVnetAToMyVnetB** társviszony-létesítés meg létrehozni. Már nincsenek társviszonyban myVnetA myVnetB számára, de most a myVnetA myVnetB kell partnert. A társviszony-létesítést ahhoz, hogy a virtuális hálózatok kommunikálnak egymással erőforrások mindkét irányban kell létrehozni.
23. Jelentkezzen ki, "a" felhasználó a portálon, és jelentkezzen be "b" felhasználó.
24. Végezze el újra az myVnetB 17-21 lépéseket. A lépés 21, adja meg a társviszony-létesítést nevét *myVnetBToMyVnetA*, jelölje be *myVnetA* a **virtuális hálózati**, és adjon meg az azonosítója a 10. lépés a **erőforrás-azonosító** mezőbe.
25. Néhány másodpercen belül kijelölése után **OK** myVnetB, a társviszony létrehozásához a **myVnetBToMyVnetA** társviszony-létesítést az imént létrehozott szerepel **csatlakoztatva** a a**Társviszony-LÉTESÍTÉS állapot** oszlop.
26. Jelentkezzen ki, "b" felhasználó a portálon, és jelentkezzen be "a" felhasználó.
27. Végezze el újra a 17-19 lépéseket. A **társviszony-LÉTESÍTÉS állapot** a a **myVnetAToVNetB** társviszony-létesítés már is **csatlakoztatva**. A társviszony-létesítést sikeresen létrejött, miután látta, **csatlakoztatva** a a **társviszony-LÉTESÍTÉS állapot** oszlop társviszony-létesítés mindkét virtuális hálózat számára. Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
29. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure parancssori felület

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Ha mindkét előfizetéshez engedéllyel rendelkező fiók használata esetén használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket az Azure-ból a naplózás és eltávolítása a parancssor által létrehozott felhasználói szerepkör-hozzárendelések. Cserélje le UserA@azure.com és UserB@azure.com összes, az alábbi parancsfájlok az "a" felhasználó és a "b" felhasználó használata a felhasználónevek. A virtuális hálózatok partnert kívánt kell lennie a azonos Azure Active Directory-bérlőhöz társított előfizetésekhez.  Ha szeretné csatlakoztatni a virtuális hálózatok, a másik Active Directory-bérlő, összekapcsolhatja őket egy [Azure VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), ahelyett, hogy a virtuális hálózati társviszony-létesítés.

Az alábbi parancsfájlok:

- Van szükség az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- A rendszerhéjakba működik. Az Azure CLI-szkriptek Windows-ügyfeleken való futtatásával kapcsolatos lehetőségekkel kapcsolatban lásd az [Azure CLI Windowsban történő futtatásával](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json) foglalkozó témakört. 

A parancssori felület és függőségeinek telepítése, helyett az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki a **kipróbálás** gombra a parancsfájl alábbi, amely meghívja a felhő rendszerhéjat, amely bejelentkezhet az Azure-fiókjába. 

1. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-bA "a" felhasználó használja a `azure login` parancsot. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Az engedélyek listájáért lásd: [virtuális hálózati társviszony-létesítési engedélyek](virtual-network-manage-peering.md#permissions).
2. Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen, hogy lecseréli `<SubscriptionA-Id>` rendelkező SubscriptionA azonosítója, majd másolja a módosított parancsfájlt, majd illessze be a parancssori munkamenetet, és nyomja le az `Enter`. Ha az előfizetés-azonosítója nem ismeri, adja meg a "az fiók megjelenítése" parancsot. A következő **azonosító** kimenet az előfizetés azonosítóját.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. Jelentkezzen az Azure-ból "a" felhasználó használja a `az logout` parancsot, majd jelentkezzen be Azure-bA "b" felhasználó. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Az engedélyek listájáért lásd: [virtuális hálózati társviszony-létesítési engedélyek](virtual-network-manage-peering.md#permissions).
4. Hozzon létre myVnetB. Másolja a parancsfájl tartalmát a 2. lépésben a számítógépen egy szövegszerkesztőben. Cserélje le `<SubscriptionA-Id>` SubscriptionB azonosítójú. Változás 10.0.0.0/16 10.1.0.0/16, módosítsa az összes, a B és a módosított parancsfájl A. másolandó összes Bs illessze be a parancssori munkamenetet, és nyomja le az `Enter`. 
5. Jelentkezzen ki az Azure-bA biztosít, és jelentkezzen be Azure-bA "a" felhasználó.
6. Hozzon létre egy virtuális hálózati társviszony-létesítést az myVnetA úgy myVnetB. Másolja a következő parancsfájl tartalmát a számítógépen egy szövegszerkesztőben. Cserélje le `<SubscriptionB-Id>` SubscriptionB azonosítójú. Futtassa a parancsfájlt, másolja a módosított, illessze be a parancssori munkamenetet, és nyomja le az Enter.
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. MyVnetA társviszony-létesítési állapotának megtekintése.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Az állapot **kezdeményezett**. Módosítja **csatlakoztatva** a társviszony-létesítést úgy myVnetA myVnetB a létrehozása után.

8. Jelentkezzen ki "a" felhasználó az Azure-ból, és jelentkezzen be Azure-bA "b" felhasználó.
9. A társviszony-létesítést az myVnetB úgy myVnetA létrehozása 6. lépés a parancsfájl-tartalom másolása a számítógépen egy szövegszerkesztőben. Cserélje le `<SubscriptionB-Id>` azonosítójú SubscriptionA és módosítása az összes, a B és az összes Bs felé. A módosításokat hajtott végre, miután a módosított parancsfájl másolása, illessze be a parancssori munkamenetet, és nyomja le az `Enter`.
10. MyVnetB társviszony-létesítési állapotának megtekintése. 7. lépés a parancsfájl-tartalom másolása a számítógépen egy szövegszerkesztőben. Módosítása A B az erőforráscsoportot és a virtuális hálózat nevét, a másolja a parancsfájlt, illessze be a módosított parancsfájlt a CLI-munkamenethez, és nyomja le az `Enter`. A társviszony-létesítési állapot **csatlakoztatva**. MyVnetA társviszony-létesítési állapota megváltozik a **csatlakoztatva** a társviszony-létesítést az myVnetB myVnetA a létrehozása után. "A" felhasználó bejelentkezhet vissza az Azure-bA és a teljes 7. lépés myVnetA társviszony-létesítési állapotának ellenőrzéséhez az újra. 

    > [!NOTE]
    > A társviszony-létesítést nincs létrehozva, amíg a társviszony-létesítési állapot **csatlakoztatva** mindkét virtuális hálózat számára.

11. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
12. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-cli) ebben a cikkben.

Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Hozzon létre a társviszony - PowerShell

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Ha mindkét előfizetéshez engedéllyel rendelkező fiók használata esetén használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket az Azure-ból a naplózás és eltávolítása a parancssor által létrehozott felhasználói szerepkör-hozzárendelések. Cserélje le UserA@azure.com és UserB@azure.com összes, az alábbi parancsfájlok az "a" felhasználó és a "b" felhasználó használata a felhasználónevek. A virtuális hálózatok partnert kívánt kell lennie a azonos Azure Active Directory-bérlőhöz társított előfizetésekhez.  Ha szeretné csatlakoztatni a virtuális hálózatok, a másik Active Directory-bérlő, összekapcsolhatja őket egy [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md), ahelyett, hogy a virtuális hálózati társviszony-létesítés.

1. Telepítse a PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduljának legújabb verzióját. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Indítson el egy PowerShell-munkamenetet.
3. A PowerShellben, jelentkezzen be az Azure-bA "a" felhasználó írja be a `Connect-AzureRmAccount` parancsot. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Az engedélyek listájáért lásd: [virtuális hálózati társviszony-létesítési engedélyek](virtual-network-manage-peering.md#permissions).
4. Hozzon létre egy erőforráscsoportot és a virtuális hálózat A. másolása a következő parancsfájl egy szövegszerkesztőben a számítógépen. Cserélje le `<SubscriptionA-Id>` SubscriptionA azonosítójú. Ha az előfizetés-azonosítója nem ismeri, adja meg a `Get-AzureRmSubscription` parancs a megtekintéséhez. A következő **azonosító** visszaadott kimenet van az előfizetés-azonosító. A parancsfájl végrehajtása, másolja a módosított, illessze be a PowerShell, és nyomja le az `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Jelentkezzen ki "a" felhasználó az Azure-ból, és jelentkezzen be a "b" felhasználó. A fiókkal jelentkezik be az virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel kell rendelkeznie. Az engedélyek listájáért lásd: [virtuális hálózati társviszony-létesítési engedélyek](virtual-network-manage-peering.md#permissions).
6. Másolja a parancsfájl tartalmát a 4. lépésben a számítógépen egy szövegszerkesztőben. Cserélje le `<SubscriptionA-Id>` b módosítás 10.0.0.0/16 10.1.0.0/16 való előfizetés azonosítóval. Módosítsa az összes, a B és az összes Bs felé. A parancsfájl végrehajtása, másolja a módosított, illessze be a PowerShell, és nyomja le az `Enter`.
7. Jelentkezzen ki a "b" felhasználó az Azure-ból, és jelentkezzen be a "a" felhasználó.
8. A társviszony-létesítést az myVnetA úgy myVnetB létrehozása Másolja a következő parancsfájl egy szövegszerkesztőben, a számítógépen. Cserélje le `<SubscriptionB-Id>` előfizetés b azonosítójú A parancsfájl végrehajtása, másolja a módosított, illessze be a PowerShell, és nyomja le az `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. MyVnetA társviszony-létesítési állapotának megtekintése.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **kezdeményezett**. Módosítja **csatlakoztatva** a társviszony-létesítést úgy myVnetA myVnetB a beállítása után.

10. Jelentkezzen ki "a" felhasználó az Azure-ból, és jelentkezzen be a "b" felhasználó.
11. A társviszony-létesítést az myVnetB úgy myVnetA létrehozása 8. lépés a parancsfájl-tartalom másolása a számítógépen egy szövegszerkesztőben. Cserélje le `<SubscriptionB-Id>` , azonosító: A előfizetés, és módosítsa az összes, a B és az összes Bs felé. A parancsfájl végrehajtása, másolja a módosított, illessze be a PowerShell, és nyomja le az `Enter`.
12. MyVnetB társviszony-létesítési állapotának megtekintése. 9. lépés a parancsfájl-tartalom másolása a számítógépen egy szövegszerkesztőben. Az erőforráscsoport és a virtuális hálózati nevek a b kiszolgálóra A módosítása. A parancsfájl végrehajtása PowerShell illessze be a módosított parancsfájl, és nyomja le az `Enter`. Az állapot **csatlakoztatva**. A társviszony-létesítési állapotának **myVnetA** vált **csatlakoztatva** a társviszony-létesítést a létrehozása után **myVnetB** való **myVnetA**. "A" felhasználó bejelentkezhet vissza az Azure-bA és a teljes 9. lépés myVnetA társviszony-létesítési állapotának ellenőrzéséhez az újra. 

    > [!NOTE]
    > A társviszony-létesítést nincs létrehozva, amíg a társviszony-létesítési állapot **csatlakoztatva** mindkét virtuális hálózat számára.

    Bármely Azure-hoz létre vagy virtuális hálózati erőforrások is az IP-címek keresztül kommunikálnak egymással. Alapértelmezett Azure névfeloldást használ a virtuális hálózatok, a virtuális hálózatok erőforrások esetén nem tudják feloldani a virtuális hálózatok közötti. Ha szeretné feloldani egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állíthat be [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
14. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez végrehajtásához a [törli az erőforrást](#delete-powershell) ebben a cikkben.

## <a name="template"></a>Hozzon létre a társviszony - Resource Manager-sablon

1. Hozzon létre egy virtuális hálózatot, és rendelje hozzá a megfelelő [engedélyek](virtual-network-manage-peering.md#permissions), hajtsa végre a a [Portal](#portal), [Azure CLI](#cli), vagy [PowerShell](#powershell)a cikk szakaszaiban.
2. Mentse a fájlt a helyi számítógépen utáni szöveget. Cserélje le `<subscription ID>` az "a" felhasználó tartozó előfizetés-azonosító. Előfordulhat, hogy menti a fájlt vnetpeeringA.json, mint például.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
    }
    ```

3. Jelentkezzen be Azure-bA "a" felhasználó és a sablon használatával telepítheti a [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), vagy a [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Adja meg a fájlnevet, például a json-szövegben 2. lépésben mentett.
4. Másolja a példa json a 2. lépésben a fájl a számítógépen, és módosítja a kezdődő sorok:
    - **név**: módosítás *myVnetA/myVnetAToMyVnetB* való *myVnetB/myVnetBToMyVnetA*.
    - **azonosító**: cserélje le `<subscription ID>` "b" felhasználó tartozó előfizetés-azonosító, és módosítsa a *myVnetB* való *myVnetA*.
5. A Kész lépés 3 újra, rendszergazdaként az Azure biztosít.
6. **Nem kötelező**: abban az esetben, ha ez az oktatóanyag nem vonatkozik a virtuális gépek létrehozását, hozzon létre egy virtuális gép minden egyes virtuális hálózati, és csatlakoztassa egy virtuális gépről a másikra, való kapcsolat ellenőrzésére.
7. **Nem kötelező**: az erőforrásokat, amelyek ebben az oktatóanyagban létrehozhat törléséhez lépéseinek végrehajtásához a [törli az erőforrást](#delete) című szakaszát, az Azure-portálon, a PowerShell vagy az Azure parancssori felület használatával.

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után, előfordulhat, hogy törölni kívánja az erőforrások létrehozta az oktatóanyagban, használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. Jelentkezzen be az Azure-portálon "a" felhasználó.
2. A portál keresési mezőbe, írja be a **myResourceGroupA**. A keresési eredmények között, válassza ki a **myResourceGroupA**.
3. Válassza a **Törlés** elemet.
4. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroupA**, majd válassza ki **törlése**.
5. Jelentkezzen ki, "a" felhasználó a portálon, és jelentkezzen be "b" felhasználó.
6. Végezze el myResourceGroupB 2 – 4 lépéseket.

### <a name="delete-cli"></a>Az Azure parancssori felület

1. Jelentkezzen be Azure-bA "a" felhasználó, és hajtsa végre a következő parancsot:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Jelentkezzen ki az Azure-bA "a" felhasználó, és jelentkezzen be, mint a "b" felhasználó.
3. Hajtsa végre a következő parancsot:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Jelentkezzen be Azure-bA "a" felhasználó, és hajtsa végre a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Jelentkezzen ki az Azure-bA "a" felhasználó, és jelentkezzen be, mint a "b" felhasználó.
3. Hajtsa végre a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>További lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátozások és viselkedéshez](virtual-network-manage-peering.md#requirements-and-constraints) társviszony-létesítés üzemi virtuális hálózat létrehozása előtt használja.
- További tudnivalók az összes [virtuális hálózati társviszony-létesítési beállítások](virtual-network-manage-peering.md#create-a-peering).
- Megtudhatja, hogyan [létrehoz egy központot, és a hálózati topológia irány](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózati társviszony-létesítés.
