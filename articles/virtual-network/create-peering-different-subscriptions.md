---
title: Egy Azure virtuális hálózat létrehozása – Resource Manager - társviszony-létesítés eltérő előfizetésekben
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre egy virtuális hálózatot a Resource Managerben létrehozott, amely a különböző Azure-előfizetésekben található virtuális hálózatok közötti társviszony.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: f06f0b5392ebb60cd852d3c2eb201478b31ae167
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014966"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Egy virtuális hálózati társviszony-létesítés létrehozása – Resource Manager, a különböző előfizetések

Ebben az oktatóanyagban elsajátíthatja a virtuális hálózat a Resource Manager modellel létrehozott virtuális hálózatok közötti társviszony létrehozása. A virtuális hálózatok különböző előfizetésekben található. Társviszony-létesítés virtuális hálózatok különböző virtuális hálózatokban kommunikálni egymással azonos sávszélesség és késés, mintha az erőforrásokat is ugyanabban a virtuális hálózatban lévő két lehetővé teszi, hogy erőforrásokat. Tudjon meg többet [virtuális hálózatok közötti társviszony](virtual-network-peering-overview.md).

A virtuális hálózatok közötti társviszony létrehozásához szükséges lépéseket, attól függően különböznek egymástól vannak-e a virtuális hálózatok a azonos vagy eltérő előfizetések és amely [Azure üzembehelyezési modell](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) jönnek létre a virtuális hálózatokon keresztül. Ismerje meg, hogyan hozhat létre virtuális hálózati társviszonyt más forgatókönyvek esetében válassza ki az alábbi táblázat a forgatókönyvhöz:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Azonos|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Azonos|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Eltérő|

A virtuális hálózatok közötti társviszony nem hozható létre, a klasszikus üzemi modellel üzembe helyezett két virtuális hálózat között. Ha a klasszikus üzemi modellel létrehozott, mindkét virtuális hálózatok csatlakoztatása van szüksége, használhatja az Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) össze a virtuális hálózatok.

