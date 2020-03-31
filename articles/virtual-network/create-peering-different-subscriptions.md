---
title: Virtuális hálózat-társviszony-létesítés létrehozása – különböző előfizetések
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre virtuális hálózati társviszony-létesítést a különböző Azure-előfizetésekben létező Erőforrás-kezelőn keresztül létrehozott virtuális hálózatok között.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245121"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Virtuális hálózati társviszony-létesítés létrehozása - Erőforrás-kezelő, különböző előfizetések

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre virtuális hálózatot az Erőforrás-kezelőn keresztül létrehozott virtuális hálózatok között. A virtuális hálózatok különböző előfizetésekben léteznek. Két virtuális hálózat társviszony-létesítése lehetővé teszi, hogy a különböző virtuális hálózatok erőforrásai ugyanolyan sávszélességgel és késleltetéssel kommunikáljanak egymással, mintha az erőforrások ugyanabban a virtuális hálózatban lennének. További információ a [virtuális hálózati társviszony-létesítésről.](virtual-network-peering-overview.md)

A virtuális hálózati társviszony-létesítés létrehozásának lépései eltérőek, attól függően, hogy a virtuális hálózatok azonos vagy eltérő előfizetések, és hogy a virtuális hálózatok milyen [Azure-telepítési modellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül jönnek létre. Megtudhatja, hogy miként hozhat létre virtuális hálózati társviszony-létesítést más esetekben, ha az alábbi táblázatból kiválasztja a forgatókönyvet:

|Azure üzembehelyezési modell  | Azure-előfizetés  |
|--------- |---------|
|[Mindkét Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ugyanaz|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models.md) |Ugyanaz|
|[Egy Resource Manager, egy klasszikus](create-peering-different-deployment-models-subscriptions.md) |Különböző|

Virtuális hálózati társviszony-létesítés nem hozható létre a klasszikus üzembe helyezési modellen keresztül üzembe helyezett két virtuális hálózat között. Ha olyan virtuális hálózatokat kell csatlakoztatnia, amelyeket a klasszikus üzembe helyezési modellen keresztül hoztak létre, egy Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával csatlakoztathatja a virtuális hálózatokat.

Ez az oktatóanyag az ugyanabban a régióban lévő virtuális hálózatokat is lekell társának. A virtuális hálózatokat különböző [támogatott régiókban](virtual-network-manage-peering.md#cross-region)is társviszonyban lévő konálhatja. Javasoljuk, hogy a virtuális hálózatok társviszony-létesítése előtt ismerkedjen meg a [társviszony-létesítési követelményekkel és korlátozásokkal.](virtual-network-manage-peering.md#requirements-and-constraints)

Az Azure [Portal](#portal), az Azure [parancssori felület](#cli) (CLI), az Azure [PowerShell](#powershell)vagy az [Azure Resource Manager-sablon](#template) használatával virtuális hálózati társviszony-létesítést hozhat létre. Válassza ki az előző eszközhivatkozások bármelyikét, hogy közvetlenül a virtuális hálózati társviszony-létesítés nek a választott eszközzel történő létrehozásának lépéseihez lépjen.

Ha a virtuális hálózatok különböző előfizetésekben vannak, és az előfizetések különböző Azure Active Directory-bérlőkhöz vannak társítva, a folytatás előtt hajtsa végre a következő lépéseket:
1. Adja hozzá a felhasználót az egyes Active Directory-bérlők, mint egy [vendég felhasználó](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) a szemközti Azure Active Directory-bérlő.
1. Minden felhasználónak el kell fogadnia a vendég felhasználó meghívását a szemközti Azure Active Directory-bérlőtől.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Társviszony-létesítés létrehozása – Azure portal

A következő lépések különböző fiókokat használnak az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, ugyanazt a fiókot használhatja az összes lépéshez, kihagyhatja a portálból való kijelentkezés lépéseit, és kihagyhatja a lépéseket egy másik felhasználói engedély hozzárendeléséhez a virtuális hálózatokhoz.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) *Felhasználói felhasználóként.* A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
2. Válassza **a + Erőforrás létrehozása**lehetőséget, válassza a **Hálózat**lehetőséget, majd a Virtuális **hálózat**lehetőséget.
3. Jelölje be vagy írja be a következő példaértékeket a következő beállításokhoz, majd válassza a **Létrehozás**lehetőséget:
    - **Név**: *myVnetA*
    - **Címtér**: *10.0.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.0.0.0/24*
    - **Előfizetés**: Válassza ki az A előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupA értéket*
    - **Helyszín**: *USA keleti része*
4. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetA*kifejezést. Válassza ki **a myVnetA-t,** amikor megjelenik a keresési eredmények között. 
5. Válassza a **hozzáférés-vezérlés (IAM)** elemet a bal oldali beállításlistában.
6. A **MyVnetA - Hozzáférés-vezérlés (IAM)** csoportban válassza a **+ Szerepkör-hozzárendelés hozzáadása lehetőséget.**
7. Válassza a **Hálózati közreműködő** lehetőséget a **Szerepkör** mezőben.
8. A **Kijelölés** mezőben válassza a *Felhasználó B*lehetőséget, vagy írja be a Felhasználó e-mail címét a kereséshez.
9. Kattintson a **Mentés** gombra.
10. A **myVnetA - Hozzáférés-vezérlés (IAM)** csoportban válassza a **Tulajdonságok elemet** a bal oldali beállítások függőleges listájából. Másolja a **forrásazonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító hasonló a következő `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`példához: .
11. Jelentkezzen ki a portálról UserA néven, majd jelentkezzen be B felhasználóként.
12. A 2-3.

    - **Név**: *myVnetB*
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózat neve**: *alapértelmezett*
    - **Alhálózati címtartomány:** *10.1.0.0/24*
    - **Előfizetés**: Válassza a B előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** és írja be a *myResourceGroupB értéket*
    - **Helyszín**: *USA keleti része*

13. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetB kifejezést.* Válassza ki **a myVnetB-t,** amikor megjelenik a keresési eredmények között.
14. A **myVnetB csoportban**válassza a **Tulajdonságok elemet** a bal oldali beállítások függőleges listájában. Másolja a **forrásazonosítót**, amelyet egy későbbi lépésben használ. Az erőforrás-azonosító hasonló a következő `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`példához: .
15. Válassza a **hozzáférés-vezérlés (IAM)** lehetőséget **a myVnetB**csoportban, majd **UserA** hajtsa végre az 5-10.
16. Jelentkezzen ki a portálról B felhasználóként, és jelentkezzen be A felhasználóként.
17. A portál tetején található **Erőforrások keresése** mezőbe írja be a *myVnetA*kifejezést. Válassza ki **a myVnetA-t,** amikor megjelenik a keresési eredmények között.
18. Válassza a **myVnetA lehetőséget.**
19. A **BEÁLLÍTÁSOK csoportban**válassza **a Társviszony-létesítések**lehetőséget.
20. A **myVnetA - Társviszony-létesítések**csoportban válassza a **+ Hozzáadás lehetőséget**
21. A **Társviszony-létesítés hozzáadása**csoportban adja meg vagy jelölje be a következő beállításokat, majd kattintson az **OK gombra:**
     - **Név**: *myVnetAToMyVnetB*
     - **Virtuális hálózat telepítési modellje**: Válassza az **Erőforrás-kezelő**lehetőséget.
     - **Ismerem az erőforrás-azonosítómat:** Jelölje be ezt a jelölőnégyzetet.
     - **Erőforrás-azonosító:** Adja meg az erőforrás-azonosítót a 14.
     - **Virtuális hálózati hozzáférés engedélyezése:** Győződjön meg arról, hogy az **Engedélyezve** jelölőnégyzet be van jelölve.
    Az oktatóanyagban nincs más beállítás. Az összes társviszony-létesítési beállításról a [Virtuális hálózati társviszony-létesítések kezelése (Manage virtual networkings) (Virtuális hálózati társviszony-létesítések kezelése) (Virtuális hálózati társviszony-létesítések kezelése) (](virtual-network-manage-peering.md#create-a-peering)
22. A létrehozott társviszony-létesítés rövid várakozással jelenik meg, miután az előző lépésben az **OK** gombot választotta. **Az kezdeményezett** lista a létrehozott **myVnetAToMyVnetB** társviszony-létesítés **társviszony-létesítésének társviszony-létesítése társviszony-létesítése** oszlopában található. Már peered myVnetA a myVnetB, de most meg kell peer myVnetB a myVnetA. A társviszony-létesítést mindkét irányban létre kell hozni, hogy a virtuális hálózatok erőforrásai kommunikálhassanak egymással.
23. Jelentkezzen ki a portálról UserA néven, és jelentkezzen be "B" felhasználóként.
24. Hajtsa végre a myVnetB 17-21. A 21. *myVnetBToMyVnetA* *myVnetA* **Virtual network** **Resource ID**
25. Néhány másodperccel azután, hogy az **OK** gombra kattintva létre szeretné hozni a myVnetB társviszony-létesítését, az imént létrehozott **myVnetB** társviszony-létesítés a **Társviszony-létesítésállapota** oszlopban található. **PEERING STATUS**
26. Jelentkezzen ki a portálról B felhasználóként, és jelentkezzen be A felhasználóként.
27. Hajtsa végre a 17-19. A **myVnetAToVNetB** társviszony-létesítés **társviszony-létesítésének társviszony-létesítési állapota** is **csatlakoztatva**van. A társviszony-létesítés sikeresen létrejött, miután a társviszony-létesítésmindkét virtuális hálózat **társviszony-létesítése társviszony-létesítési** oszlopban a **Társviszony-LÉTESÍTÉS** oszlopban a Csatlakoztatva látható. A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
28. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
29. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-portal) végre a cikk Erőforrások törlése című szakaszának lépéseit.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Társviszony-létesítés létrehozása - Azure CLI

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, használhatja ugyanazt a fiókot az összes lépéshez, kihagyhatja az Azure-ból való kijelentkezés lépéseit, és eltávolíthatja a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájlsorokat. Cserélje UserA@azure.com UserB@azure.com le az alábbi parancsfájlok mindegyikét a UserA és A UserB használt felhasználónevekre. 

A következő parancsfájlok:

- Az Azure CLI 2.0.4-es vagy újabb verzióját igényli. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissítenie kell, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakört.
- Bash héjban dolgozik. Az Azure CLI-szkriptek Windows-ügyfeleken való futtatásának lehetőségeiről az [Azure CLI Windows rendszeren való telepítésével](/cli/azure/install-azure-cli-windows) foglalkozó témakörben talál további információt.

A CLI és a függőségek telepítése helyett használhatja az Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki a **Try it** gombot a következő parancsfájlban, amely meghívja a Cloud Shell, amellyel bejelentkezhet az Azure-fiókjába.

1. Nyisson meg egy CLI-munkamenetet, és `azure login` jelentkezzen be az Azure-ba UserA-ként a paranccsal. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
2. Másolja a következő parancsfájlt a számítógép `<SubscriptionA-Id>` szövegszerkesztőjéhez, cserélje le az SubscriptionA azonosítójára, másolja a módosított `Enter`parancsfájlt, illessze be a CLI-munkamenetbe, és nyomja meg a gombot. Ha nem ismeri az előfizetésazonosítóját, `az account show` írja be a parancsot. Az **azonosító** értéke a kimenetben az előfizetés-azonosító.

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

3. Jelentkezzen ki az Azure-ból `az logout` A felhasználóként a paranccsal, majd jelentkezzen be az Azure-ba B felhasználóként. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
4. Hozzon létre myVnetB. A 2. Cserélje `<SubscriptionA-Id>` le az SubscriptionB azonosítójára. Módosítsa a 10.0.0.0/16-ot 10.1.0.0/16-ra, módosítsa az összes As to B-t és az összes B-t A-ra. Másolja be a módosított parancsfájlt, illessze be a CLI munkamenetbe, és nyomja meg a gombot. `Enter`
5. Jelentkezzen ki az Azure-ból B felhasználóként, és jelentkezzen be az Azure-ba Felhasználói felhasználóként.
6. Hozzon létre egy virtuális hálózati társviszony-létesítés a myVnetA a myVnetB. Másolja a következő parancsfájltartalmat a számítógép szövegszerkesztőjéhez. Cserélje `<SubscriptionB-Id>` le az SubscriptionB azonosítójára. A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, és nyomja le az Enter billentyűt.

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

7. Tekintse meg a myVnetA társviszony-létesítési állapotát.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Az állapot **bevan indítva.** A myVnetB-ből a myVnetA-hoz való társviszony-létesítés létrehozása után **a Csatlakoztatott** ra változik.

8. Jelentkezzen ki az Azure-ból származó UserA-t, és jelentkezzen be az Azure-ba B felhasználóként.
9. Hozza létre a társviszony-létesítést a myVnetB-ről a myVnetA-ra. A 6. Cserélje `<SubscriptionB-Id>` le az SubscriptionA azonosítóját, és módosítsa az összes As to B-t és az összes B-t A-ra. Miután elkészítette a módosításokat, másolja a módosított parancsfájlt, illessze be a CLI-munkamenetbe, és nyomja meg a billentyűt. `Enter`
10. Tekintse meg a myVnetB társviszony-létesítési állapotát. A 7. Módosítsa az erőforráscsoport és a virtuális hálózat nevének A-t B-re, másolja a parancsfájlt, illessze be a módosított parancsfájlt a CLI-munkamenetbe, majd nyomja le a gombot. `Enter` A társviszony-létesítési állapot **csatlakoztatva**van. A társviszony-létesítési állapota myVnetA változások **Kapcsolat után** a myVnetB a myVnetA létrehozása után. Bejelentkezhet usera vissza az Azure-ba, és töltse ki a 7- es lépést újra a myVnetA társviszony-létesítési állapotának ellenőrzéséhez. 

    > [!NOTE]
    > A társviszony-létesítés nem jön létre, amíg a társviszony-létesítési állapot **csatlakoztatva** van mindkét virtuális hálózathoz.

11. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
12. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-cli) végre a cikk Erőforrások törlése című területén található lépéseket.

A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="create-peering---powershell"></a><a name="powershell"></a>Társviszony-létesítés létrehozása – PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag különböző fiókokat használ az egyes előfizetésekhez. Ha olyan fiókot használ, amely mindkét előfizetéshez rendelkezik, használhatja ugyanazt a fiókot az összes lépéshez, kihagyhatja az Azure-ból való kijelentkezés lépéseit, és eltávolíthatja a felhasználói szerepkör-hozzárendeléseket létrehozó parancsfájlsorokat. Cserélje UserA@azure.com UserB@azure.com le az alábbi parancsfájlok mindegyikét a UserA és A UserB használt felhasználónevekre.

1. Ellenőrizze, hogy rendelkezik-e az Azure PowerShell 1.0.0-s vagy újabb verziójával. Ehhez fut a `Get-Module -Name Az` Javasoljuk, hogy telepítse a legújabb verzióját a PowerShell Az [modul](/powershell/azure/install-az-ps). Ha először használja a PowerShellt, olvassa el az [Azure PowerShell áttekintését](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. PowerShell-munkamenet indítása.
3. A PowerShellben jelentkezzen be az Azure-ba UserA-ként a `Connect-AzAccount` parancs beírásával. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
4. Hozzon létre egy erőforráscsoportot és a virtuális hálózatot A. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<SubscriptionA-Id>` le a SubscriptionA azonosítójára. Ha nem ismeri az előfizetésazonosítóját, `Get-AzSubscription` írja be a megtekintéshez szükséges parancsot. Az **azonosító** értéke a visszaadott kimenetaz előfizetés-azonosító. A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze `Enter`be a PowerShellbe, majd nyomja le a billentyűt.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Jelentkezzen ki az Azure-ból a UserA-t, és jelentkezzen be a B felhasználóba. A fióknak, amelybe bejelentkezik, rendelkeznie kell a virtuális hálózati társviszony-létesítés létrehozásához szükséges engedélyekkel. Az engedélyek listáját a [Virtuális hálózati társviszony-létesítési engedélyek című témakörben található.](virtual-network-manage-peering.md#permissions)
6. A 4. Cserélje `<SubscriptionA-Id>` le a B előfizetés azonosítójára. Változtassa meg az összes "B-ig"-t és az összes B-t A-ra. A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze be a PowerShellbe, majd nyomja le a billentyűt. `Enter`
7. Jelentkezzen ki a B felhasználóból az Azure-ból, és jelentkezzen be az A felhasználóba.
8. Hozza létre a társviszony-létesítést a myVnetA-ból a myVnetB-be. Másolja a következő parancsfájlt a számítógép szövegszerkesztőjéhez. Cserélje `<SubscriptionB-Id>` le a B előfizetés azonosítójára. A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze `Enter`be a PowerShellbe, majd nyomja le a billentyűt.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Tekintse meg a myVnetA társviszony-létesítési állapotát.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Az állapot **bevan indítva.** A myVnetB-ből a myVnetA-hoz való társviszony-létesítés beállítása után **a Csatlakoztatott** beállításra változik.

10. Jelentkezzen ki az Azure-ból a UserA-t, és jelentkezzen be a B felhasználóba.
11. Hozza létre a társviszony-létesítést a myVnetB-ről a myVnetA-ra. A 8. Cserélje `<SubscriptionB-Id>` le az A előfizetés azonosítóját, és módosítsa az összes As to B-t és az összes B-t A-ra. A parancsfájl végrehajtásához másolja a módosított parancsfájlt, illessze `Enter`be a PowerShellbe, majd nyomja le a billentyűt.
12. Tekintse meg a myVnetB társviszony-létesítési állapotát. A 9. Módosítsa az Erőforráscsoport és a virtuális hálózat nevének A-t B-re. A parancsfájl végrehajtásához illessze be a módosított `Enter`parancsfájlt a PowerShellbe, majd nyomja le a billentyűt. Az állapot **Csatlakoztatva**van. A **myVnetA** társviszony-létesítési állapota **a Kapcsolatra** változik, miután létrehozta a társviszony-létesítést a **myVnetB-ről** a **myVnetA-ra.** Bejelentkezhet usera vissza az Azure-ba, és töltse ki a 9.

    > [!NOTE]
    > A társviszony-létesítés nem jön létre, amíg a társviszony-létesítési állapot **csatlakoztatva** van mindkét virtuális hálózathoz.

    A virtuális hálózatban létrehozott Azure-erőforrások most már képesek kommunikálni egymással az IP-címükön keresztül. Ha a virtuális hálózatok alapértelmezett Azure-névfeloldást használ, a virtuális hálózatok erőforrásai nem tudják feloldani a neveket a virtuális hálózatokon keresztül. Ha egy társviszony-létesítésben virtuális hálózatokon keresztül szeretné feloldani a neveket, létre kell hoznia saját DNS-kiszolgálóját. További információ a névfeloldás beállításáról [a saját DNS-kiszolgálóhasználatával.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

13. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
14. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete-powershell) végre a cikk Erőforrások törlése című területén található lépéseket.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Társviszony-létesítés létrehozása – Erőforrás-kezelő sablon

1. Virtuális hálózat létrehozásához és a megfelelő [engedélyek](virtual-network-manage-peering.md#permissions)hozzárendeléséhez hajtsa végre a [cikk portál](#portal), [Azure CLI](#cli)vagy [PowerShell](#powershell) szakaszaiban található lépéseket.
2. Mentse a következő szöveget a helyi számítógépen lévő fájlba. Cserélje `<subscription ID>` le a UserA előfizetés-azonosítójára. Előfordulhat, hogy például a fájlt vnetpeeringA.json néven menti.

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

3. Jelentkezzen be az Azure-ba UserA-ként, és telepítse a sablont a [portálon](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), a [PowerShellen](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)vagy az [Azure CLI-n](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)keresztül. Adja meg azt a fájlnevet, amelybe a json minta szövegét a 2.
4. Másolja a json példát a 2.
   - **név**: Módosítsa *a myVnetA/myVnetAToMyVnetB nevet* *myVnetB/myVnetBToMyVnetA*névre.
   - **id**: `<subscription ID>` Cserélje le a UserB előfizetés-azonosítóját, és változtassa *myVnetB* *myVnetA*.
5. Hajtsa végre újra a 3.
6. **Nem kötelező**: Bár a virtuális gépek létrehozása nem szerepel az oktatóanyagban, létrehozhat egy virtuális gépet minden virtuális hálózatban, és csatlakozhat az egyik virtuális gépről a másikra a kapcsolat érvényesítéséhez.
7. **Nem kötelező:** Az oktatóanyagban létrehozott erőforrások törléséhez [hajtsa](#delete) végre a cikk erőforrások törlése szakaszában leírt lépéseket az Azure Portal, a PowerShell vagy az Azure CLI használatával.

## <a name="delete-resources"></a><a name="delete"></a>Erőforrások törlése
Ha befejezte az oktatóanyagot, érdemes törölni az oktatóanyagban létrehozott erőforrásokat, hogy ne kelljen használati díjat fizetnie. Az erőforráscsoport törlése az erőforráscsoportban lévő összes erőforrást is törli.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portál

1. Jelentkezzen be az Azure Portalon UserA néven.
2. A portál keresőmezőjébe írja be a **myResourceGroupA**értéket. A keresési eredmények között válassza a **myResourceGroupA**lehetőséget.
3. Válassza a **Törlés** elemet.
4. A törlés megerősítéséhez írja **be az ERŐFORRÁSCSOPORT NEVE mezőbe** a **myResourceGroupA**értéket, majd kattintson a **Törlés gombra.**
5. Jelentkezzen ki a portálról UserA néven, és jelentkezzen be "B" felhasználóként.
6. A myResourceGroupB 2-4.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Jelentkezzen be az Azure-ba Felhasználói felhasználóként, és hajtsa végre a következő parancsot:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Jelentkezzen ki az Azure-ból A felhasználóként, és jelentkezzen be "B" felhasználóként.
3. Hajtsa végre a következő parancsot:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Jelentkezzen be az Azure-ba Felhasználói felhasználóként, és hajtsa végre a következő parancsot:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Jelentkezzen ki az Azure-ból A felhasználóként, és jelentkezzen be "B" felhasználóként.
3. Hajtsa végre a következő parancsot:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>További lépések

- Alaposan ismerkedjen meg a fontos [virtuális hálózati társviszony-létesítési korlátokkal és viselkedésekkel,](virtual-network-manage-peering.md#requirements-and-constraints) mielőtt éles környezetben létrehozna egy virtuális hálózati társviszony-létesítést.
- További információ a [virtuális hálózati társviszony-létesítési beállításokról.](virtual-network-manage-peering.md#create-a-peering)
- Ismerje meg, hogyan [hozhat létre központi és küllős hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) virtuális hálózati társviszony-létesítéssel.