Ebben az oktatóanyagban ugyanabban a régióban található virtuális hálózatok is társul. Emellett társviszonyt virtuális hálózatok különböző [támogatott régiók](virtual-network-manage-peering.md#cross-region). Javasoljuk, hogy Ön feltérképezése a [társviszony-létesítés követelményei és korlátozásai](virtual-network-manage-peering.md#requirements-and-constraints) előtt társviszony-létesítés virtuális hálózatok.

Használhatja a [az Azure portal](#portal), az Azure [parancssori felület](#cli) (CLI), Azure [PowerShell](#powershell), vagy egy [Azure Resource Manager-sablon](#template)egy virtuális hálózati társviszony-létesítés létrehozása. Válassza ki, hogy közvetlenül a virtuális hálózati társviszonyt a választott eszköz használatával létrehozásának menete a korábbi eszköz hivatkozások bármelyikére.

## <a name="portal"></a>Társviszony - létrehozása az Azure Portalon

Különböző Azure Active Directory-bérlőkhöz társított előfizetésekben található virtuális hálózatok társviszonyba állítása kívánt esetén kövesse a cikk a CLI és PowerShell című szakaszának lépéseit. Portál nincs támogatási eltérő Active Directory-bérlők, előfizetések régiókba tartozó virtuális hálózatok társviszonyba állítása.

Az alábbi lépéseket az egyes előfizetésekhez más fiókokat kell használni. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, a naplózáshoz a portálról a lépés kihagyható és egy másik felhasználói engedélyek hozzárendelése a virtuális hálózatok számára a lépés kihagyható.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , *Felhasználóa*. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
2. Válassza ki **+ erőforrás létrehozása**válassza **hálózatkezelés**, majd válassza ki **virtuális hálózati**.
3. Válassza ki vagy adja meg a következő példában szereplő értékeket a következő beállításokat, majd válassza ki **létrehozás**:
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.0.0.0/24*
    - **Előfizetés**: Válassza ki előfizetését rögzíti.
    - **Erőforráscsoport**: Válassza ki **új létrehozása** , és adja meg *myResourceGroupA*
    - **Hely**: *USA keleti RÉGIÓJA*
4. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetA*. Válassza ki **myVnetA** amikor megjelenik a keresési eredmények között. 
5. Válassza ki **hozzáférés-vezérlés (IAM)** a bal oldali beállítások függőleges listájából.
6. A **myVnetA - hozzáférés-vezérlés (IAM)** válassza **+ szerepkör-hozzárendelés hozzáadása**.
7. Válassza ki **hálózati közreműködő** a a **szerepkör** mezőbe.
8. Az a **kiválasztása** jelölje ki *Felhasználób*, vagy írja be a Felhasználób e-mail-címet a keresési funkciót.
9. Kattintson a **Mentés** gombra.
10. Alatt **myVnetA - hozzáférés-vezérlés (IAM)** válassza **tulajdonságok** a bal oldali beállítások függőleges listájából. Másolás a **erőforrás-azonosító**, amely egy későbbi lépésben szolgál. Az erőforrás-azonosítója a következő példához hasonlít: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Jelentkezzen ki a portálra a Felhasználóa felhasználóként, majd jelentkezzen be Felhasználób felhasználóként.
12. 2-3, megadásával vagy a 3. lépésben a következő értékeket kiválasztásával lépések végrehajtásával:

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány**: *10.1.0.0/24*
    - **Előfizetés**: B előfizetés kiválasztása
    - **Erőforráscsoport**: Válassza ki **új létrehozása** , és adja meg *myResourceGroupB*
    - **Hely**: *USA keleti RÉGIÓJA*

13. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetB*. Válassza ki **myVnetB** amikor megjelenik a keresési eredmények között.
14. Alatt **myVnetB**válassza **tulajdonságok** a bal oldali beállítások függőleges listájából. Másolás a **erőforrás-azonosító**, amely egy későbbi lépésben szolgál. Az erőforrás-azonosítója a következő példához hasonlít: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Válassza ki **hozzáférés-vezérlés (IAM)** alatt **myVnetB**, majd hajtsa végre az 5 – 10. lépéseket myVnetB, írja be a **Felhasználóa** 8. lépés.
16. Jelentkezzen ki a Felhasználób portált, és jelentkezzen be a Felhasználóa felhasználóként.
17. Az a **erőforrások keresése** mezőbe írja be a portál tetején lévő *myVnetA*. Válassza ki **myVnetA** amikor megjelenik a keresési eredmények között.
18. Válassza ki **myVnetA**.
19. A **beállítások**válassza **Társviszonyok**.
20. A **myVnetA - Társviszonyok**válassza **+ Hozzáadás**
21. A **Hozzáadás társviszony-létesítés**, adja meg, vagy válassza ki, a következő beállításokat, majd válassza a **OK**:
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat üzemelőpéldány-modellje**:  Válassza a **Resource Manager** lehetőséget.
     - **Tudom, hogy az erőforrás-Azonosítómat**: Ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító**: Adja meg az erőforrás-azonosítója a 14. lépés.
     - **Virtuális hálózati hozzáférés engedélyezése:** Ügyeljen arra, hogy **engedélyezve** van kiválasztva.
    Ebben az oktatóanyagban nincs más beállításokat használja. Minden társviszony beállításai kapcsolatos további információkért olvassa el a [kezelheti a virtuális társhálózatok](virtual-network-manage-peering.md#create-a-peering).
22. A társviszony-létesítés létrehozott jelenik meg egy rövid várakozás kiválasztása után **OK** az előző lépésben. **Kezdeményezett** szerepel-e a **társviszony-LÉTESÍTÉS állapota** oszlopában a **myVnetAToMyVnetB** társviszony-létesítés, létrehozva. MyVnetA myVnetB, akkor már társviszonyban, de most a myVnetA myVnetB kell társviszonyt. A társviszony-létesítést mindkét irányban, lehetővé teszik az erőforrások a virtuális hálózatok egymással kommunikálni kell létrehozni.
23. Jelentkezzen ki a portálra a Felhasználóa felhasználóként, és jelentkezzen be Felhasználób felhasználóként.
24. Végezze el újra a myVnetB 17-21 lépéseket. 21. lépésben, nevezze el a társviszony-létesítés *myVnetBToMyVnetA*, jelölje be *myVnetA* a **virtuális hálózati**, és adja meg az Azonosítót a 10. lépés a **erőforrás-azonosító**mezőbe.
25. Kiválasztása után néhány másodpercig **OK** hozhat létre, a társviszony-létesítést myVnetB, a **myVnetBToMyVnetA** együtt jelenik meg az imént létrehozott társviszony **csatlakoztatva** a a**Társviszony-LÉTESÍTÉS állapota** oszlop.
26. Jelentkezzen ki a Felhasználób portált, és jelentkezzen be a Felhasználóa felhasználóként.
27. Végezze el ismét a 17-19. lépést. A **társviszony-LÉTESÍTÉS állapota** számára a **myVnetAToVNetB** társviszony-létesítés jelenleg is **csatlakoztatva**. A sikeresen létrejött a társviszony, miután látta, **csatlakoztatva** a a **társviszony-LÉTESÍTÉS állapota** oszlopot mindkét a társviszony-létesítés virtuális hálózatok. Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
29. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések a [törölje az erőforrást](#delete-portal) című szakaszát.

## <a name="cli"></a>Társviszony - létrehozása az Azure CLI

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás az Azure-ból és a sorok szkript által létrehozott felhasználói szerepkör-hozzárendelések eltávolításához. Cserélje le UserA@azure.com és UserB@azure.com az alábbi parancsfájlok a Felhasználóa és Felhasználób használata a felhasználónevek mindegyikét. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, és az előfizetések társítva, a másik Azure Active Directory-bérlők, a folytatás előtt végezze el az alábbi lépéseket:
 - Adja hozzá a felhasználót, mint minden Active Directory-bérlőt egy [vendégfelhasználó](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) az ellentétes Azure Active Directory-bérlőben.
 - Minden felhasználó rendelkezik az ellentétes Azure Active Directory-bérlőhöz, a Vendég felhasználói meghívó elfogadását.

Az alábbi parancsfájlok:

- Van szükség az Azure CLI 2.0.4-es vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissíteni szeretne, olvassa el [Azure CLI telepítése](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Bash-felületen működik. Az Azure CLI-szkriptek Windows-ügyfeleken való futtatásának lehetőségeiről az [Azure CLI Windows rendszeren való telepítésével](/cli/azure/install-azure-cli-windows) foglalkozó témakörben talál további információt.

Telepítése a parancssori felület és annak függőségeit, helyett használhatja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki a **kipróbálás** gombra a szkript a következő, amely meghív egy Cloud Shell, amely az Azure-fiókjával bejelentkezhet. 

1. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-bA Felhasználóa használatával a `azure login` parancsot. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
2. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen, cserélje le `<SubscriptionA-Id>` a hozzárendelhető azonosítója, majd másolja a módosított szkripttel, illessze be a CLI-munkamenetet, majd nyomja le a `Enter`. Ha az előfizetés-azonosító nem ismeri, adja meg a "az account show" parancsot. Az érték **azonosító** kimenet az előfizetés azonosítóját.

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
    
3. Jelentkezzen ki a Azure Felhasználóa használatával a `az logout` parancsot, majd jelentkezzen be Azure-t biztosít. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
4. Hozzon létre myVnetB. A parancsfájl tartalmának másolása a 2. lépésben a számítógépen egy szövegszerkesztőbe. Cserélje le `<SubscriptionA-Id>` b azonosítójú. Változás 10.0.0.0/16 10.1.0.0/16, minden esetleges szabályozási hiányosságok elhárítását B módosítása és A. másolás a módosított szkriptet minden Bs illessze be a CLI-munkamenetet, majd nyomja le az `Enter`. 
5. Jelentkezzen ki az Azure-t biztosít, és jelentkezzen be az Azure-bA Felhasználóa felhasználóként.
6. Hozzon létre egy virtuális hálózati társviszonyt myVnetA myVnetB. Másolja a következő parancsfájl tartalmát egy szövegszerkesztőben a számítógépen. Cserélje le `<SubscriptionB-Id>` b azonosítójú. A szkript végrehajtása, másolja a módosított szkripttel, illessze be a CLI-munkamenetet, és nyomja le az Enter billentyűt.

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

7. MyVnetA társviszony állapotának megtekintéséhez.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Az állapot **kezdeményezve**. Állapotúra változik **csatlakoztatva** a társviszonyt myVnetA myVnetB a létrehozása után.

8. Jelentkezzen ki a "a" felhasználó az Azure-ból, majd jelentkezzen be Azure-t biztosít.
9. Hozzon létre a társviszonyt myVnetB myVnetA. A parancsfájl tartalmának másolása a 6. lépésben a számítógépen egy szövegszerkesztőbe. Cserélje le `<SubscriptionB-Id>` hozzárendelhető, és módosítsa az összes esetleges szabályozási hiányosságok elhárítását B és az összes Bs irányát az azonosítót Amikor végzett a módosításokat, másolja a módosított szkripttel, illessze be a CLI-munkamenetet, majd nyomja meg `Enter`.
10. MyVnetB társviszony állapotának megtekintéséhez. A parancsfájl tartalmának másolása a 7. lépésben a számítógépen egy szövegszerkesztőbe. Módosítása A-B az erőforráscsoportot és a virtuális hálózat nevét, másolja a szkriptet, illessze be a módosított szkriptet a CLI-munkamenetre, és nyomja le az `Enter`. A társviszony-létesítés állapota **csatlakoztatva**. MyVnetA társviszony-létesítés állapota megváltozik a **csatlakoztatva** a társviszonyt myVnetB myVnetA létrehozását követően. "A" felhasználó bejelentkezhet térjen vissza az Azure-ba, és teljes körű 7. lépés a társviszony-létesítés állapota myVnetA megerősítése érdekében. 

    > [!NOTE]
    > A társviszony nem jön létre, amíg a társviszony-létesítés állapota **csatlakoztatva** mindkét virtuális hálózat számára.

11. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
12. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-cli) ebben a cikkben.

Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>-Társviszony-létesítés PowerShell

Ez az oktatóanyag az egyes előfizetésekhez külön fiókot használja. Mindkét előfizetés engedéllyel rendelkező fiók használata, használja ugyanazt a fiókot az összes lépést, hagyja ki a lépéseket a naplózás az Azure-ból és a sorok szkript által létrehozott felhasználói szerepkör-hozzárendelések eltávolításához. Cserélje le UserA@azure.com és UserB@azure.com az alábbi parancsfájlok a Felhasználóa és Felhasználób használata a felhasználónevek mindegyikét. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, és az előfizetések társítva, a másik Azure Active Directory-bérlők, a folytatás előtt végezze el az alábbi lépéseket:
 - Adja hozzá a felhasználót, mint minden Active Directory-bérlőt egy [vendégfelhasználó](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) az ellentétes Azure Active Directory-bérlőben.
 - Minden felhasználó rendelkezik az ellentétes Active Directory-bérlő, a Vendég felhasználói meghívó elfogadását.

1. Győződjön meg arról, hogy 6.5.0 verzió vagy újabb verziója. Ezt megteheti futtatásával is a `Get-Module -Name AzureRm` javasoljuk, hogy a PowerShell legújabb verziójának telepítéséhez [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modul. Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Indítsa el egy PowerShell-munkamenetet.
3. A PowerShellben jelentkezzen be az Azure-bA Felhasználóa felhasználóként megadásával a `Connect-AzureRmAccount` parancsot. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
4. Hozzon létre egy erőforráscsoportot és a virtuális hálózat A. másolása a következő parancsfájl egy szövegszerkesztőbe a számítógépen. Cserélje le `<SubscriptionA-Id>` azonosítójú, hozzárendelhető. Ha az előfizetés-azonosító nem ismeri, adja meg a `Get-AzureRmSubscription` paranccsal megtekintheti azokat. Az érték **azonosító** visszaadott kimenet van az előfizetés-azonosítójára. A szkript végrehajtásához másolja a módosított szkripttel, illessze be a powershellt, majd nyomja le `Enter`.

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

5. Jelentkezzen ki az Azure-ból Felhasználóa és Felhasználób található. A bejelentkezés a következővel fiók jogosult arra, hogy a virtuális hálózati társviszony-létesítés kell rendelkeznie. Engedélyek listáját lásd: [virtuális hálózatok közötti társviszonyra vonatkozó engedélyek](virtual-network-manage-peering.md#permissions).
6. A parancsfájl tartalmának másolása a 4. lépésben a számítógépen egy szövegszerkesztőbe. Cserélje le `<SubscriptionA-Id>` azonosítójú előfizetés módosítása b 10.0.0.0/16 a 10.1.0.0/16. Módosítsa az összes esetleges szabályozási hiányosságok elhárítását B és az összes Bs A. A szkript végrehajtásához másolja a módosított szkripttel, illessze be a PowerShell és nyomja le az `Enter`.
7. Jelentkezzen ki a Felhasználób az Azure-ból, és jelentkezzen be az "a" felhasználó.
8. Hozzon létre a társviszonyt myVnetA myVnetB. Másolja a következő parancsfájl egy szövegszerkesztőbe, a számítógépen. Cserélje le `<SubscriptionB-Id>` a b előfizetés azonosítója A szkript végrehajtásához másolja a módosított szkripttel, illessze be a PowerShell és nyomja le az `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. MyVnetA társviszony állapotának megtekintéséhez.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **kezdeményezve**. Állapotúra változik **csatlakoztatva** a társviszonyt a myVnetA myVnetB beállítása után.

10. Jelentkezzen ki az Azure-ból Felhasználóa és Felhasználób található.
11. Hozzon létre a társviszonyt myVnetB myVnetA. A parancsfájl tartalmának másolása a 8. lépés a számítógépen egy szövegszerkesztőbe. Cserélje le `<SubscriptionB-Id>` azonosítójú előfizetés A és a módosítás minden esetleges szabályozási hiányosságok elhárítását B és az összes Bs irányát, A szkript végrehajtásához másolja a módosított szkripttel, illessze be a powershellt, majd nyomja le `Enter`.
12. MyVnetB társviszony állapotának megtekintéséhez. A parancsfájl tartalmának másolása a 9. lépés a számítógépen egy szövegszerkesztőbe. Módosítsa A B, az erőforráscsoportot és a virtuális hálózat nevét. A szkript végrehajtásához PowerShell illessze be a módosított szkriptet, és nyomja le az `Enter`. Az állapot **csatlakoztatva**. A társviszony-létesítés állapota **myVnetA** vált **csatlakoztatva** a társviszony létrehozását követően **myVnetB** való **myVnetA**. "A" felhasználó bejelentkezhet térjen vissza az Azure-ba, és teljes körű 9. lépés a társviszony-létesítés állapota myVnetA megerősítése érdekében. 

    > [!NOTE]
    > A társviszony nem jön létre, amíg a társviszony-létesítés állapota **csatlakoztatva** mindkét virtuális hálózat számára.

    Bármelyik virtuális hálózatban hoz létre Azure-erőforrások immár képes az IP-címekkel kommunikálhatnak egymással. Ha Azure névfeloldási szolgáltatása alapértelmezett a virtuális hálózatok használata esetén a virtuális hálózatokban az erőforrások nem lesznek tudják feloldani a virtuális hálózatok között. Ha szeretné feloldani az egy társviszony-létesítés virtuális hálózatok között, létre kell hoznia a saját DNS-kiszolgáló. Ismerje meg, hogyan állítható be [névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
14. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések [törölje az erőforrást](#delete-powershell) ebben a cikkben.

## <a name="template"></a>-Társviszony létrehozása Resource Manager-sablon

Ha a virtuális hálózatok különböző előfizetésekben találhatóak, és az előfizetések társítva, a másik Azure Active Directory-bérlők, a folytatás előtt végezze el az alábbi lépéseket:
 - Adja hozzá a felhasználót, mint minden Active Directory-bérlőt egy [vendégfelhasználó](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) az ellentétes Azure Active Directory-bérlőben.
 - Minden felhasználó rendelkezik az ellentétes Active Directory-bérlő, a Vendég felhasználói meghívó elfogadását.
 
1. Virtuális hálózat létrehozása és hozzárendelése a megfelelő [engedélyek](virtual-network-manage-peering.md#permissions), hajtsa végre a a [portál](#portal), [Azure CLI-vel](#cli), vagy [PowerShell](#powershell)Ez a cikk szakaszainak.
2. Mentse fájlba a helyi számítógépen a következő szöveget. Cserélje le `<subscription ID>` az "a" felhasználó az előfizetés-azonosítójára. Előfordulhat, hogy a fájlt mentette vnetpeeringA.json, mint például.

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

3. Jelentkezzen be az Azure-bA Felhasználóa felhasználóként, és a sablon üzembe a [portál](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), vagy a [Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Adja meg a fájlnevet, 2. lépésben mentett a példa json-szövegben.
4. A példa json másolásához a 2. lépés egy fájlt a számítógépen, és módosítja a kezdődő sorokat:
    - **Név**: Változás *myVnetA/myVnetAToMyVnetB* való *myVnetB/myVnetBToMyVnetA*.
    - **ID**: Cserélje le `<subscription ID>` Felhasználób az előfizetés-azonosító, és módosítsa a *myVnetB* való *myVnetA*.
5. 3. lépés kész, bejelentkezésének az Azure-bA Felhasználób.
6. **Nem kötelező**: Bár ebben az oktatóanyagban a virtuális gépekre nem vonatkozik, egy virtuális gép létrehozása az egyes virtuális hálózatok, és csatlakozzon egy virtuális gépről a másikra, a kapcsolat ellenőrzéséhez.
7. **Nem kötelező**: Ebben az oktatóanyagban létrehozott erőforrások törléséhez hajtsa végre a lépések a [törölje az erőforrást](#delete) című szakaszát, az Azure portal, PowerShell vagy az Azure CLI használatával.

## <a name="delete"></a>Erőforrások törlése
Ez az oktatóanyag befejezése után érdemes törölni, így nem használati díjak merülhetnek fel az oktatóanyagban létrehozott erőforrásokat. Egy erőforráscsoport törlésekor a, amelyek az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="delete-portal"></a>Azure Portal

1. Jelentkezzen be az Azure Portalon Felhasználóa felhasználóként.
2. A portál keresőmezőbe írja be a **myResourceGroupA**. A keresési eredmények között, válassza ki a **myResourceGroupA**.
3. Válassza a **Törlés** elemet.
4. A törlés megerősítéséhez a a **írja be az ERŐFORRÁSCSOPORT nevét** mezőbe írja be **myResourceGroupA**, majd válassza ki **törlése**.
5. Jelentkezzen ki a portálra a Felhasználóa felhasználóként, és jelentkezzen be Felhasználób felhasználóként.
6. MyResourceGroupB 2 – 4 a lépéseket.

### <a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-bA Felhasználóa felhasználóként, és hajtsa végre a következő parancsot:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Jelentkezzen ki az Azure Felhasználóa felhasználóként, és jelentkezzen be Felhasználób felhasználóként.
3. Hajtsa végre a következő parancsot:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Jelentkezzen be az Azure-bA Felhasználóa felhasználóként, és hajtsa végre a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Jelentkezzen ki az Azure Felhasználóa felhasználóként, és jelentkezzen be Felhasználób felhasználóként.
3. Hajtsa végre a következő parancsot:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>További lépések

- Alaposan olvassa el a fontos [virtuális hálózati társviszony-létesítési korlátok és működési módokkal](virtual-network-manage-peering.md#requirements-and-constraints) társviszonyt az éles virtuális hálózat létrehozása előtt használja.
- További információ az összes [virtuális hálózati társviszony beállításról](virtual-network-manage-peering.md#create-a-peering).
- Ismerje meg, hogyan [eseményközpont létrehozásához és a hálózati topológia küllő](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a virtuális hálózatok közötti társviszony.
